---
title: Product Overview
keywords: android
sidebar: android_sidebar
toc: true
permalink: android_product_overview.html
folder: android
---
## Overview

AgoraChat is a cloud-based provider of in-app chat. We provide the SDKs and a hosted Platform API to add chat to mobile and web apps. We offer the ability to add one-to-one chat, group chat, chat rooms, text messages, image and file sharing, emojis, typing indicators, delivery status and self-destructing messages.

Stop reinventing the wheel and spending valuable engineering resources on building a chat infrastructure yourself. Chat has your in-app chat system covered, so you can focus on building and optimizing the core features of your app.

Chat provides SDKs for native mobile apps for Android and iOS, Javascript for Web, and the APIs for server communication between the Chat server and your developer server. 

## Client Server Relationship
![Client Server Relationship](/images/index/PlatformArchitecture.jpeg)
## Chat Platform Advantages

Chat offers five core architecture design enhancements that efficiently handle a variety of complex services:

1. Asynchronous - High concurrent asynchronous architecture that can easily handle millions of concurrent requests.
2. Stateless - Stateless modules allow services to seamlessly switch between clusters in real time.
3. Cluster Service - Ensures high availability.
4. Horizontal Scaling - Optimized server configuration to ensure that each service cluster has high horizontal scalability.
5. Peak Traffic Handling - Resource optimization with additional system redundancy, 50% for core services and 30% for other services, which takes care of the peak volume in various conditions.

## Chat Protocol

- MSYNC is Chat's patent pending protocol that is optimized for mobile device performance and built to handle sporadic and unstable mobile networks. It is based on a binary format instead of raw text like XML, which is able to compress message data up to 80%. MSYNC also reduces the message relay between the client and server. As a result, Chat is able to deliver more information with significantly less overhead, less network bandwidth consumption, and better battery preservation. In addition, MSYNC is based on a persistent socket connection to establish real-time message delivery with extremely low latency in milliseconds scale. 

- Chat employs a smart heartbeat, meaning it adjusts the frequency of packet relay dynamically based on the communication channel and environment. It minimizes the mobile device power consumption and cellular data usage without compromising connection continuity.

## Features 

|  Feature                      | Description     |
|  :----:                       |  :----         |
|  User Management     |   1. Provide basic user management functions, add/delete friends, and blacklist friends. Support user metadata, can store user profile information for easy integration.  |
|  Message    |  Support all message types, including: text, emoji, picture, voice, video, location, files, CMD, custom messages.  |
|  Group      |  Provides a complete group management function, supporting different roles in group: owner, administrator and member. Support group blacklist, user ban and other functions.  |
|  Chatroom      |   4. Provides a complete chatroom management function and supports live interactive scenes.  |
|  Multi-Device      |   Support one same account to log in to multiple devices at the same time, message roaming synchronization.  |
|  Push notification      |   Support push platforms such as Apple APNS and Google FCM. |
|  Messageing Roaming      |  The roaming service can realize that the terminal user's historical messages are saved and synchronized through the server to ensure that the messages are synchronized in real time  |
|  Message Withdraw      |   Users can withdraw the message within the withdrawal time limit after the message is sent. |


## Android SDK introduction

Chat SDK provides a complete development framework for users to develop chat-related applications. It includes the following parts:


-   Message synchronization protocol implementation with the core of SDK_Core achieves the information exchange with the servers.
-   SDK is a complete chat function based on the core protocol, which implements functions such as sending and receiving of different types of messages, conversation management, groups, friends, and chat rooms.


The SDK adopts a modular design, and the function of each module is relatively independent and complete. Users can choose to use the following modules according to their needs:

-   ChatClient: The entrance of SDK mainly implements the functions such as login, logout, and connection management. It is also the entrance to other modules.
-   ChatManager: Manage the sending messages, receiving messages and implements functions such as conversation management.
-   ContactManager: Responsible for adding friends, deleting friends and managing the blacklist.
-   GroupManager: Responsible for group management, creating groups, deleting groups, managing group members and other functions.
-   ChatroomManager: Responsible for the management of chat rooms.


## Android SDK import

### Preparation before integration

