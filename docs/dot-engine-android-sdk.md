# dotEngine Android SDK



*2017-06-30 12:48:46*



## SDK使用


!> dotEngine Android SDK 提供aar包,只需要将aar导入项目就可以使用.

!> dotEngine Android SDK 支持Android4.2及以上版本



### dotEngine Android SDK 需要如下的库的依赖:


- com.squareup.okhttp3: okhttp:3.5.0




## SDK API  


### VideoProfile

```
    DotEngine_VideoProfile_120P(0, 160, 120, 15, 80),
    DotEngine_VideoProfile_120P_2(1, 120, 160, 15, 80),
    DotEngine_VideoProfile_120P_3(2, 120, 120, 15, 60),
    DotEngine_VideoProfile_180P(10, 320, 180, 15, 160),
    DotEngine_VideoProfile_180P_2(11, 180, 320, 15, 160),
    DotEngine_VideoProfile_180P_3(12, 180, 180, 15, 120),
    DotEngine_VideoProfile_240P(20, 320, 240, 15, 200),
    DotEngine_VideoProfile_240P_2(21, 240, 320, 15, 200),
    DotEngine_VideoProfile_240P_3(22, 240, 240, 15, 160),
    DotEngine_VideoProfile_360P(30, 640, 360, 15, 400),
    DotEngine_VideoProfile_360P_2(31, 360, 640, 15, 400),
    DotEngine_VideoProfile_360P_3(32, 360, 360, 15, 300),
    DotEngine_VideoProfile_480P(40, 640, 480, 15, 500),
    DotEngine_VideoProfile_480P_2(41, 480, 640, 15, 500),
    DotEngine_VideoProfile_480P_3(42, 480, 480, 15, 400),
    DotEngine_VideoProfile_480P_4(43, 640, 480, 30, 750),
    DotEngine_VideoProfile_480P_5(44, 480, 640, 30, 750),
    DotEngine_VideoProfile_480P_6(45, 480, 480, 30, 680),
    DotEngine_VideoProfile_480P_7(46, 640, 480, 15, 1000),
    DotEngine_VideoProfile_720P(50, 1280, 720, 15, 1000),
    DotEngine_VideoProfile_720P_2(51, 720, 1280, 15, 1000),
    DotEngine_VideoProfile_720P_3(52, 1280, 720, 30, 1700),
    DotEngine_VideoProfile_720P_4(53, 720, 1280, 30, 1700),
```



### DotEngine



#### 创建DotEngine实例

` DotEngine.builder().build()`


> 该方法创建一个DotEngine的实例

```
具体参数如下

DotEngine  dotEngine = DotEngine.builder()
        .setContext(Context context)
        .setDotEngineListener(DotEngineListener listener).build();

```



#### 添加本地stream


`public void addStream(final DotStream dotStream)`

> 添加本地stream, 此方法需要在加入房间成功之后回调.

> DotEngine 支持添加多个stream, 比如进行屏幕共享的时候,也共享摄像头



#### 移出本地stream

`public void removeStream(final DotStream dotStream)`

> 移出本地stream


#### 加入房间 

`public boolean joinRoom(final String dotToken) `

> 加入一个房间, 房间和用户信息已经包含在token中, dotEngine的服务端会自动匹配.
> 为防止token泄露,最好不要重复使用token. 此方法调用成功会产生 `onJoined` 回调


#### 离开房间 

`public void leaveRoom() {...}`


> 离开一个房间, 此方法并不会停止本地音视频, 如果要关闭本地视频预览, 在调用leaveRoom后调shutdownLocalMedia.
> 此方法会产生 `onLeave` 回调.




### 开启/关闭扬声器

`public void enableSpeakerphone(boolean enable) {...}`

> speekerphone 模式切换




### 获取测试token

`static public void generateTestToken(String appKey, String appSecret, String room, String user, TokenCallback tokenCallback) {...}`


> 获得测试token, 方面用户在测试的时候快速集成. 不建议在生产环境中使用, 生产环境中请使用后端sdk来获取token.


### onResume 返回前台

`public void onResume()`


### onPause 进入后台 

`public void onPause()`




## DotEngineListener

DotEngineListener 为一个listener.  处理DotEngine的回调.


```
public abstract class DotEngineListener {


    abstract public void onJoined(String userId);


    abstract public void onLeave(String userId);


    abstract public void onOccurError(DotEngineErrorType errorCode);


    abstract public void onWarning(DotEngineWarnType warnCode);


    abstract public void onAddRemoteStream(DotStream stream);

    abstract public void onRemoveRemoteStream(DotStream stream);


    abstract public void onStateChange(DotEngineStatus status);

    // 如果 return true  则直接断开  如果为false 会一直重试  retryCount 为重试的次数
    public boolean onConnectionLost(int retryCount){
        return true;
    };

    // got custome message
    public void onReceiveMessage(String text){};

}

```


