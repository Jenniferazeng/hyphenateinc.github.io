---
title: Android Relaease Note
keywords: android
sidebar: android_sidebar
toc: true
permalink: android_release_note.html
folder: android
---
# Android SDK update log

### Version V3.8.1 2021-04-13

New function:

* [IM SDK] addeded an interface for setting and getting user attributes. For integration, please refer to: [[im:android:basics:profile|user attributes]]
* [EaseIM App] addeded storage and display of user information (avatar, nickname, etc.);
* [EaseIM App] addeded sending and display of user business card messages (implemented using custom messages);

Fixed:

* [EaseIMKIt] Fixed the issue that duplicate messages would be sent when sending multiple messages with attachments;
* [EaseIMKIt] Fixed the display problem caused by the sorting problem when registering the chat type;
* [EaseIMKIt] Provides an interface for setting message properties before sending a message(OnaddedMsgAttrsBeforeSendEvent);
* [EaseIMKIt] Fixed the problem that the avatar of the setting chat page item will be blocked by the default avatar;
* [EaseIMKIt] Optimize the logic of uploading files and Fixed the problem that files cannot be uploaded in some scenes (EaseFileUtils):
* [IM SDK] Fixed the problem that the file length is not set when uploading the file message;
* [IM SDK] Fixed the problem that conversation list message cannot be loaded completely after the database is migrated from 2.0 to 3.0 after SDK 3.5.3;

Update(2021-05-08)：

* [EaseCallkit] Modify the way to join the Agora channel, use the digital uid to join, add the intercommunication with the applet, ``not intercommunication with the previous version'', see [[im:android:other:easecallkit|EaseCallKit user guide]];

`Major changes: `Remote repositories are unifiedly migrated from JCenter to `MavenCentral`, the domain name of the dependent library is changed from \"com.hyphenate\" to `"io.hyphenate"`, for detail, see [AndroidSDK introduction and import](/im/ android/sdk/import).

------------------------------------------------------------------------
