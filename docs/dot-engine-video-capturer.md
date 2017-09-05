# Video Capturer



## 使用自定义视频输入


使用场景:

- 把桌面当做一个视频源输入
- 把某个view挡住视频源输入 
- 对摄像头取得的数据进行二次处理 



## iOS Video Capturer使用 


```

DotStream* localStream = [[DotStream alloc] initWithAudio:true
                                              video:true
                                           delegate:self];
DotVideoCapturer*  videoCapturer = [[DotVideoCapturer alloc] init];

[localStream setVideoCaptuer:videoCapturer];


// some other logic  

// when got new frame  

[videoCapturer sendCVPixelBuffer:(CVPixelBufferRef _Nonnull)pixelBuffer rotation:(VideoRotation)rotation];

```


## Android Video Capturer使用 

```

customCapturer = new CustomCaptuer(this.getApplicationContext());

mDotEngine = DotEngine.builder()
        .setContext(this.getApplicationContext())
        .setDotEngineListener(dotEngineListener).build();


localStream = DotStream.builder(this.getApplicationContext())
                .setAudio(true)
                .setVideo(true)
                .setVideoProfile(DotEngineVideoProfileType.DotEngine_VideoProfile_240P)
                .setCapturer(customCapturer)
                .build();


// some other logic 
// when got new frame

customCapturer.onByteBufferFrame(final VideoFrameBuffer videoFrameBuffer, final int rotation)

or  

customCapturer.onTextureFrameCaptured(int width, int height, int oesTextureId, float[] transformMatrix,
                                int rotation)

```