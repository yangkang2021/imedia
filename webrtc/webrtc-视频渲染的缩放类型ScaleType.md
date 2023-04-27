# webrtc-视频渲染的缩放类型ScaleType
>webrtc视频渲染经常调整渲染方式，看看webrtc下怎么做：
> - 保持比例裁剪填充(SCALE_ASPECT_FILL，适合人头画面)，
> - 保持比例显示全部画面(SCALE_ASPECT_FIT，适合屏幕共享)

### 一. ios的opengl或者Metal
> 由用户调整RTCVideoRenderer在父视图中的显示区域bounds完成。
- opengl或者Metal的接口RTCVideoRenderer.h都回调视频的高宽变化
```
//RTCVideoRenderer.h
- (void)videoView:(RTC_OBJC_TYPE(RTCNSGLVideoView) *)videoView didChangeVideoSize:(NSSize)size
```
- 用户更加变化自己调整视频显示
```
//ARDVideoCallView.m
- (void)videoView:(id<RTC_OBJC_TYPE(RTCVideoRenderer)>)videoView didChangeVideoSize:(CGSize)size {
  if (videoView == _remoteVideoView) {
    _remoteVideoSize = size;
  }
  [self setNeedsLayout];
}
```
- 调整方法
```
- (void)layoutSubviews {
  CGRect bounds = self.bounds;
  if (_remoteVideoSize.width > 0 && _remoteVideoSize.height > 0) {
    // Aspect fill remote video into bounds.
    CGRect remoteVideoFrame =
        AVMakeRectWithAspectRatioInsideRect(_remoteVideoSize, bounds);
    CGFloat scale = 1;
    if (remoteVideoFrame.size.width > remoteVideoFrame.size.height) {
      // Scale by height.
      scale = bounds.size.height / remoteVideoFrame.size.height;
    } else {
      // Scale by width.
      scale = bounds.size.width / remoteVideoFrame.size.width;
    }
    remoteVideoFrame.size.height *= scale;
    remoteVideoFrame.size.width *= scale;
    _remoteVideoView.frame = remoteVideoFrame;
    _remoteVideoView.center =
        CGPointMake(CGRectGetMidX(bounds), CGRectGetMidY(bounds));
  } else {
    _remoteVideoView.frame = bounds;
  }
    
  .....
}
```

### 二. android的SurfaceViewRenderer
> - 通过onMeasure调整view的实现
> - 可以根据父容器自动调整view的大小，不用sdk用户在重新计算
> - 所以SurfaceViewRenderer要放到一个更大的父容器里面且高宽是wrap_content，才能实现保持比例显示全部画面SCALE_ASPECT_FIT

- SurfaceViewRenderer.java缩放控制参数
```
    renderer.setScalingType(RendererCommon.ScalingType.SCALE_ASPECT_FIT,RendererCommon.ScalingType.SCALE_ASPECT_FIT);
```

