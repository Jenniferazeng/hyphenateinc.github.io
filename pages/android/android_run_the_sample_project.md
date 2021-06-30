---
title: Android Run the Sample Project
keywords: android
sidebar: android_sidebar
toc: true
permalink: android_run_the_sample_project.html
folder: android
---
# Demo Introduction

------------------------------------------------------------------------

## Code download

You can get the source code in the following two ways:

-   Download the code compression package: [IM SDK and Demo
        Download](https://www.easemob.com/download/im)
-   Download the source code: [github source address](https://github.com/easemob/chat-android)

Everyone is welcome to submit PR to improve and fix the problems in ChatDemo.

## Import ChatDemo

Download the Android SDK compressed package from [Chat SDK and Demo Download](https://www.easemob.com/download/im), and then unzip it. After decompression, under the examples folder, it is the ChatDemo project directory.

### Import to Android Studio

Open Android Studio, click File→Open to open the root directory of ChatDemo. \

### Import to Eclipse

Click File→Import→click the subdirectory under Android→Next→select the root path of ChatDemo→Finish.

## Main category introduction

-   **DemoHelper**：ChatDemo global helper class, the main function is to initialize ChatSDK, register global listners, etc.;
-   **ConversationListFragment**: Displays how to load and show conversation list, the item long press event, and item click event, etc.;
-   **ChatActivity**: It shows the extended item long press event, the preset item long press menu and the rewrite part of the long press event function, shows how to reset and add more extended functions, and shows the implementation of the avatar click event and inputting events
-   **ContactListFragment**：It shows the adding headers layout, adding item long press function and implementing item click events, etc.;


------------------------------------------------------------------------
