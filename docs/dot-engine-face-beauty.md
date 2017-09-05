# Face Beauty 


## Face Beauty Filter


> dotEngine 内置了美颜滤镜
> 美颜滤镜可以随时开启和关闭 
> 美颜滤镜会耗费很多计算资源,会让你app的续航大大降低



## Face Beauty 使用 


iOS
```
_localStream = [[DotStream alloc] initWithAudio:true
                                            video:true
                                        delegate:self];

[_localStream setupVideoProfile:DotEngine_VideoProfile_360P];


_localStream.useFaceBeauty = true;

_localStream.beautyLevel = 0.7f;

_localStream.brightLevel = 0.6f;

[_localStream setupLocalMedia];

```


Android
```

mDotEngine = DotEngine.builder()
        .setContext(this.getApplicationContext())
        .setDotEngineListener(dotEngineListener).build();


localStream = DotStream.builder(this.getApplicationContext())
                .setAudio(true)
                .setVideo(true)
                .setVideoProfile(DotEngineVideoProfileType.DotEngine_VideoProfile_240P)
                .build();


localStream.enableFaceBeauty(true);

localStream.setBrightLevel(0.4f);

```