- RendererCommon.java核心类，用于控制和计算renderview的高宽
```
    public void setScalingType(
        ScalingType scalingTypeMatchOrientation, ScalingType scalingTypeMismatchOrientation) {
      this.visibleFractionMatchOrientation =
          convertScalingTypeToVisibleFraction(scalingTypeMatchOrientation);
      this.visibleFractionMismatchOrientation =
          convertScalingTypeToVisibleFraction(scalingTypeMismatchOrientation);
    }

  private static float convertScalingTypeToVisibleFraction(ScalingType scalingType) {
    switch (scalingType) {
      case SCALE_ASPECT_FIT:
        return 1.0f;
      case SCALE_ASPECT_FILL:
        return 0.0f;
      case SCALE_ASPECT_BALANCED:
        return BALANCED_VISIBLE_FRACTION;
      default:
        throw new IllegalArgumentException();
    }

  public static Point getDisplaySize(
      float minVisibleFraction, float videoAspectRatio, int maxDisplayWidth, int maxDisplayHeight) {
    // If there is no constraint on the amount of cropping, fill the allowed display area.
    if (minVisibleFraction == 0 || videoAspectRatio == 0) {
      return new Point(maxDisplayWidth, maxDisplayHeight);
    }
    // Each dimension is constrained on max display size and how much we are allowed to crop.
    final int width = Math.min(
        maxDisplayWidth, Math.round(maxDisplayHeight / minVisibleFraction * videoAspectRatio));
    final int height = Math.min(
        maxDisplayHeight, Math.round(maxDisplayWidth / minVisibleFraction / videoAspectRatio));
    return new Point(width, height);
  }

//videoLayoutMeasure.measure
public Point measure(int widthSpec, int heightSpec, int frameWidth, int frameHeight) {
      // Calculate max allowed layout size.
      final int maxWidth = View.getDefaultSize(Integer.MAX_VALUE, widthSpec);
      final int maxHeight = View.getDefaultSize(Integer.MAX_VALUE, heightSpec);
      if (frameWidth == 0 || frameHeight == 0 || maxWidth == 0 || maxHeight == 0) {
        return new Point(maxWidth, maxHeight);
      }
      // Calculate desired display size based on scaling type, video aspect ratio,
      // and maximum layout size.
      final float frameAspect = frameWidth / (float) frameHeight;
      final float displayAspect = maxWidth / (float) maxHeight;
      final float visibleFraction = (frameAspect > 1.0f) == (displayAspect > 1.0f)
          ? visibleFractionMatchOrientation
          : visibleFractionMismatchOrientation;
      final Point layoutSize = getDisplaySize(visibleFraction, frameAspect, maxWidth, maxHeight);

      // If the measure specification is forcing a specific size - yield.
      if (View.MeasureSpec.getMode(widthSpec) == View.MeasureSpec.EXACTLY) {
        layoutSize.x = maxWidth;
      }
      if (View.MeasureSpec.getMode(heightSpec) == View.MeasureSpec.EXACTLY) {
        layoutSize.y = maxHeight;
      }
      return layoutSize;
    }
```

- SurfaceViewRenderer.java在onMeasure计算自己的高宽，updateSurfaceSize更新显示区域
```
  // View layout interface.
  @Override
  protected void onMeasure(int widthSpec, int heightSpec) {
    ThreadUtils.checkIsOnMainThread();
    Point size =
        videoLayoutMeasure.measure(widthSpec, heightSpec, rotatedFrameWidth, rotatedFrameHeight);
    setMeasuredDimension(size.x, size.y);
    logD("onMeasure(). New size: " + size.x + "x" + size.y);
  }

  @Override
  protected void onLayout(boolean changed, int left, int top, int right, int bottom) {
    ThreadUtils.checkIsOnMainThread();
    eglRenderer.setLayoutAspectRatio((right - left) / (float) (bottom - top));
    updateSurfaceSize();
  }

private void updateSurfaceSize() {
    ThreadUtils.checkIsOnMainThread();
    if (enableFixedSize && rotatedFrameWidth != 0 && rotatedFrameHeight != 0 && getWidth() != 0
        && getHeight() != 0) {
      final float layoutAspectRatio = getWidth() / (float) getHeight();
      final float frameAspectRatio = rotatedFrameWidth / (float) rotatedFrameHeight;
      final int drawnFrameWidth;
      final int drawnFrameHeight;
      if (frameAspectRatio > layoutAspectRatio) {
        drawnFrameWidth = (int) (rotatedFrameHeight * layoutAspectRatio);
        drawnFrameHeight = rotatedFrameHeight;
      } else {
        drawnFrameWidth = rotatedFrameWidth;
        drawnFrameHeight = (int) (rotatedFrameWidth / layoutAspectRatio);
      }
      // Aspect ratio of the drawn frame and the view is the same.
      final int width = Math.min(getWidth(), drawnFrameWidth);
      final int height = Math.min(getHeight(), drawnFrameHeight);
      logD("updateSurfaceSize. Layout size: " + getWidth() + "x" + getHeight() + ", frame size: "
          + rotatedFrameWidth + "x" + rotatedFrameHeight + ", requested surface size: " + width
          + "x" + height + ", old surface size: " + surfaceWidth + "x" + surfaceHeight);
      if (width != surfaceWidth || height != surfaceHeight) {
        surfaceWidth = width;
        surfaceHeight = height;
        getHolder().setFixedSize(width, height);
      }
    } else {
      surfaceWidth = surfaceHeight = 0;
      getHolder().setSizeFromLayout();
    }
  }
```
### 三. GDI
- 见examples\peerconnection\client\main_wnd.cc的MainWnd::OnPaint
### 四. PC平台的opengl和d3d用矩阵--略。