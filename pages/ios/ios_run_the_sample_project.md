---
title: iOS Run The Sample Project
keywords: ios
sidebar: ios_sidebar
toc: true
permalink: ios_run_the_sample_project.html
folder: ios
---
# iOS SDK's Introduction and import

------------------------------------------------------------------------

## DEMO（ChatDemo-UI3.0 App） experience

Download link：[download page](http://www.easemob.com/download/im)

# iOS SDK 快速集成

在您阅读此文档时，我们假定您已经具备了基础的 iOS
应用开发经验，并能够理解相关基础概念，最新版本的SDK 只支持 **iOS9**
及以上 iOS 系统版本。

**注：**此文档对之前的文档进行了优化，结构更清晰，帮助您更快的集成环信
iOS SDK，且对UI进行了改版优化。如果您已集成环信 iOS SDK
，并习惯于之前文档的结构，希望再次了解其集成方式，可参考之前的[旧文档](/im/ios/sdk/import)。

`特殊提示：如果使用的是xcode11打包，那么需要先将环信SDK内的模拟器框架去掉，否则会报"IPA processing failed "的错误。`
（如何剔除SDK内的模拟器框架，见iOS SDK
快速集成中的\'集成动态库上传AppStore\'）

------------------------------------------------------------------------

## DEMO 体验

[ChatDemo-UI3.0 源码地址](https://github.com/HyphenateInc/Hyphenate-Demo-iOS.git)
-->**注：**更多Demo体验，请前往[下载页](https://www.pgyer.com/tsui)

## 注册并创建应用

注册环信开发者账号并创建后台应用

详细操作步骤见[开发者注册及管理后台](/im/quickstart/guide/experience#注册并创建应用)。

Appkey：一个 APP 的唯一标识

IM 用户：一个 appkey
下的唯一标识用户，用来登录环信服务器进行收发消息的用户。
可以在创建好的应用内注册两个 IM 用户(也可以称为环信 id)，例如：
账号：user1，密码：123 ； 账号：user2，密码：123，用来通过 SDK
登录环信服务器，收发消息测试。

在环信 Console 后台，点击创建好的应用 → IM 用户 → 创建 IM 用户

![](https://docs-im.easemob.com/_media/im/ios/sdk/创建用户.png)

建议创建两个 IM 用户，用于后面集成 SDK 之后聊天使用。例如登录 user1
，在初始化聊天页面时传入 user2 ，user1 给 user2 发消息测试。

![](https://docs-im.easemob.com/_media/im/ios/sdk/用户.png)

------------------------------------------------------------------------

## iOS SDK 介绍

环信 SDK 为用户开发 IM
相关的应用提供的一套完善的开发框架。包括以下几个部分：

![开发框架](/im/ios/sdk/image006.png){.align-center}

-   SDK_Core: 为核心的消息同步协议实现，完成与服务器之间的信息交换。

```{=html}
<!-- -->
```
-   SDK: 是基于核心协议实现的完整的 IM
    功能，实现了不同类型消息的收发、会话管理、群组、好友、聊天室等功能。

```{=html}
<!-- -->
```
-   UI：[见集成 UI](/im/ios/sdk/prepare#集成_ui)

用户可以基于我们提供的 Demo 实现自己的应用，也可以基于 SDK
开发自己应用。

SDK
采用模块化设计，每一模块的功能相对独立和完善，用户可以根据自己的需求选择使用下面的模块：

![模块化设计](/im/ios/sdk/image005.png){.align-center}

-   AgoraChatClient: 是 SDK
    的入口，主要完成登录、退出、连接管理等功能。也是获取其他模块的入口。

-   AgoraChatManager: 管理消息的收发，完成会话管理等功能。


-   AgoraContactManager: 负责好友的添加删除，黑名单的管理。


-   AgoraGroupManager: 负责群组的管理，创建、删除群组，管理群组成员等功能。


-   AgoraChatroomManager: 负责聊天室的管理。

注意：如果您是从 SDK2.x 升级到 3.0，可以参考[环信 SDK
2.x到3.0升级文档](/im/ios/sdk/upgradeguide)。

------------------------------------------------------------------------

## 集成 SDK

环信 SDK 支持**pod
方式导入**，**手动导入**两种方式任选其一即可，下面分别介绍两种导入方式。

`从3.6.0版本开始，sdk只支持ios9.0及以上版本。`

### Pod 导入SDK

推荐使用 `Cocoapods` 集成环信 SDK。 Cocoapods
提供了一个简单的依赖管理系统，避免手动导入产生的错误（首先需要确认已经安装了
Cocoapods，如果没有安装过Cocoapods，参考安装使用指南：<https://www.cnblogs.com/wangluochong/p/5567082.html>）。

    sudo gem install cocoapods
    pod setup

在 `Xcode` 项目的根目录下，新建一个空文件，命名为
`Podfile`，向此文件添加以下行：

    pod 'AgoraChat'

在 `Podfile` 目录下，执行以下指令：

    pod install --repo-update 

执行完pod install，打开工程目录，找到.xcworkspace文件运行即可。

------------------------------------------------------------------------

### 手动导入 SDK

[下载环信demo](http://www.easemob.com/download/im)

-   AgoraChat 开发使用SDK

开发者最开始集成，如果选择手动导入文件集成的方式，只需要向工程中添`AgoraChat.framework`就可以，下面会介绍具体的集成方式。

demo 中的SDK文件夹为AgoraChat SDK，将SDK
文件夹拖入到工程中，并勾选截图中标注的三项。

![](https://docs-im.easemob.com/_media/im/ios/sdk/选项.png)

#### 设置工程属性

Xcode中，向 General → Embedded Binaries 中添加依赖库。

**注意要将\'Do Not Embed\'改成\'Embed & Sign\'**
![](./images/embeded_framework.png)

------------------------------------------------------------------------

### Demo 目录介绍

目录 ChatDemo-UI3.0 --->Class 中的 Demo 目录介绍

![](/im/ios/sdk/2e702406-82da-4f6b-9ae7-a2c42850f39b.png){width="600"}

-   Account：主要是 demo 的注册，登录


-   AppDelegate：主要是 demo 中初始化环信SDK，注册推送等

-   Communicate：demo 的语音视频通话功能页面（包含 1v1
    实时通话以及多人实时通话的功能）


-   Chat：demo 的聊天功能页面


-   Contact：demo 的好友功能页面

-   Conversation：demo 的会话列表功能页面


-   ChatDemo-UI3.0Helper：demo
    的单例类，主要是全局监听接收消息，好友，群组，聊天室等相关事件的回调，从而进行对应的处理

-   Group：demo 的群组功能页面


-   Helper：demo 的功能性文件，全局通用的配置


-   Home：demo 的根控制器页面


-   Notification：demo 的好友，群组相关请求通知的页面


-   Settings：demo 的功能设置页面

------------------------------------------------------------------------

## 集成 UI

环信的 UI 模块在 demo 中的该路径下 ChatDemo-UI3.0\-\--Class

demo 中有几大 UI 功能模块，在集成时将对应的模块添加到工程中即可。

-   Helper\-\-\-\-\--自定义库和页面，第三方库，全局通用模块


-   Chat\-\-\-\-\--聊天模块

-   Conversation\-\-\-\-\--会话列表模块


-   Communicate\-\-\-\-\--实时音视频模块（包含 1v1
    实时通话以及多人实时通话的功能）

-   Contact\-\-\-\-\--好友列表模块


-   Group\-\-\-\-\--群组模块


-   Chatroom\-\-\-\-\--聊天室模块

在集成时，必须要先向自己的工程中导入 `Helper`
模块，然后在根据自己的需求导入其他模块。

环信的 UI 模块依赖于以下三方库：

-   MWPhotoBrowser

-   Masonry


-   MJRefresh


-   MBProgressHUD


-   SDWebImage


-   WHToast




保证这些三方库在自己的工程中存在。

**注意：**三方推荐使用 pod 方式导入，手动导入需要修改 `info.plist`
重复等报错。

------------------------------------------------------------------------

## 增加隐私权限

在工程`info.plist`文件中增加隐私权限

用于 Chat
聊天模块中发送图片，语音，视频，位置消息使用，如您的工程中已经添加过请忽略：

-   Privacy - Photo Library Usage Description 需要访问您的相册


-   Privacy - Microphone Usage Description 需要访问您的麦克风


-   Privacy - Camera Usage Description 需要访问您的摄像机


-   Privacy - Location Always Usage Description
    需要您的同意,才能在使用期间访问位置

-   Privacy - Location When In Use Usage Description
    需要您的同意,才能始终访问位置

------------------------------------------------------------------------

## 添加SDK以及UI头文件

建议在 `PCH` 文件中引入 SDK 以及 UI 的头文件。如果工程中没有 `pch`
文件，需要新建一个，并在 `Build Settings` 中设置 `Prefix Header` 为该
pch 文件，例如：`iOS/PrefixHeader.pch`。

在 pch 文件文件中添加如下代码：

    #ifdef __OBJC__
    #import <AgoraChat/AgoraChat.h>
    // UI 头文件
    #endif

如果自己工程中的 pch
文件还引入了其他的头文件，那么所有的头文件都需要放到。

    #ifdef __OBJC__

    // 存放 pch 文件中所有的头文件

    #endif 的内部

------------------------------------------------------------------------

## 初始化及登录

初始化 SDK 以及登录环信服务器

### 初始化 SDK

在工程的 `AppDelegate` 中的以下方法中，调用 SDK 对应方法。

    - (BOOL)application:(UIApplication *)application didFinishLaunchingWithOptions:(NSDictionary *)launchOptions
    {
        // appkey替换成自己在环信管理后台注册应用中的appkey
    AgoraOptions *options = [AgoraOptions optionsWithAppkey:@"appkey"];
    // apnsCertName是证书名称，可以先传nil，等后期配置apns推送时在传入证书名称
        options.apnsCertName = nil;
        [[AgoraChatClient sharedClient] initializeSDKWithOptions:options];
        return YES;
    }

### 登录环信服务器

建议使用异步登录方法，防止网络不好的情况下，出现卡UI主线程的情况出现。

    // 传入在应用（appkey）下注册的IM用户user1，密码123，用于登录环信服务器

    [[AgoraChatClient sharedClient] loginWithUsername:@"user1" password:@"123" completion:^(NSString *aUsername, AgoraError *aError) {
        if (!aError) {
            NSLog(@"登录成功");
        } else {
            NSLog(@"登录失败的原因---%@", aError.errorDescription);
        }
    }];

-   如果在集成调试阶段，可以在初始化环信 SDK 完成之后，就调用登录方法。

```{=html}
- (AgoraError *)loginWithUsername:(NSString *)aUsername
                         token:(NSString *)aToken;

```
-   如果项目上线，建议开发者在登录自己服务器成功之后，再调用环信 SDK
    登录方法使用用户绑定的环信id登录环信服务器（开发者给自己用户在自己服务器创建账号的同时，调用环信的
    rest 接口在给用户授权注册一个环信 id，一起返回给 app 端，app
    端拿到用户的账号密码以及环信 id
    密码分别登录自己的服务器以及环信服务器）。

------------------------------------------------------------------------

## 初始化聊天页面

向工程中导入 `Chat` 文件

    // ConversationId接收消息方的环信ID:@"user2"
    // type聊天类型:AgoraConversationTypeChat    单聊类型
    // createIfNotExist 如果会话不存在是否创建会话：YES
    AgoraChatViewController *chatViewController = [[AgoraChatViewController alloc] initWithConversationId:conversationChatter conversationType:[self conversationTypeFromMessageType:messageType]];
    [self.navigationController  pushViewController:chatViewController animated:NO];

    [self.navigationController pushViewController:chatController animated:YES];

有导航的话，可以用 `push` 方式跳转到聊天页面发消息测试，也就是用登录的
user1 给 user2 发消息，没有导航的话，可以用 `present`
方式跳转到聊天页面。

------------------------------------------------------------------------


## 集成其他模块

集成这些模块，涉及到一些回调方法的监听与页面的跳转，需要在初始化环信SDK之后，添加
\[AgoraChatDemoHelper shareHelper\];

### 会话列表

向工程中导入 `Conversation` 文件

头文件：`#import "AgoraChatsViewController.h"`

初始化页面跳转(导航跳转示例)：

     AgoraChatsViewController  *chatsVC = [[AgoraChatsViewController alloc] init];

    [self.navigationController pushViewController: chatsVC animated:YES];

### 好友列表

向工程中导入 `Contact` 文件

头文件：`#import "AgoraContactsViewController.h"`

初始化页面跳转(导航跳转示例)：

     AgoraContactsViewController   *contactsVC = [[AgoraContactsViewController alloc] init];

    [self.navigationController pushViewController: contactsVC animated:YES];

### 群组

向工程中导入 `Group` 文件

头文件：`#import "AgoraGroupsViewController.h"`

初始化页面跳转(导航跳转示例)：

    AgoraGroupsViewController *groupVC= [[AgoraGroupsViewController alloc] init];
    [self.navigationController pushViewController:groupVC animated:YES];

### 聊天室

向工程中导入 `Chatroom` 文件

头文件：`#import "AgoraChatroomsViewController.h"`

初始化页面跳转(导航跳转示例)：

    AgoraChatroomsViewController *chatRoomVC= [[AgoraChatroomsViewController alloc] init];
    [self.navigationController pushViewController:chatRoomVC animated:YES];

------------------------------------------------------------------------

## 集成动态库上传AppStore

从3.7.4版本SDK开始支持bitcode打包，并且不再支持armv7,i386指令集，打包时需去除armv7指令。\
Xcode11需在Build Settings - Valid Architectures 设置项去除armv7指令；\
Xcode12需在Build Settings - Excluded Architectures
设置项添加armv7指令，若是iOS11以上无需此操作；

由于 iOS 编译的特殊性，为了方便开发者使用，我们将`x86_64`,`arm64`
两个平台都合并到了一起，所以使用动态库上传 appstore 时需要将`x86_64`
平台删除后，才能正常提交审核。\
首先将SDK进行备份。因为剔除过SDK的项目只能真机运行，要想继续模拟器运行，要换回未剔除的SDK。

然后进入到Launchpad中，找到其他\-\--打开终端，然后cd到SDK的所在目录。\
简单的方式就是找到项目中的环信SDK，然后终端先输入cd，然后按空格键，将环信SDK拖拽到终端内，会自动生成SDK的路径，然后环信SDK名称.framework删除掉，不要cd到环信SDK，只cd到SDK所在的目录下即可。\
示例：\
比如环信SDK的路径是\
/Users/easemob-dn0164/Desktop/iOS_IM_SDK_V3.6.0/HyphenateFullSDK/Hyphenate.framework\
那么只需要cd到\
/Users/easemob-dn0164/Desktop/iOS_IM_SDK_V3.6.0/HyphenateFullSDK/\
就可以了。

后续在 SDK 当前路径下执行以下命令删除x86_64平台\

不包含实时音视频版本 `AgoraChat.framework`

    【首先进入AgoraChat.framework所在目录】
    // 移除支持x86_64的二进制文件
    lipo AgoraChat.framework/AgoraChat -remove x86_64 -output AgoraChat
    //替换framwork内部二进制文件[记得备份]
    mv AgoraChat AgoraChat.framework/AgoraChat
    //查看剥离后的二进制文件支持的CPU架构，如果显示arm64，就完成剥离，可上传AppStore
    lipo -info AgoraChat.framework/AgoraChat

------------------------------------------------------------------------


