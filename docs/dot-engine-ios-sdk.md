# dotEngine  iOS SDK 



*2017-06-30 12:48:37*








## 安装方法 

### CocoaPods

1, 在Podfile 内加入 `pod 'libdotEngine', :git => 'https://github.com/dotEngine/dotEngine-ios-sdk.git'`
2, 使用 `pod install` 安装


### plist 设定

iOS9需要在 info.plist 申请音视频的权限

```
<key>NSMicrophoneUsageDescription</key>
<string>dotEngine need to access your microphone</string>
<key>NSCameraUsageDescription</key>
<string>dotEngine need to access your camera</string>

```


### 关闭bitcode

在 `Build Settings` 中的 `Enable Bitcode` 設定為 NO 



## SDK API  


### VideoProfile 

> VideoProfile 代表本地stream的视频质量

```

typedef NS_ENUM(NSInteger)
{
    
    DotEngine_VideoProfile_120P = 0,        // 160x120   15   80
    DotEngine_VideoProfile_120P_2 = 1,		// 120x160   15   80
    DotEngine_VideoProfile_120P_3 = 2,		// 120x120   15   60
    
    DotEngine_VideoProfile_180P = 10,		// 320x180   15   160
    DotEngine_VideoProfile_180P_2 = 11,		// 180x320   15   160
    DotEngine_VideoProfile_180P_3 = 12,		// 180x180   15   120
    
    DotEngine_VideoProfile_240P = 20,		// 320x240   15   200
    DotEngine_VideoProfile_240P_2 = 21,		// 240x320   15   200
    DotEngine_VideoProfile_240P_3 = 22,		// 240x240   15   160
    
    DotEngine_VideoProfile_360P = 30,		// 640x360   15   400
    DotEngine_VideoProfile_360P_2 = 31,		// 360x640   15   400
    DotEngine_VideoProfile_360P_3 = 32,		// 360x360   15   300
  
    DotEngine_VideoProfile_360P_4 = 33,     // 640x360   30   800
    DotEngine_VideoProfile_360P_5 = 34,     // 360x640   30   800
    DotEngine_VideoProfile_360P_6 = 35,     // 360x360   30   600
    
    DotEngine_VideoProfile_480P = 40,		// 640x480   15   500
    DotEngine_VideoProfile_480P_2 = 41,		// 480x640   15   500
    DotEngine_VideoProfile_480P_3 = 42,		// 480x480   15   400
    
    DotEngine_VideoProfile_480P_4 = 43,		// 640x480   30   750
    DotEngine_VideoProfile_480P_5 = 44,		// 480x640   30   750
    DotEngine_VideoProfile_480P_6 = 45,		// 480x480   30   680
    DotEngine_VideoProfile_480P_7 = 46,		// 640x480   15   1000
    
    DotEngine_VideoProfile_720P = 50,		// 1280x720  15   1000
    DotEngine_VideoProfile_720P_2 = 51,		// 720x1280  15   1000
    DotEngine_VideoProfile_720P_3 = 52,		// 1280x720  30   1700
    DotEngine_VideoProfile_720P_4 = 53,		// 720x1280  30   1700
    
} DotEngineVideoProfile;

```



### DotEngine 



#### 创建DotEngine实例

`+(instancetype _Nonnull)sharedInstanceWithDelegate:(id<DotEngineDelegate>)delegate;`


> 该方法创建一个DotEngine的实例, delegate 为DotEngineDelegate


#### 获得创建的DotEngine实例

`+(instancetype _Nonnull)sharedInstance;`

> 该方法返回之前创建的DotEngine实例



### 添加本地stream  

`-(void)addStream:(DotStream* _Nonnull)stream;`

> 添加本地stream, 此方法需要在加入房间成功之后回调.

> DotEngine 支持添加多个stream, 比如进行屏幕共享的时候,也共享摄像头

> 如果成功会产生 didAddLocalStream 回调 



### 移出本地stream 

`-(void)removeStream:(DotStream* _Nonnull)stream;`

> 移出本地stream 

> 如果成功会产生 didRemoveLocalStream 回调 



### 加入房间

`-(void)joinRoomWithToken:(NSString*)token;`

> 加入一个房间, 房间和用户信息已经包含在token中, dotEngine的服务端会自动匹配.  
> 为防止token泄露,最好不要重复使用token. 此方法调用成功会产生`didJoined`回调  



