# dotEngine Android SDK




*2017-05-31 更新*



## SDK使用


!> dotEngine Android SDK 提供aar包,只需要将aar导入项目就可以使用.

!> dotEngine Android SDK 支持Android4.2及以上版本



### dotEngine Android SDK 需要如下的库:


- com.squareup.okhttp3: okhttp:3.5.0


## SDK API  

sdk api 主要包含两部分, `DotEngine` 是dotEngine的主体操作类, `DotEngineListener`是回调类.


### VideoProfile

> VideoProfile 代表本地stream的视频质量


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

`private DotEngine(Context context, DotEngineListener listener) {...}`


> 该方法创建一个DotEngine的实例, listener 为DotEngineListener



#### 获得创建的DotEngine实例

`public static synchronized DotEngine instance(Context context, DotEngineListener listener) {...)`

> 该方法返回之前创建的DotEngine实例


### 添加本地stream  

`public void addStream(DotStream dotStream){...}`

> 添加本地stream, 此方法需要在加入房间成功之后回调.

> DotEngine 支持添加多个stream, 比如进行屏幕共享的时候,也共享摄像头

> 如果成功会产生 didAddLocalStream 回调



### 移出本地stream

`public void removeStream(DotStream dotStream){...}`

> 移出本地stream

> 如果成功会产生 didRemoveLocalStream 回调



### 加入房间

`    public boolean joinRoom(final String dotToken) {...}`

> 加入一个房间, 房间和用户信息已经包含在token中, dotEngine的服务端会自动匹配.  
> 为防止token泄露,最好不要重复使用token. 此方法调用成功会产生`onJoined`回调  



### 离开房间

`    public void leaveRoom() {...}`

> 离开一个房间,  此方法并不会停止本地音视频, 如果要关闭本地视频预览, 在调用leaveRoom后调stopLocalMedia.

> 此方法会产生`onLeave`回调.




### 开启/关闭扬声器

`public void enableSpeakerphone(boolean enable) {...}`

> speekerphone 模式切换



### 获取测试token

`public static void generateTestToken(String appKey, String appSecret, String room, String user, TokenCallback tokenCallback) {...}`

```
public GenerateTestToken(final String appKey, final String appSecret, final String room, final String user, final TokenCallback tokenCallback) {
    new AsyncTask<Void, Void, String>() {
        @Override
        protected String doInBackground(Void... ps) {
            Map<String, Object> params = new HashMap<>(4);
            params.put("appkey", appKey);
            params.put("app_secret", appSecret);
            params.put("room", room);
            params.put("user", user);

            String value;
            try {
                value = NetUtils.postValue(DotConfig.instance().getGenerateTokenHost(), params);
            } catch (Exception e) {
                e.printStackTrace();
                return null;
            }
            if (TextUtils.isEmpty(value)) {
                return null;
            }
            try {
                JSONObject response = new JSONObject(value);
                JSONObject dataObject = response.getJSONObject("d");
                if (!dataObject.has("token")) {
                    Logging.w(TAG, "not token  msg=" + value);
                    return null;
                }
                return dataObject.getString("token");
            } catch (JSONException e) {
                Logging.w(TAG, "get token  failure msg=" + value + "  exception=" + e);
                return null;
            }
        }

        @Override
        protected void onPostExecute(final String s) {
            Logging.d(TAG, s);
            if (tokenCallback == null) {
                return;
            }
            if (!TextUtils.isEmpty(s)) {
                tokenCallback.onSuccess(s);
            } else {
                tokenCallback.onFailure();
            }

        }
    }.execute();
}
```

> 获得测试token, 方面用户在测试的时候快速集成. 不建议在生产环境中使用, 生产环境中请使用后端sdk来获取token.

<br/>





## DotEngineListener

DotEngineListener 为一个listener.  处理DotEngine的回调.



### 用户加入房间回调

`public void onJoined(final String user) {...}`

> 当有用户加入房间时的回调,  包括远程用户和本地用户.



### 用户离开房间回调

`public void onLeave(String user) {...}`

> 当有用户离开房间时的回调, 包括远程用户和本地用户.


### 用户状态发生变化

`public void onStateChange(DotEngineStatus status){...}`

- **DotEngineStatus:**

```
public enum DotEngineStatus {
    DotEngineStatusNew,
    DotEngineStatusReady,
    DotEngineStatusConnected,
    DotEngineStatusDisConnected,
    DotEngineStatusError,
}};

```

> 连接状态发生变化   


### 本地视频视图加入回调

`public void onAddLocalStream(DotStream stream){...}`

> 在addStream之后, 会回调此方法.

> 如果stream中包含视频, dotEngine 会自动创建DotView, 可以通过stream.getDotView() 获得已经创建好的view


