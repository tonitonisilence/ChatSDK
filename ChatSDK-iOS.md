# DirectChatSDK
a sdk for chat

# 简介

DirectChatSDK是一个app端客服系统访客解决方案，既包含了客服聊天逻辑管理，也提供了聊天界面，开发者可方便的将客服功能集成到自己的 APP 中。

# 集成
### 一.添加DirectChatSDK.framework进你的项目
1、找到bluild文件夹下的DirectChatSDK.framework文件，把文件拖进项目目录中
2、找到项目中对应 TARGET -> General -> Linked Linked Frameworks and Libraries 添加 DirectChatSDK.framework

### 二.添加DirectChatSDK.bundle进你的项目
1、找到bluild文件夹下的DirectChatSDK.bundle文件，把文件拖进项目目录中,并添加引用

### 三.添加必要系统权限配置
添加下面到info.plist文件，如果项目已配置则跳过
```javascript
    <key>NSPhotoLibraryAddUsageDescription</key>
    <string>请允许加入到相册</string>
    <key>NSPhotoLibraryUsageDescription</key>
    <string>请允许访问相册</string>
    <key>NSCameraUsageDescription</key>
    <string>请允许拍照</string>
    <key>NSMicrophoneUsageDescription</key>
    <string>请允许访问麦克风</string>
```
### 四.自定义SDK配置信息
1、在AppDelegate的 - (BOOL)application:(UIApplication *)application didFinishLaunchingWithOptions:(NSDictionary *)launchOptions 方法中添加下面代码：
```c

    DCSConfig *config = [DCSConfig sharedConfig];
    [DCSDirectChat takeOff:config];
```
#### 具体可配置信息请看DCSConfig.h文件

### 五.在你需要用到的地方调用SDK提供的界面
1、帮助与反馈页面
```c
    DCSHelpFeedbackViewController *vc = [[DCSHelpFeedbackViewController alloc] init];
    [self.navigationController pushViewController:vc animated:YES];
```
2、在线客服
```c
    DCSChatViewController *vc = [[DCSChatViewController alloc] init];
    [self.navigationController pushViewController:vc animated:YES];
```

### 六.获取相关信息 
```c
    // 未读消息数
    DCSDirectChat.unReadMessageCount;
    // 最后一条消息时间
    DCSDirectChat.lastMessageDate;
    // 最后一条消息内容
    DCSDirectChat.lastMessageContent;
```
#### 具体请看DCSDirectChat.h文件