[Register and create application](https://hyphenateinc.github.io/android_connect_to_chat_server.html)

### Manually copy the jar package and the import of so

Go to Agora official website to download chat SDK.

There is a libs folder in the downloaded SDK, which contains jar packages and files of so.

### Import via gradle remote link

First, add the remote library address under the `allprojects->repositories` attribute of the `build.gradle` file in your project root directory

``` gradle
    repositories {
        google()
        mavenCentral()
        maven { url 'http://developer.huawei.com/repo'} //If you need to use Huawei to push HMS, please add this sentence
    }
```

Then add the following code to the `build.gradle` of your module

``` java
android {
    
    //Requires java8 support since 3.6.0
    compileOptions {
        sourceCompatibility JavaVersion.VERSION_1_8
        targetCompatibility JavaVersion.VERSION_1_8
    }
}
dependencies {
    //other necessary dependencies
    ......
    implementation 'io.hyphenate:chat-sdk:xxx version number'
}
```

SDK version number reference [Release Note](https://hyphenateinc.github.io/android_release_note.html)

### SDK directory explanation

The unzipped package downloaded from the official website is as follows:

Here we mainly introduce the contents of the following four folders:

-   Doc folder: SDK related API documentation
-   Examples folder: ChatDemo
-   Libs folder: Contains the JAR and files of so needed for the chat function

### Introduction to third-party libraries

#### Third party libraries used in the SDK

-   android-support-v4.jar：This is a jar package that is indispensable in every APP. 


### Configuration project

#### Import SDK

In the self-developed application, to integrate Agora chat, you need to copy the .jar and .so files in the libs folder to the corresponding location in the libs folder of your project.

#### Configuration information

Add the following permissions in the list file AndroidManifest.xml, and write your registered AppKey.

Permission configuration (more permissions may be needed in actual development, please refer to Demo):

``` xml
<?xml version="1.0" encoding="utf-8"?>
<manifest xmlns:android="http://schemas.android.com/apk/res/android"
    package="Your Package"
    android:versionCode="100"
    android:versionName="1.0.0">
  
    <!-- chat SDK required start -->
    <!-- Allow the program to vibrate -->
    <uses-permission android:name="android.permission.VIBRATE" />
    <!-- Access to the network -->
    <uses-permission android:name="android.permission.INTERNET" />
    <!-- Microphone permissions -->
    <uses-permission android:name="android.permission.RECORD_AUDIO" />
    <!-- Camera permissions -->
    <uses-permission android:name="android.permission.CAMERA" />
    <!-- get operator information to support the related interfaces which provides operator information--->
    <uses-permission android:name="android.permission.ACCESS_NETWORK_STATE"/>
    <!-- Write extended storage permissions-->
    <uses-permission android:name="android.permission.WRITE_EXTERNAL_STORAGE"/>
    <!-- This permission is used to access GPS location (used for location messages, and can be removed if location is not required) -->
    <uses-permission android:name="android.permission.ACCESS_FINE_LOCATION"/>
    <!-- After api 21 is marked as deprecated -->
    <uses-permission android:name="android.permission.GET_TASKS" />
    <!-- Used to access wifi network information-->
    <uses-permission android:name="android.permission.ACCESS_WIFI_STATE"/>
    <!-- Used to get access permission for wifi -->
    <uses-permission android:name="android.permission.CHANGE_WIFI_STATE"/>
    <!-- Allow background processes to run still after the phone screen is turned off -->
    <uses-permission android:name="android.permission.WAKE_LOCK" />
    <!-- Allow the program to modify the sound setting information -->
    <uses-permission android:name="android.permission.MODIFY_AUDIO_SETTINGS" />
    <!-- Allow program to access phone status -->
    <uses-permission android:name="android.permission.READ_PHONE_STATE" />
    <!-- Allow the program to run automatically after startup -->
    <uses-permission android:name="android.permission.RECEIVE_BOOT_COMPLETED" />
    <!-- Permission required to capture the screen, added permission after Q (multi-person audio and video screen sharing use) -->
    <uses-permission android:name="android.permission.FOREGROUND_SERVICE"/>
    <!-- chat SDK required end -->
 
    <application
        android:icon="@drawable/ic_launcher"
        android:label="@string/app_name"
        android:name="Your Application">
  
        <!-- Declare the core functions of the service SDK required by the SDK-->
        <service android:name="io.agora.chat.ChatService" android:exported="true"/>
        <service android:name="io.agora.chat.JobService"
            android:permission="android.permission.BIND_JOB_SERVICE"
            android:exported="true"
            />
        <!-- Declare the receiver required by the SDK -->
        <receiver android:name="io.agora.chat.MonitorReceiver">
            <intent-filter>
                <action android:name="android.intent.action.PACKAGE_REMOVED"/>
                <data android:scheme="package"/>
            </intent-filter>
            <!-- Optional filter -->
            <intent-filter>
                <action android:name="android.intent.action.BOOT_COMPLETED"/>
                <action android:name="android.intent.action.USER_PRESENT" />
            </intent-filter>
        </receiver>
    </application>
</manifest>
```

If you are sensitive to the size of the generated apk, we recommend using the jar and copying the so manually instead of using Aar, because the Aar method will include the so files of each platform. Using the jar method, you can keep only one ARCH directory. In this way, although the execution speed on the corresponding platform will be reduced, it can effectively reduce the size of the apk.

### Summary of common problems

1. The user use HttpClient to report an error after integrating the SDK

- Android 9.0 needs to add following code in the `application` tag of `AndroidManifest.xml`:

       <application>
        <uses-library android:name="org.apache.http.legacy" android:required="false"/>
       </application>

2. In Android 9.0, compulsory use of https

Performance: There will be an error of `UnknownServiceException: CLEARTEXT communication to localhost not permitted by network security policy` or `IOException java.io.IOException: Cleartext HTTP traffic to * not permitted`

The solution can be referred to: [StackOverFlow](https://stackoverflow.com/questions/45940861/android-8-cleartext-http-traffic-not-permitted), or directly set android:usesCleartextTraffic=\"true\"in the `application` tag of the `AndroidManifest.xml` 

      <application
            android:usesCleartextTraffic="true" >
      </application>


## App packaging confusion

Add the following keep in the ProGuard.

``` java
-keep class io.agora.** {*;}
-dontwarn  io.agora.**
```

------------------------------------------------------------------------
