# dotEngine  iOS SDK 



*2017-05-07 更新*



## SDK使用


!> dotEngine iOS SDK 提供静态库,只需要将静态库和头文件导入项目就可以使用.

!> dotEngine iOS SDK 支持iOS8和iOS8+

!> dotEngine iOS SDK 暂时不支持bitcode

### dotEngine iOS SDK 需要如下的库:


- VideoToolbox.framework
- SystemConfiguration.framework
- OpenGLES.framework
- GLKit.framework
- CoreVideo.framework
- CoreTelephony.framework
- CoreMedia.framework
- CoreGraphics.framework
- AVFoundation.framework
- AudioToolbox.framework
- Foundation.framework
- UIKit.framework
- libcucore.tbd
- libc++.tbd


## SDK API  

sdk api 主要包含两部分, `DotEngine` 是dotEngine的主体操作类, `DotEngineDelegate`是回调类.



### DotEngine 



#### 创建DotEngine实例

`+(instancetype _Nonnull)sharedInstanceWithDelegate:(id<DotEngineDelegate>)delegate;`


> 该方法创建一个DotEngine的实例, delegate 为DotEngineDelegate



#### 获得创建的DotEngine实例

`+(instancetype _Nonnull)sharedInstance;`

> 该方法返回之前创建的DotEngine实例



#### 开启本地音视频

`-(void)startLocalMedia;`

> 该方法初始化本地的音视频, 可以用来在加入房间之前进行视频的预览, 此方法会产生 `didAddLocalView` 回调.
> 如果不调用此方法, 则该用户为订阅模式,只接收远程的视频.



### 关闭本地音视频

`-(void)stopLocalMedia;`

> 该方法结束本地的音视频, 此方法会产生 `didRemoveLocalView`回调
> 如果在加入房间之后调用该方法, 则会转为订阅模式.



### 加入房间

`-(void)joinRoomWithToken:(NSString*)token;`

> 加入一个房间, 房间和用户信息已经包含在token中, dotEngine的服务端会自动匹配.  
> 为防止token泄露,最好不要重复使用token. 此方法调用成功会产生`didJoined`回调  



### 离开房间

`-(void)leaveRoom;`

> 离开一个房间,  此方法并不会停止本地音视频, 如果要关闭本地视频预览, 在调用leaveRoom后调stopLocalMedia.

> 此方法会产生`didLeave`回调.



### 设置本地视频发送质量 

`-(void)setupVideoProfile:(DotEngineVideoProfile)profile;`

> dotEngine设置了多种视频传输质量来适配各种使用场景. 此方法可以设置本地视频的传输质量. 

> 此方法需要在开启本地音视频之前调用. DotEngineVideoProfile 默认为DotEngine_VideoProfile_240P.

- **DotEngineVideoProfile的种类为:**

```
typedef NS_ENUM(NSInteger, DotEngineVideoProfile)
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
    
};
```



### 设置音视频采集模式

`-(void)setCaptureMode:(DotEngineCaptureMode)captureMode;`

> 此方法为高级api,  设置音视频的采集模式. 使用场景为自定义音视频输入.

- **DotEngineCaptureMode 有三种模式:**

```
typedef NS_ENUM(NSInteger,DotEngineCaptureMode){
    DotEngine_Capture_Default,
    DotEngine_Capture_Custom_Video,
    DotEngine_Capture_Custom_Video_Audio

};
```

- **DotEngine_Capture_Default:**  为默认模式, 使用dotEngine内置的音视频采集api.

- **DotEngine_Capture_Custom_Video:**  为自定义视频模式, 使用场景为用户自行采集视频, 需要对采集的视频进行更多的处理, 比如添加滤镜, 增加美颜,视觉分析,然后再把处理过的视频数据送给dotEngine.

- **DotEngine_Capture_Custom_Video_Audio:** 为自定义音频和视频,  此模式暂时未开放.



### 切换前后摄像头

`-(void)switchCamera;`

> 切换前后摄像头, 默认是优先选择前置摄像头.



### 本地音频静音模式切换

`-(void)muteLocalAudio:(BOOL)muted;`

> `muted` 如果为true, 会停止发送本地音频, `muted` 如果为false, 会恢复发送本地音频


### 本地视频切换

