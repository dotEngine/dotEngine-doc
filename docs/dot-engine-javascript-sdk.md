# dotEngine javascript sdk

*2017-05-31 更新*


## SDK使用


> dotEngine已经发布到npm,  npm install dotengine  --save



## SDK API 


### 本地视频质量

```
VideoProfile_180P:{width:320,height:180,fps:15,bitrate:160000},
VideoProfile_240P:{width:320,height:240,fps:15,bitrate:200000},
VideoProfile_360P:{width:640,height:360,fps:15,bitrate:400000},
VideoProfile_480P:{width:640,height:480,fps:15,bitrate:500000},
VideoProfile_720P:{width:1280,height:720,fps:15,bitrate:1000000}
```

> dotEngine 默认的video profile 为 `VideoProfile_240P`



### DotEngine state


```
DotEngine.CONNECTING = 'connecting';
DotEngine.CONNECTED = 'connected';
DotEngine.DISCONNECTED = 'disconnected';
```

> DotEngine 的几种状态


### DotStream


> DotStream 代表一个stream对象, 持有对音视频的基本操作
> DotStream 分为本地和远程, DotEngine 负责远程的stream的创建和销毁



```
let stream = DotStream({audio:bool,video:bool,peerId:peerId});
```

其中audio 和 video应该最少有一个


#### 属性列表 

- `streamId` stream的唯一标识, 如果没有视频流, 此值为未定义
- `peerId`   stream的用户id, 一个用户有可能多个stream
- `isLocal`  是否是本地stream
- `audioTrack`  音频track  
- `videoTrack`  视频track
- `mediastream`  此对象为Mediastream 
- `videoProfile`  VideoProfile  默认值为 VideoProfile_240P 



####  静音音频

`muteAudio(muted)`

>  muted为boolean类型, 如果是本地stream, 会停止/开启本地音频发送, 如果是远程stream,则会静音/开启远程音频


####  静音视频

`muteVideo(muted)`

>  muted为boolean类型, 如果是本地stream, 会停止/开启本地视频发送, 如果是远程stream,则会停止/开启远层视频


#### 改变videoProfile


```

stream.videoProfile = VideoProfile_360P;

```

> 设置videoProfile,  在加入房间之前, 只会改变videoProfile的值,在加入房间时起作用. 在加入房间以后之后设置,
> 会动态的调整视频质量以及码率.




### DotEngine 

> DotEngine 是主体类, 负责用户,房间的管理


#### 实例化DotEngine实例

```

let dotEngine = new DotEngine();

```

#### 添加本地stream

`addStream(stream)`

```

let dotEngine = new DotEngine();

let stream = new DotStream({video:true,audio:true});

dotEngine.addStream(stream);

dotEngine.on('addLocalStream', (stream) => {
    
    console.log('streamId ', stream.streamId);
    console.log('mediastream ', stream.mediastream);

});

```

> 此方法需要在加入房间之后调用 

> 如果添加成功, 会有`addLocalStream` 事件回调, 参数为DotStream实例


#### 移出本地stream

`removeStream(stream)`

```

let dotEngine = new DotEngine();

let stream = new DotStream({video:true,audio:true});

dotEngine.addStream(stream);

dotEngine.on('addLocalStream', (stream) => {
    
    console.log('streamId ', stream.streamId);
    console.log('mediastream ', stream.mediastream);

});

dotEngine.on('removeLocalStream', (stream) => {

    console.log('streamId ', stream.streamId);
    console.log('mediastream ', stream.mediastream);
});

// some other stuff

dotEngine.removeStream(stream);

```

> 如果移除成功,会有`removeLocalStream` 事件回调, 参数为DotStream实例 


#### 加入房间

`joinRoom(token)`

```

let token = 'xxxxx'; // token from server   

let dotEngine = new DotEngine();

let stream = new DotStream({video:true,audio:true});

dotEngine.addStream(stream);

dotEngine.on('state',(newState) => {
    console.log('state change ', newState);
});

```


> 如果加入成功, 会有`state` 事件回调, 参数为表示状态的字符串.


#### 离开房间


`leaveRoom()`


```
let token = 'xxxxx'; // token from server   

let dotEngine = new DotEngine();

let stream = new DotStream({video:true,audio:true});

dotEngine.addStream(stream);

dotEngine.on('state',(newState) => {
    console.log('state change ', newState);
});

// some other stuff 


dotEngine.leaveRoom();

```

> 如果退出成功, 会有`state` 事件回调, 参数为表示状态的字符串.



#### 生成测试token

`generateTestToken(appKey,appSecret,room,user,callback)`


```
let appkey = 'dotcc';
let appSecret = 'dotcc';
let room = 'room';
let user = 'user';

let dotEngine = new DotEngine();

dotEngine.generateTestToken(appkey,appSecret,room,user, (error,token) => {

    if(!error){
        console.error(error);
        return;
    }

    dotEngine.joinRoom(token);
});

```

!> 这个方法仅供测试开发时使用, 为防止暴漏appSecret,  请使用server sdk来生成token.




### Event 回调 


-  `addLocalStream`  本地stream加入成功 
-  `removeLocalStream` 本地stream移出成功
-  `addRemoteStream`  远程stream加入成功
-  `removeRemoteStream` 远程stream移出回调 
-  `state`  状态发生改变 
-  `peerRemoved`  有用户加入 
-  `peerConnected` 有用户离开 
-  `muteRemoteVideo` 远程用户视频mute回调  
-  `muteRemoteAudio` 远程用户音频mute回调
-  `audioLevel`   音频音量回调  

>  audioLevel 的值为[-127, 0], -127为静音  0 为最大. dotEngine 会自动过滤audioLevel小于-50

-  `errors`  错误回调 



```
dotEngine.on('addLocalStream', (stream) => {
    console.log('streamId ', stream.streamId); 
});

dotEngine.on('removeLocalStream', (stream) => {
    console.log('streamId ', stream.streamId); 
});

dotEngine.on('addRemoteStream', (stream) => {
    console.log('streamId ', stream.streamId); 
    console.log('peerId ', stream.peerId);
});

dotEngine.on('removeRemoteStream', (stream) => {
    console.log('streamId ', stream.streamId);
    console.log('peerId ', stream.peerId);
});

dotEngine.on('state', (newState) => {

    console.log('newState ', newState);
});


dotEngine.on('peerConnected', (peerId) => {

    console.log('peerId come', peerId);
});

dotEngine.on('peerRemoved', (peerId) => {

    console.log('peerId gone ', peerId);
});


dotEngine.on('muteRemoteVideo', (stream, muted) => {

    console.log('remote stream video muted ', stream.streamId, muted);
});


dotEngine.on('muteRemoteAudio', (stream, muted) => {

    console.log('remote stream video muted ', stream.streamId, muted);
});


dotEngine.on('audioLevel', (stream, audioLevel) => {

    console.log('audioLevel ', stream.streamId,  audioLevel);
});



``` 


