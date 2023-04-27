# worker启动方式与启动参数
>支持四类参数：logLevel，logTags, minPort/maxPort，dtls证书和私钥

### 1. worker三种使用及其启动都可以带启动参数
- 方式1：跨进程启动：如果不借助libuv，windows平台管道很麻烦。
- 方式2：用动态库启动：用mediasoup_worker_run函数启动，dll或者so一起发布。每次发信令，都要调uv_async_send告诉worker来取信令。
- 方式3：用静态库启动：用mediasoup_worker_run函数启动，链接依赖多。同事uv_async_send告诉worker来取信令。

```
--logLevel=warn 
--logTags=info 
--logTags=ice 
--logTags=dtls 
--logTags=rtp 
--logTags=srtp 
--logTags=rtcp 
--logTags=rtx 
--logTags=bwe 
--logTags=score 
--logTags=simulcast 
--logTags=svc 
--logTags=sctp 
--rtcMinPort=40000 
--rtcMaxPort=49999
```

### 2. 存放在Settings.hpp/Settings.cpp
```
class Settings
{
public:
	struct LogTags
	{
		bool info{ false };
		bool ice{ false };
		bool dtls{ false };
		bool rtp{ false };
		bool srtp{ false };
		bool rtcp{ false };
		bool rtx{ false };
		bool bwe{ false };
		bool score{ false };
		bool simulcast{ false };
		bool svc{ false };
		bool sctp{ false };
		bool message{ false };
	};

public:
	// Struct holding the configuration.
	struct Configuration
	{
		LogLevel logLevel{ LogLevel::LOG_ERROR };
		struct LogTags logTags;
		uint16_t rtcMinPort{ 10000u };
		uint16_t rtcMaxPort{ 59999u };
		std::string dtlsCertificateFile;
		std::string dtlsPrivateKeyFile;
	};

public:
	thread_local static struct Configuration configuration;
...
}
```