### 用户加入房间回调

`public void onJoined(final String user) {...}`

> 当有用户加入房间时的回调,  包括远程用户和本地用户.



### 用户离开房间回调

`public void onLeave(String user) {...}`

> 当有用户离开房间时的回调, 包括远程用户和本地用户.


### 用户状态发生变化


`public void onStateChange(DotEngineStatus status);`


- **DotEngineStatus:**

```
public enum DotEngineStatus {
    DotEngineStatusConnecting,
    DotEngineStatusConnected,
    DotEngineStatusDisConnected
}

```

### 远程stream加入回调  

`public void onAddRemoteStream(DotStream stream)`

> 远程stream加入回调  



### 远层stream移出回调 

`public void onRemoveRemoteStream(DotStream stream);`

> 远程stream移出回调, dotEngine 不会做view的移出操作, 需要用户自己完成.



### 错误回调

`public void onOccurError(DotEngineErrorType errorType) {...}`

> 当dotEngine发生错误会产生此回调



## DotStream  

###  初始化本地stream 


```

DotStream stream = DotStream.builder(Context context)
            .setVideo(true)
            .setCapturer(DotCustomCapturer capturer)
            .setScreenCaptureIntent(data)
            .setVideoProfile()
            .setAudio(true).build()

```

> 设置本地视频发送质量, 此方法需要在addStream 前调用. DotEngineVideoProfile 默认为DotEngine_VideoProfile_240P.


### 开启本地音视频

`public void setupLocalMedia()`

> 开启本地媒体, 用来预览视频. 如果没有开启视频, dotEngine会在addStream时候自动开启


### 关闭本地音视频

`public void shutdownLocalMedia()`

> 关闭本地媒体, 结束会话的时候关闭媒体.


### 切换前后摄像头

`public boolean switchCamera()`


### 静音/结束静音   接受/停止接受远程音频

`public void muteAudio(boolean muted)`

> 此方法分为本地和远程. 如果是本地的stream, 则为静音/结束静音. 如果是远程的则为接受/停止接受远程音频


### 显示/不显示本地视频  接受/停止接受远程视频

`public void muteVideo(boolean muted)`

> 此方法分为本地和远程. 如果是本地的stream, 则为显示/不显示本地视频. 如果是远程的则为接受/停止接受远程视频.


## DotStream 其他属性和方法


- `public boolean isLocal()` 是否本地stream 

- `public boolean isHasAudio()` 是否包含音频

- `public boolean isHasVideo()` 是否包含视频 

- `public String getStreamId()` streamId, 全局唯一

- `public String getPeerId()` 

- `public DotView getView()` stream 对应的view

## DotStreamListener 

```

public interface DotStreamListener {


    void onCameraError(DotStream stream, String error);

    void onVideoMuted(DotStream stream, boolean muted);

    void onAudioMuted(DotStream stream, boolean muted);

    void onAudioLevel(DotStream stream, int audioLevel);

}

```

### Camera开启错误回调 

`void onCameraError(DotStream stream, String error);`

### 远程视频 开启/关闭回调

`void onVideoMuted(DotStream stream, boolean muted);`

### 远程音频  开启/关闭回调

`void onAudioMuted(DotStream stream, boolean muted);`

### 音量回调 

`void onAudioLevel(DotStream stream, int audioLevel);`

> audioLevel 的大小为 [-127,0], -127为静音, 0为最大. dotEngine 会自动过滤audioLevel < -50的, 

> 此回调 500ms 回调一次 


## DotView

> DotView 为DotEngine内部创建, 用户并不需要创建DotView实例


### DotView 属性列表


- `public void setMirror(boolean mirror)` 是否镜像该view 

- `public void  setScaleMode(DotVideoViewScaleMode scaleMode)` 设置scaleMode

- `public void setViewListener(DotViewListener listener)` 

> DotView 对应的listener 





## DotVideoViewScaleMode 

```
public enum DotVideoViewScaleMode {
    ScaleModeFit,
    ScaleModeFill
}
```

> DotVideoViewScaleMode 默认为 `ScaleModeFill`

> `ScaleModeFit` 表示根据view的大小完整显示视频内容, 进行等比例缩放或扩大

> `ScaleModeFill` 表示根据view的大小视频完全填充view, 视频会根据view的大小做一定裁剪


## DotView

```

public interface DotViewListener {

    void onFirstFrameRendered();

    void onFrameResolutionChanged(int videoWidth, int videoHeight, int rotation);

}

```


- `void onFirstFrameRendered();`

第一帧视频开始渲染的时候开始的回调


- `void onFrameResolutionChanged(int videoWidth, int videoHeight, int rotation);`

视频大小发生改变时产生的回调 


