
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