---
title: Android Run the Sample Project
keywords: android
sidebar: android_sidebar
toc: true
permalink: android_run_the_sample_project.html
folder: android
---

## Code download

You can get the source code in the following two ways:

-   Download the code compression package: [IM SDK and Demo
        Download](https://www.easemob.com/download/im)
-   Download the source code: [github source address](https://github.com/easemob/chat-android)

Everyone is welcome to submit PR to improve and fix the problems in ChatDemo.

## Import ChatDemo

Download the Android SDK compressed package from [Chat SDK and Demo Download](https://www.easemob.com/download/im), and then unzip it. After decompression, under the examples folder, it is the ChatDemo project directory.

### Import to Android Studio

Open Android Studio, click File→Open to open the root directory of ChatDemo. 

### Import to Eclipse

Click File→Import→click the subdirectory under Android→Next→select the root path of ChatDemo→Finish.

## Main category introduction

-   **DemoHelper**：ChatDemo global helper class, the main function is to initialize ChatSDK, register global listners, etc.;
-   **ConversationListFragment**: Displays how to load and show conversation list, the item long press event, and item click event, etc.;
-   **ChatActivity**: It shows you a list of messages, how to send and receive messages, how to add new message layouts, etc.;
-   **ContactListFragment**：It shows how to get data and display it, how to add click events, etc.;


------------------------------------------------------------------------