`-(void)muteLocalVideo:(BOOL)muted;`

> `muted` 如果为true, 会停止发送本地视频, `muted` 如果为false, 会恢复发送本地视频



### 开启/关闭扬声器

`-(void)enableSpeakerphone:(BOOL)enable;`

> speekerphone 模式切换


### 开启音量回调

`-(void)enableAudioVolumeIndicate:(BOOL)enable;`

> 本地音量回调开关


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






### 发送本地视频

`-(void) sendSampleBuffer:(CMSampleBufferRef ) sampleBuffer;`

> 当DotEngineCaptureMode 为`DotEngine_Capture_Custom_Video`, `DotEngine_Capture_Custom_Video_Audio`时,需要输入外部的视频源. 


### 发送本地视频

`-(void) sendPixelBuffer:(CVPixelBufferRef _Nonnull)pixelBuffer rotation:(VideoRotation)rotation;`

> 同sendSampleBuffer, 只不过支持CVPixelBuffer输入



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

- **DotEngineStatus:**

```
typedef NS_ENUM(NSInteger, DotEngineStatus) {
    DotEngineStatusReady,
    DotEngineStatusConnected,
    DotEngineStatusDisConnected,
    DotEngineStatusError,
};

```

> 连接状态发生变化   


### 本地视频视图加入回调

`-(void)dotEngine:(DotEngine* _Nonnull) engine  didAddLocalView:(UIView* _Nonnull)view;`

> 用户在调用startLocalMedia后, 会回调此方法. 

> 如果view没有被添加到父视图中, 该视图中的视频不会被渲染.



### 本地视频视图移出回调

`-(void)dotEngine:(DotEngine* _Nonnull) engine didRemoveLocalView:(UIView*)view;`

> 用户在调用stopLocalMedia后, 会回调此方法.  dotEngine 不会做view的移出操作, 需要用户自己完成.




### 远程视频视图加入回调

`-(void)dotEngine:(DotEngine* _Nonnull) engine  didAddRemoteView:(UIView* _Nonnull)view withUser:(NSString*_Nonnull)userId;`

> 当远程用户的视频准备好的时候会调用此方法.

> 如果view没有被添加父视图中,该视图中的视频不会被渲染. 



### 远程视频视图移出回调

`-(void)dotEngine:(DotEngineKit*) engine didRemoveRemoteView:(UIView*)view withUser:(NSString*)userId;`

> 当远程用户的视频移出的时候会调用此方法.dotEngine 不会做view的移出操作, 需要用户自己完成.




### 本地用户静音/关闭静音回调

`-(void)dotEngine:(DotEngine * _Nonnull)engine didMutedLocalAudio:(BOOL)muted;`

> 当用户开启静音或者关闭静音时会产生此回调.  



### 本地用户关闭视频/打开视频回调

`-(void)dotEngine:(DotEngine * _Nonnull)engine didMutedLocalVideo:(BOOL)muted;`

> 当用户发送视频或者停止发送本地视频时会产生此回调.


### 远程用户静音/关闭静音回调

`-(void)dotEngine:(DotEngine * _Nonnull)engine didMutedRemoteAudio:(BOOL)muted userId:(NSString*)peerId;`

> 远程用户开启静音/或者关闭静音时会产生此回调




### 远程用户关闭/打开视频回调

`-(void)dotEngine:(DotEngine * _Nonnull)engine didMutedRemoteVideo:(BOOL)muted userId:(NSString*)peerId;`

> 远程用户开启静音/或者关闭静音时会产生此回调




### 用户音量回调 

`-(void)dotEngine:(DotEngine* _Nonnull)engine didGotAudioVolume:(float)volume  userId:(NSString* _Nonnull)userId;`

!> 需要在加入房间之后才有回调,  200ms回调一次 


### 用户bitrate回调

`-(void)dotEngine:(DotEngine* _Nonnull)engine didGotBitrateStat:(DotBitrateStat* _Nonnull)stat userId:(NSString* _Nonnull)userId;`

!> 需要在加入房间之后才有回调,  1000ms回调一次 


### 错误回调

`-(void)dotEngine:(DotEngineKit*) engine didOccurError:(int)errorCode;`

> 当dotEngine发生错误会产生此回调


















