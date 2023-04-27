### WebRTC底层c++开发小技巧

## 基础

1. 日志：RTC_LOG与RTC_DLOG
2. 检查：RTC_CHECK与RTC_DCHECK
3. 线程检查：
    - RTC_RUN_ON：成员函数
    - RTC_GUARDED_BY：成员变量
    - RTC_DCHECK_RUN_ON：在函数体内
4. 线程安全的接口设计：api proxy。用宏包装一个线程来调用内部函数。

##### 时间
1. rtc::TimeMillis():返回单调递增的相对时间。
2. rtc::TimeUTCMillis:返回utc时间，从1970年1月1日0点到现在的时间。
3. webrtc::Clock: 返回utc时间和ntp时间。，从1900年1月1日0点到现在的时间。


## 线程：TaskQueue与rtc::Thread
2. Invoke/Send：如果在当前线程就直接执行，如果不是就同步等待。
2. Post/PostDelayed/PostAt：异步发送消息
5. PostTask/PostDelayedTask：异步执行函数
7. Clear：Post未执行要提前退出用clear。
8. 同步锁：webrtc::Mutex.

## 内存管理
大部分对象应该形成一棵对象树，父对象是子对象的所有者owner管理子对象析构。
1. 独占指针：std::unique_ptr<T>，大部对象都要用这个管理。
2. 共享指针：rtc::scoped_refptr<T>用rtc::RefCountedObject<T>可以方便的创建。
4. 弱引用指针：WeakPtrFactory<T>与WeakPtr<T>
5. 用delete禁用：构造，拷贝，=号赋值
    ```
    class T{
        T() = delete;
        T(const T&) = delete;
        T& operator=(const T&) = delete;
    };
    ```


## sdk和demo分析思路：
1. 基本框架：如webrtc 线程和TaskQueue库，Android和ios也有类似的。
1. 对象模型：对象树拉出出来展示展示。
2. 进程/线程模型：内部一个worker线程做同步。
3. IO模型：
3. 数据回调：同步回调，保证数据。
4. 断线重连：