### 本地stream移出回调

`public void onRemoveLocalStream(DotStream stream) {...}`

> 用户在调用removeStream后, 会回调此方法.  dotEngine 不会做view的移出操作, 需要用户自己完成.



### 远程stream加入回调

`public void onAddRemoteStream(DotStream stream) {...}`

> 当远程用户的stream创建好的时候会调用此方法.


### 远程视频视图移出回调

`public void onRemoveRemoteStream(DotStream stream) {...}`

> 当远程用户的stream的时候会调用此方法.dotEngine 不会做view的移出操作, 需要用户自己完成.


### 错误回调

`public void onOccurError(DotEngineErrorType errorCode) {...}`

> 当dotEngine发生错误会产生此回调




### DotStream

#### 初始化本地stream

`DotStream.builder().setAudio(boolean).setVideo(boolean).build();`

> 创建本地DotStream, audio和video最少需要有一个,否则localStream会建立不成功


#### 设置DotStream VideoProfile

`public StreamBuilder setVideoProfile(DotEngineVideoProfileType profile){...}`

> dotEngine设置了多种视频传输质量来适配各种使用场景. 此方法可以设置本地视频的传输质量.

>设置本地视频发送质量, 此方法需要在addStream 前调用. DotEngineVideoProfile 默认为DotEngine_VideoProfile_240P.


#### 切换前后摄像头

`public void switchCamera() {...}`

> 切换前后摄像头, 默认是优先选择前置摄像头.



#### 静音/结束静音   接受/停止接受远程音频

`public void muteAudio(boolean muted) {...}`

> 此方法分为本地和远程. 如果是本地的stream, 则为静音/结束静音. 如果是远程的则为接受/停止接受远程音频


#### 显示/不显示本地视频  接受/停止接受远程视频

`public void muteVideo(boolean muted) {...}`

> 此方法分为本地和远程. 如果是本地的stream, 则为显示/不显示本地视频. 如果是远程的则为接受/停止接受远程视频.





### DotStream 属性列表



- `Class DotStreamParams: boolean local;`  是否是本地stream

- `Class DotStreamParams: boolean audio;`  是否包含音频

- `Class DotStreamParams: boolean video;`  是否包含视频

- `private String streamId;` streamId, 全局唯一

- `private String peerId;`  peerId

- `private DotView mDotView;`   stream 对应的view

- `protected DotStreamListener mListener;`  

> DotStream 对应的listener,  可以通过  [stream setDelegate:xxxx] 来进行设置

- `public void setStreamListener(DotStreamListener listener){...}`

> 设置videoCapturer, 自定义视频输入的时候用到, 如果设置此属性视频流将取至videoCapturer



### DotStreamListener

> DotStreamListener 为DotStream的回调protocol

```
public interface DotStreamListener {

    void onCameraError(DotStream stream, String error);

    void onVideoMuted(DotStream stream, boolean muted);

    void onAudioMuted(DotStream stream, boolean muted);

    void onAudioLevel(DotStream stream, int audioLevel);

}
```


#### 远程视频 开启/关闭回调

`void onVideoMuted(DotStream stream, boolean muted);`

> 远程视频开启/关闭产生的回调


#### 远程音频 开启/关闭回调

`void onAudioMuted(DotStream stream, boolean muted);`

> 远程音频 开启/关闭回调


#### 音量回调

`void onAudioLevel(DotStream stream, int audioLevel);`

>  audioLevel 的大小为 [-127,0], -127为静音, 0为最大. dotEngine 会自动过滤audioLevel < -50的,

>  此回调 500ms 回调一次



### DotView


> DotView 为DotEngine 内部创建, 用户并不需要创建DotView实例

#### DotView 属性列表

- `private int frameHeight; private int frameWidth;` videoSize 为接收到视频的大小

- `private static final ScalingType DEFAULT_SCALING_TYPE
            = ScalingType.SCALE_ASPECT_FILL;`  设置scaleMode

- `private boolean mirror;`   是否镜像该view



### DotVideoViewScaleMode

```
public static enum ScalingType { SCALE_ASPECT_FIT,
    SCALE_ASPECT_FILL}
```

> `SCALE_ASPECT_FIT`  表示根据view的大小完整显示视频内容, 进行等比例缩放或扩大

> `SCALE_ASPECT_FILL` 表示根据view的大小视频完全填充view, 视频会根据view的大小做一定裁剪



### DotVideoCapturer


> 用户可以自定义视频的输入, 比如添加滤镜, 屏幕共享, 发送一个view. DotVideoCapturer 是一个protocol


具体可见 [Custom Capturer](dot-engine-custom-capturer.md)
