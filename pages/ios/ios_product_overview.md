---
title: iOS Product Overview
keywords: ios
sidebar: ios_sidebar
toc: true
permalink: ios_product_overview.html
folder: ios
---
# iOS SDK's Introduction and import

------------------------------------------------------------------------

## DEMO（ChatDemo-UI3.0 App） experience


Download link：[download page](http://www.easemob.com/download/im)

# Demo（EaseIM App）Introduction

------------------------------------------------------------------------

## Code download

You can get the source code in the following two ways:

-   Download the compression package of code：[IM SDK and Demo download](https://www.easemob.com/download/im)
-   Download source code：[github source code link](https://github.com/easemob/chat-ios)

Everyone is welcome to submit PR to improve and fix the problems in EaseIM and EaseIMKit.

## Run EaseIM project

Download the iOS SDK compressed package from [IM SDK and Demo Download](https://www.easemob.com/download/im), and then unzip it. After decompression,the EaseIM project directory is under the EaseIM folder.

In the terminal, please cd to the podfile directory of EaseIM, and the terminal executes the 'pod install' command. After all the pod dependent libraries are downloaded, you can open EaseIM.xcworkspace and run the EaseIM demo for custom development.

## Pod library used

``` java
* Easemob SDK pod'HyphenateChat', '3.8.0'
* Easemob IM UI library pod'EaseIMKit', '3.8.0.1'
* Easemob audio and video UI library pod'EaseCallKit', '3.8.0.3'
* Soundnet audio and video SDK pod'AgoraRtcEngine_iOS', '3.3.1'
```

Third-party library includes

``` java
* pod 'MBProgressHUD',
* pod 'Masonry'
* pod 'MJRefresh'
* pod 'SDWebImage'
```

## Main module introduction

There are several main UI modules in the demo. You can add the corresponding modules to the project during integration.

-   Helper\-\-\-\-\--Custom libraries and pages, third-party libraries, global universal modules

-   Chat\-\-\-\-\--Chat module

-   Conversation\-\-\-\-\--Conversion list module


-   Communicate\-\-\-\-\--Real-time audio and video module(Including 1v1
    Real-time call and multi-person real-time call function)


-   Contact\-\-\-\-\--Friends list module


-   Group\-\-\-\-\--Group module


-   Chatroom\-\-\-\-\--Chat room module

When integrating, you must first import `Helper` module into your own project
, and then import other modules depending on your own needs.

## Main class introduction

-   **EaseIMHelper**：EaseIM global help class, demo
    The singleton class is mainly to monitor the callback of related events such as receiving messages, friends, groups, and chat rooms globally to perform corresponding process;
-   **AgoraConversationsViewController**：The session list function page of the demo shows the implementation of extended item side-slip events and item click events;
-   **AgoraChatViewController**：The conversation list function page of the demo shows the implementation of extended item side-slip events and item click events;
-   **AgoraContactsViewController**：The contact page shown in the demo shows the long-press side-slip of adding items and the realization of item click events, etc.;
-   **AgoraGroupInfoViewController**：The following functions are implemented: adding group members, modifying group announcements and group introductions, uploading shared files, group management, setting message do not disturb and disbanding or exiting the group, etc.

## Display of some UIs

![contact list](/im/android/other/easeim通讯录.jpg){width="360"}

![Chat page](/im/android/other/easeim聊天.jpg){width="360"}

------------------------------------------------------------------------