### 离开房间

`-(void)leaveRoom;`

> 离开一个房间,  此方法并不会停止本地音视频, 如果要关闭本地视频预览, 在调用leaveRoom后调stopLocalMedia.

> 此方法会产生`didLeave`回调.




### 开启/关闭扬声器

`-(void)enableSpeakerphone:(BOOL)enable;`

> speekerphone 模式切换


### 获取测试token

`-(void)generateTestTokenWithAppKey.......`

```
-(void)generateTestTokenWithAppKey:( NSString* _Nonnull )appkey
                         appsecret:(NSString* _Nonnull )appsecret
                              room:(NSString* _Nonnull )room
                            userId:(NSString* _Nonnull )userid
                         withBlock:(void (^_Nonnull)(NSString* token,NSError* error))tokenBlock;
```

> 获得测试token, 方面用户在测试的时候快速集成. 不建议在生产环境中使用, 生产环境中请使用后端sdk来获取token.


<br/>


## DotEngineDelegate

DotEngineDelegate 为一个delegate.  处理DotEngineKit的回调.



### 用户加入房间回调

`-(void)dotEngine:(DotEngine* _Nonnull) engine didJoined:(NSString* _Nonnull)userId;`

> 当有用户加入房间时的回调,  包括远程用户和本地用户.



### 用户离开房间回调

`-(void)dotEngine:(DotEngine* _Nonnull) engine didLeave:(NSString* _Nonnull)userId;`

> 当有用户离开房间时的回调, 包括远程用户和本地用户.


### 用户状态发生变化

`-(void)dotEngine:(DotEngine* _Nonnull) engine  stateChange:(DotEngineStatus)state;`

- **DotStatus:**

```
typedef NS_ENUM(NSInteger, DotStatus) {
    DotStatusConnecting,
    DotStatusConnected,
    DotStatusDisConnected,
};

```

> 连接状态发生变化   



### 本地stream加入回调 

`-(void)dotEngine:(DotEngine* _Nonnull) engine didAddLocalStream:(DotStream* _Nonnull)stream;`

> 本地stream加入回调, 可以在回调中添加预览.


### 本地stream移出回调

`-(void)dotEngine:(DotEngine* _Nonnull) engine didRemoveLocalStream:(DotStream* _Nonnull)stream;`


> 本地stream移出回调, 可以在回调中移出预览.



### 远程stream加入回调

`-(void)dotEngine:(DotEngine* _Nonnull) engine didAddRemoteStream:(DotStream* _Nonnull)stream;`

> 当远程用户的stream创建好的时候会调用此方法.




### 远程stream移出回调

`-(void)dotEngine:(DotEngine* _Nonnull) engine didRemoveRemoteStream:(DotStream* _Nonnull) stream;`

> 当远程用户的stream的时候会调用此方法.dotEngine 不会做view的移出操作, 需要用户自己完成.




### 错误回调

`-(void)dotEngine:(DotEngineKit*) engine didOccurError:(int)errorCode;`

> 当dotEngine发生错误会产生此回调




### DotStream 



#### 初始化本地stream

`-(instancetype)initWithAudio:(BOOL)audio video:(BOOL)video;`

> 创建本地DotStream, audio和video最少需要有一个,否则localStrem会建立不成功

#### 设置DotStream VideoProfile 

`-(void)setupVideoProfile:(DotEngineVideoProfile)profile;`

> dotEngine设置了多种视频传输质量来适配各种使用场景. 此方法可以设置本地视频的传输质量. 

>设置本地视频发送质量, 此方法需要在addStream 前调用. DotEngineVideoProfile 默认为DotEngine_VideoProfile_240P.



#### 开启本地视频预览 

`-(void)setupLocalMedia;`

> 开启本地媒体, 用来预览视频. 如果没有开启视频, dotEngine会在addStream时候自动开启



#### 关闭本地视频

`-(void)shutdownLocalMedia;`


> 关闭本地媒体, 结束会话的时候关闭媒体.


#### 切换前后摄像头 

`-(void)switchCamera;`


#### 静音/结束静音   接受/停止接受远程音频

`-(void)muteAudio:(BOOL)muted;`

> 此方法分为本地和远程. 如果是本地的stream, 则为静音/结束静音. 如果是远程的则为接受/停止接受远程音频


#### 显示/不显示本地视频  接受/停止接受远程视频

`-(void)muteVideo:(BOOL)muted;`

> 此方法分为本地和远程. 如果是本地的stream, 则为显示/不显示本地视频. 如果是远程的则为接受/停止接受远程视频.


### DotStream 属性列表 



- `@property (nonatomic,readonly) BOOL local;`  是否是本地stream

- `@property (nonatomic,readonly) BOOL audio;`  是否包含音频

- `@property (nonatomic,readonly) BOOL video;`  是否包含视频 

- `@property (nonatomic,readonly) NSString* streamId;` streamId, 全局唯一

- `@property (nonatomic,readonly) NSString* peerId;`  peerId

- `@property (nonatomic,readonly) DotView*  view;`   stream 对应的view

- `@property (nonatomic,weak) id<DotStreamDelegate> delegate;`  

> DotStream 对应的delegate,  可以通过  [stream setDelegate:xxxx] 来进行设置

- `@property (nonatomic,retain) id<DotVideoCapturer> _Nullable videoCapturer;`

> 设置videoCapturer, 自定义视频输入的时候用到, 如果设置此属性视频流将取至videoCapturer



### DotStreamDelegate

> DotStreamDelegate 为DotStream的回调protocol


```
protocol DotStreamDelegate <NSObject>

-(void)stream:(DotStream* _Nullable)stream  didMutedVideo:(BOOL)muted;

-(void)stream:(DotStream* _Nullable)stream  didMutedAudio:(BOOL)muted;

-(void)stream:(DotStream* _Nullable)stream  didGotAudioLevel:(int)audioLevel;

@end

```

#### 远程视频 开启/关闭回调

`-(void)stream:(DotStream* _Nullable)stream  didMutedVideo:(BOOL)muted;`

> 远程视频开启/关闭产生的回调 


#### 远程音频 开启/关闭回调 

`-(void)stream:(DotStream* _Nullable)stream  didMutedAudio:(BOOL)muted;`

> 远程音频 开启/关闭回调 


#### 音量回调

`-(void)stream:(DotStream* _Nullable)stream  didGotAudioLevel:(int)audioLevel;`

>  audioLevel 的大小为 [-127,0], -127为静音, 0为最大. dotEngine 会自动过滤audioLevel < -50的, 

>  此回调 500ms 回调一次 



### DotView 


> DotView 为DotEngine 内部创建, 用户并不需要创建DotView实例


#### DotView 属性列表

- `@property (nonatomic, readonly) CGSize videoSize;` videoSize 为接收到视频的大小


- `@property (nonatomic, weak)     id<DotViewDelegate>  dotViewDelegate;` 

> DotView 对应的delegate, 可以通过 [view setDotViewDelegate:XXXXX] 来进行设置

- `@property (nonatomic, assign)   DotVideoViewScaleMode scaleMode;`  设置scaleMode

- `@property (nonatomic, assign)   BOOL mirror;`   是否镜像该view




### DotVideoViewScaleMode

```
typedef NS_ENUM(NSUInteger, DotVideoViewScaleMode)
{
    
    DotVideoViewScaleModeFit = 0,
    DotVideoViewScaleModeFill = 1,
};
```

> DotVideoViewScaleMode 默认为 `DotVideoViewScaleModeFill`

> `DotVideoViewScaleModeFit`  表示根据view的大小完整显示视频内容, 进行等比例缩放或扩大, DotView会出现留白

> `DotVideoViewScaleModeFill` 表示根据view的大小视频完全填充view, 视频会根据view的大小做一定裁剪


### DotViewDelegate


```

@protocol DotViewDelegate <NSObject>

- (void)videoViewDidReceiveData:(DotView *)renderer withSize:(CGSize)dimensions;
- (void)videoView:(DotView *)renderer streamDimensionsDidChange:(CGSize)dimensions;

@end

``` 

- ` (void)videoViewDidReceiveData:(DotView *)renderer withSize:(CGSize)dimensions;`

第一帧视频开始渲染的时候开始的回调


- `- (void)videoView:(DotView *)renderer streamDimensionsDidChange:(CGSize)dimensions;`

视频大小发生改变时产生的回调 



### DotVideoCapturer


> 用户可以自定义视频的输入, 比如添加滤镜, 屏幕共享, 发送一个view. DotVideoCapturer 是一个protocol


具体可见 [Custom Capturer](dot-engine-custom-capturer.md)

















