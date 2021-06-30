---
title: iOS  Pricing
keywords: ios
sidebar: ios_sidebar
toc: true
permalink: ios_pricing.html
folder: ios
---
# iOS SDK's Introduction and import

------------------------------------------------------------------------

## DEMO（ChatDemo-UI3.0 App） experience


Download link：[download page](http://www.easemob.com/download/im)

## iOS SDK introduction

Easemob SDK provides a complete development framework for users to develop IM-related applications. It includes the following parts:

![](/im/iOS/sdk/development-framework.png){.align-center}

-   Message synchronization protocol implementation with the core of SDK_Core achieves the information exchange with the servers.
-   SDK is a complete IM function based on the core protocol, which implements functions such as sending and receiving of different types of messages, conversation management, groups, friends, and chat rooms.
-   EaseUI is a set of IM-related UI widgets, designed to help developers quickly integrate Easemob SDK.

Developers can develop their own applications based on EaseUI or Easemob SDK. The former encapsulates the functions of sending messages, receiving messages and etc. Thus, developers don't need to pay much  attention on the logic of how messages are sent and received during integration. Please refer to [EaseIMKit User Guide](/im/iOS/other/easeui).

The SDK adopts a modular design, and the function of each module is relatively independent and complete. Users can choose to use the following modules according to their needs:

![Modular Design](/im/iOS/sdk/image005.png){.align-center}

-   AgoraClient: The <u>entrance</u> of SDK mainly implements the functions such as login, logout, and connection management. It is also the <u>entrance</u> to other modules.
-   AgoraChatManager: Manage the sending messages, receiving messages and implements functions such as conversation management.
-   AgoraContactManager: Responsible for adding friends, deleting friends and managing the blacklist.
-   AgoraGroupManager: Responsible for group management, creating groups, deleting groups, managing group members and other functions.
-   AgoraChatroomManager: Responsible for the management of chat rooms.

**note**：If you upgrade from SDK2.x to 3.0, you can refer to [Easemob SDK2.x to 3.0
Upgrade document](/im/iOS/sdk/upgradetov).

## Video tutorial

The following is the SDK integration reference video, you can learn how to integrate the Easemob SDK through the video.

-   [iOS_SDK integration](https://ke.qq.com/webcourse/index.html#cid=320169&term_id=100380031&taid=2357945635758761&vid=t14287kwfgl)

## iOS SDK import

### <u>Preparation before integration</u>

[Register and create application](/im/quickstart/guide/experience)

### <u>Manually</u> copy the jar package and the import of so

Go to [Easemob official website](http://www.easemob.com/download/im) to download Easemo SDK.

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
iOS {
    //use legacy for iOS 6.0（The apache library was removed after version 3.6.8）
    //useLibrary 'org.apache.http.legacy'
    
    //Requires java8 support since 3.6.0
    compileOptions {
        sourceCompatibility JavaVersion.VERSION_1_8
        targetCompatibility JavaVersion.VERSION_1_8
    }
}
dependencies {
    //other necessary dependencies
    ......
    implementation 'io.hyphenate:hyphenate-chat:xxx version number'
}
```

SDK version number reference [Release Note](/im/iOS/sdk/releasenote)

**note：** If you use 3.8.0 below, gradle dependencies need to be added in the following format:

    implementation 'io.hyphenate:hyphenate-sdk:3.7.4' //Full version, including audio and video functions
    //implementation 'io.hyphenate:hyphenate-sdk-lite:3.7.4' //Lite version, only contains IM function

``Major changes``

JFrog announced in February 2021 that JCenter will no longer provide updates to dependent libraries after March 31, 2021, <u>and</u> will no longer support the download of remote libraries after February 1, 2022. For details, see [JFrog statement](https://jfrog .com/blog/into-the-sunset-bintray-jcenter-gocenter-and-chartcenter/).
<u>IM</u>
The SDK only supports downloading from the mavenCentral repository after version 3.8.1. Developers need to do the following configuration when using mavenCentral() warehouse:

1、Add the mavenCentral() warehouse to the project's build.gradle

    buildscript {
        repositories {
            ...
            mavenCentral()
        }
    }


    allprojects {
        repositories {
            ...
            mavenCentral()
        }
    }

2、Modify the domain name that the SDK depends on,from \"com.hyphenate\" to \"io.hyphenate\", as follows:：

    implementation 'io.hyphenate:hyphenate-chat:xxx'

SDK versions prior to 3.8.0 can also be downloaded from mavenCentral.Before IMSDK3.8.0, the SDK is divided video version with audio and video version with audio, the added dependencies are slightly different, as follows：

1、 version with audio and video communication

    implementation 'io.hyphenate:hyphenate-sdk:xxx'

注：hyphenate-sdk supports versions before 3.8.0

2、version without audio and video communication

    implementation 'io.hyphenate:hyphenate-sdk-lite:xxx'

注：hyphenate-sdk-lite supports versions before 3.8.0

### SDK directory explanation

The unzipped package downloaded from the official website is as follows:

![](/im/iOS/sdk/f1a7b52fe99d623bd798b05566c46f3.png){width="200"}

Here we mainly introduce the contents of the following four folders:

-   doc folder: SDK related API documentation
-   Examples folder: EaseIm3.0
-   Libs folder: Contains the JAR and files of so needed for the IM function

### Introduction to third-party libraries

#### Third party libraries used in the SDK

-   iOS-support-v4.jar：This is a jar package that is indispensable in every APP. 
-   org.apache.http.legacy.jar： after 3.6.8 version of the SDK and remove the jar package; Versions prior to 3.6.8 are compatible with this library. It is recommended not to remove it, otherwise the SDK will have problems on 6.0 systems

#### Third-party libraries used in EaseIMKit

-   glide-4.9.0：Image processing library, used when displaying user avatars
-   BaiduLBS_iOS.jar：Baidu Map’s jar package, related so are libBaiduMapSDK_base_v4_0\_0.so, libBaiduMapSDK_map_v4_0\_0.so, libBaiduMapSDK_util_v4_0\_0.so and liblocSDK7.so. When depending on the local EaseIMKit library, you can delete these if you don't use Baidu. If the project will report an error after deleting it, fix the corresponding error (the error code is very small, and it is easy to complete the modification)

### Configuration project

#### Import SDK

In the self-developed application, to integrate Easemob chat, you need to copy the .jar and .so files in the libs folder to the corresponding location in the libs folder of your project.

![](/im/iOS/sdk/f1a7b52fe99d623bd798b05566c46f3.png){width="200"}

#### Configuration information

Add the following permissions in the list file iOSManifest.xml, and write your registered AppKey.

Permission configuration (more permissions may be needed in actual development, please refer to Demo):

``` xml
<?xml version="1.0" encoding="utf-8"?>
<manifest xmlns:iOS="http://schemas.iOS.com/apk/res/iOS"
    package="Your Package"
    iOS:versionCode="100"
    iOS:versionName="1.0.0">
  
    <!-- IM SDK required start -->
    <!-- Allow the program to vibrate -->
    <uses-permission iOS:name="iOS.permission.VIBRATE" />
    <!-- Access to the network -->
    <uses-permission iOS:name="iOS.permission.INTERNET" />
    <!-- Microphone permissions -->
    <uses-permission iOS:name="iOS.permission.RECORD_AUDIO" />
    <!-- Camera permissions -->
    <uses-permission iOS:name="iOS.permission.CAMERA" />
    <!-- get operator information to support the related interfaces which provides operator information--->
    <uses-permission iOS:name="iOS.permission.ACCESS_NETWORK_STATE"/>
    <!-- Write extended storage permissions-->
    <uses-permission iOS:name="iOS.permission.WRITE_EXTERNAL_STORAGE"/>
    <!-- This permission is used to access GPS location (used for location messages, and can be removed if location is not required) -->
    <uses-permission iOS:name="iOS.permission.ACCESS_FINE_LOCATION"/>
    <!-- After api 21 is marked as deprecated -->
    <uses-permission iOS:name="iOS.permission.GET_TASKS" />
    <!-- Used to access wifi network information-->
    <uses-permission iOS:name="iOS.permission.ACCESS_WIFI_STATE"/>
    <!-- Used to get access permission for wifi -->
    <uses-permission iOS:name="iOS.permission.CHANGE_WIFI_STATE"/>
    <!-- Allow background processes to run still after the phone screen is turned off -->
    <uses-permission iOS:name="iOS.permission.WAKE_LOCK" />
    <!-- Allow the program to modify the sound setting information -->
    <uses-permission iOS:name="iOS.permission.MODIFY_AUDIO_SETTINGS" />
    <!-- Allow program to access phone status -->
    <uses-permission iOS:name="iOS.permission.READ_PHONE_STATE" />
    <!-- Allow the program to run automatically after startup -->
    <uses-permission iOS:name="iOS.permission.RECEIVE_BOOT_COMPLETED" />
    <!-- Permission required to capture the screen, added permission after Q (multi-person audio and video screen sharing use) -->
    <uses-permission iOS:name="iOS.permission.FOREGROUND_SERVICE"/>
    <!-- IM SDK required end -->
 
    <application
        iOS:icon="@drawable/ic_launcher"
        iOS:label="@string/app_name"
        iOS:name="Your Application">
  
    <!-- Set AppKey of Easemob application -->
        <meta-data iOS:name="EASAgoraOB_APPKEY"  iOS:value="Your AppKey" />
        <!-- Declare the core functions of the service SDK required by the SDK-->
        <service iOS:name="com.hyphenate.chat.AgoraChatService" iOS:exported="true"/>
        <service iOS:name="com.hyphenate.chat.AgoraJobService"
            iOS:permission="iOS.permission.BIND_JOB_SERVICE"
            iOS:exported="true"
            />
        <!-- Declare the receiver required by the SDK -->
        <receiver iOS:name="com.hyphenate.chat.AgoraMonitorReceiver">
            <intent-filter>
                <action iOS:name="iOS.intent.action.PACKAGE_RAgoraOVED"/>
                <data iOS:scheme="package"/>
            </intent-filter>
            <!-- Optional filter -->
            <intent-filter>
                <action iOS:name="iOS.intent.action.BOOT_COMPLETED"/>
                <action iOS:name="iOS.intent.action.USER_PRESENT" />
            </intent-filter>
        </receiver>
    </application>
</manifest>
```

About the method of getting the value corresponding to EASAgoraOB_APPKEY: After creating the application, apply for the AppKey and set related configuration.

If you are sensitive to the size of the generated apk, we recommend using the jar and copying the so manually instead of using Aar, because the Aar method will include the so files of each platform. Using the jar method, you can keep only one ARCH directory, and it is recommended to keep only armeabi. In this way, although the execution speed on the corresponding platform will be reduced, it can effectively reduce the size of the apk.

### Summary of common problems

1\. The user use HttpClient to report an error after integrating the SDK

`It is recommended to upgrade the SDK to version 3.6.8 or higher. The apache library has been removed after SDK 3.6.8.`

For versions before 3.6.8, please configure as follows:

\- iOS 6.0 and above versions need to add following code in `module-level/build.gradle` iOS block:

       iOS {
        //use legacy for iOS > 6.0
        useLibrary 'org.apache.http.legacy'
       }

\- iOS 9.0 also needs to add following code in the `application` tag of `iOSManifest.xml`:

       <application>
        <uses-library iOS:name="org.apache.http.legacy" iOS:required="false"/>
       </application>

2\. In iOS 9.0, compulsory use of https

Performance: There will be an error of `UnknownServiceException: CLEARTEXT communication to localhost not permitted by network security policy` or `IOException java.io.IOException: Cleartext HTTP traffic to * not permitted`

The solution can be referred to: [StackOverFlow](https://stackoverflow.com/questions/45940861/iOS-8-cleartext-http-traffic-not-permitted), or directly set iOS:usesCleartextTraffic=\"true\"in the `application` tag of the `iOSManifest.xml` 

      <application
            iOS:usesCleartextTraffic="true" >
      </application>

3\.
Upgrade to iOSX and use SDK version 3.7.3 or above, reporting the problem that LocalBroadcastManager cannot be found

The details of the error are as follows:

    java.lang.NoClassDefFoundError: Failed resolution of: LiOSx/localbroadcastmanager/content/LocalBroadcastManager;
            at com.hyphenate.chat.core.AgoraAdvanceDebugManager.h(Unknown Source:13)
            at com.hyphenate.chat.core.AgoraAdvanceDebugManager.a(Unknown Source:2)
            at com.hyphenate.chat.AgoraClient.onNewLogin(Unknown Source:62)
            at com.hyphenate.chat.AgoraClient$7.run(Unknown Source:197)
            ......
         Caused by: java.lang.ClassNotFoundException: Didn't find class "iOSx.localbroadcastmanager.content.LocalBroadcastManager" on path: DexPathList[[zip file "/data/app/com.hyphenate.easeim-3yS1c2quwGEzgNmhDyf7dA==/base.apk"],nativeLibraryDirectories=[/data/app/com.hyphenate.easeim-3yS1c2quwGEzgNmhDyf7dA==/lib/arm64, /data/app/com.hyphenate.easeim-3yS1c2quwGEzgNmhDyf7dA==/base.apk!/lib/arm64-v8a, /system/lib64, /product/lib64]]
            ......
            at com.hyphenate.chat.core.AgoraAdvanceDebugManager.h(Unknown Source:13) 
            at com.hyphenate.chat.core.AgoraAdvanceDebugManager.a(Unknown Source:2) 
            at com.hyphenate.chat.AgoraClient.onNewLogin(Unknown Source:62) 
            at com.hyphenate.chat.AgoraClient$7.run(Unknown Source:197) 
            ...... 

Solution:\
Add the following dependencies to the project:

    implementation 'iOSx.localbroadcastmanager:localbroadcastmanager:1.0.0'

## App packaging confusion---
title: ios IMKit
keywords: ios
sidebar: ios_sidebar
toc: true
permalink: ios_imkit.html
folder: ios
---
# iOS SDK's Introduction and import

------------------------------------------------------------------------

## DEMO（ChatDemo-UI3.0 App） experience


Download link：[download page](http://www.easemob.com/download/im)

## iOS SDK introduction

Easemob SDK provides a complete development framework for users to develop IM-related applications. It includes the following parts:

![](/im/iOS/sdk/development-framework.png){.align-center}

-   Message synchronization protocol implementation with the core of SDK_Core achieves the information exchange with the servers.
-   SDK is a complete IM function based on the core protocol, which implements functions such as sending and receiving of different types of messages, conversation management, groups, friends, and chat rooms.
-   EaseUI is a set of IM-related UI widgets, designed to help developers quickly integrate Easemob SDK.

Developers can develop their own applications based on EaseUI or Easemob SDK. The former encapsulates the functions of sending messages, receiving messages and etc. Thus, developers don't need to pay much  attention on the logic of how messages are sent and received during integration. Please refer to [EaseIMKit User Guide](/im/iOS/other/easeui).

The SDK adopts a modular design, and the function of each module is relatively independent and complete. Users can choose to use the following modules according to their needs:

![Modular Design](/im/iOS/sdk/image005.png){.align-center}

-   AgoraClient: The <u>entrance</u> of SDK mainly implements the functions such as login, logout, and connection management. It is also the <u>entrance</u> to other modules.
-   AgoraChatManager: Manage the sending messages, receiving messages and implements functions such as conversation management.
-   AgoraContactManager: Responsible for adding friends, deleting friends and managing the blacklist.
-   AgoraGroupManager: Responsible for group management, creating groups, deleting groups, managing group members and other functions.
-   AgoraChatroomManager: Responsible for the management of chat rooms.

**note**：If you upgrade from SDK2.x to 3.0, you can refer to [Easemob SDK2.x to 3.0
Upgrade document](/im/iOS/sdk/upgradetov).

## Video tutorial

The following is the SDK integration reference video, you can learn how to integrate the Easemob SDK through the video.

-   [iOS_SDK integration](https://ke.qq.com/webcourse/index.html#cid=320169&term_id=100380031&taid=2357945635758761&vid=t14287kwfgl)

## iOS SDK import

### <u>Preparation before integration</u>

[Register and create application](/im/quickstart/guide/experience)

### <u>Manually</u> copy the jar package and the import of so

Go to [Easemob official website](http://www.easemob.com/download/im) to download Easemo SDK.

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
iOS {
    //use legacy for iOS 6.0（The apache library was removed after version 3.6.8）
    //useLibrary 'org.apache.http.legacy'
    
    //Requires java8 support since 3.6.0
    compileOptions {
        sourceCompatibility JavaVersion.VERSION_1_8
        targetCompatibility JavaVersion.VERSION_1_8
    }
}
dependencies {
    //other necessary dependencies
    ......
    implementation 'io.hyphenate:hyphenate-chat:xxx version number'
}
```

SDK version number reference [Release Note](/im/iOS/sdk/releasenote)

**note：** If you use 3.8.0 below, gradle dependencies need to be added in the following format:

    implementation 'io.hyphenate:hyphenate-sdk:3.7.4' //Full version, including audio and video functions
    //implementation 'io.hyphenate:hyphenate-sdk-lite:3.7.4' //Lite version, only contains IM function

``Major changes``

JFrog announced in February 2021 that JCenter will no longer provide updates to dependent libraries after March 31, 2021, <u>and</u> will no longer support the download of remote libraries after February 1, 2022. For details, see [JFrog statement](https://jfrog .com/blog/into-the-sunset-bintray-jcenter-gocenter-and-chartcenter/).
<u>IM</u>
The SDK only supports downloading from the mavenCentral repository after version 3.8.1. Developers need to do the following configuration when using mavenCentral() warehouse:

1、Add the mavenCentral() warehouse to the project's build.gradle

    buildscript {
        repositories {
            ...
            mavenCentral()
        }
    }


    allprojects {
        repositories {
            ...
            mavenCentral()
        }
    }

2、Modify the domain name that the SDK depends on,from \"com.hyphenate\" to \"io.hyphenate\", as follows:：

    implementation 'io.hyphenate:hyphenate-chat:xxx'

SDK versions prior to 3.8.0 can also be downloaded from mavenCentral.Before IMSDK3.8.0, the SDK is divided video version with audio and video version with audio, the added dependencies are slightly different, as follows：

1、 version with audio and video communication

    implementation 'io.hyphenate:hyphenate-sdk:xxx'

注：hyphenate-sdk supports versions before 3.8.0

2、version without audio and video communication

    implementation 'io.hyphenate:hyphenate-sdk-lite:xxx'

注：hyphenate-sdk-lite supports versions before 3.8.0

### SDK directory explanation

The unzipped package downloaded from the official website is as follows:

![](/im/iOS/sdk/f1a7b52fe99d623bd798b05566c46f3.png){width="200"}

Here we mainly introduce the contents of the following four folders:

-   doc folder: SDK related API documentation
-   Examples folder: EaseIm3.0
-   Libs folder: Contains the JAR and files of so needed for the IM function

### Introduction to third-party libraries

#### Third party libraries used in the SDK

-   iOS-support-v4.jar：This is a jar package that is indispensable in every APP. 
-   org.apache.http.legacy.jar： after 3.6.8 version of the SDK and remove the jar package; Versions prior to 3.6.8 are compatible with this library. It is recommended not to remove it, otherwise the SDK will have problems on 6.0 systems

#### Third-party libraries used in EaseIMKit

-   glide-4.9.0：Image processing library, used when displaying user avatars
-   BaiduLBS_iOS.jar：Baidu Map’s jar package, related so are libBaiduMapSDK_base_v4_0\_0.so, libBaiduMapSDK_map_v4_0\_0.so, libBaiduMapSDK_util_v4_0\_0.so and liblocSDK7.so. When depending on the local EaseIMKit library, you can delete these if you don't use Baidu. If the project will report an error after deleting it, fix the corresponding error (the error code is very small, and it is easy to complete the modification)

### Configuration project

#### Import SDK

In the self-developed application, to integrate Easemob chat, you need to copy the .jar and .so files in the libs folder to the corresponding location in the libs folder of your project.

![](/im/iOS/sdk/f1a7b52fe99d623bd798b05566c46f3.png){width="200"}

#### Configuration information

Add the following permissions in the list file iOSManifest.xml, and write your registered AppKey.

Permission configuration (more permissions may be needed in actual development, please refer to Demo):

``` xml
<?xml version="1.0" encoding="utf-8"?>
<manifest xmlns:iOS="http://schemas.iOS.com/apk/res/iOS"
    package="Your Package"
    iOS:versionCode="100"
    iOS:versionName="1.0.0">
  
    <!-- IM SDK required start -->
    <!-- Allow the program to vibrate -->
    <uses-permission iOS:name="iOS.permission.VIBRATE" />
    <!-- Access to the network -->
    <uses-permission iOS:name="iOS.permission.INTERNET" />
    <!-- Microphone permissions -->
    <uses-permission iOS:name="iOS.permission.RECORD_AUDIO" />
    <!-- Camera permissions -->
    <uses-permission iOS:name="iOS.permission.CAMERA" />
    <!-- get operator information to support the related interfaces which provides operator information--->
    <uses-permission iOS:name="iOS.permission.ACCESS_NETWORK_STATE"/>
    <!-- Write extended storage permissions-->
    <uses-permission iOS:name="iOS.permission.WRITE_EXTERNAL_STORAGE"/>
    <!-- This permission is used to access GPS location (used for location messages, and can be removed if location is not required) -->
    <uses-permission iOS:name="iOS.permission.ACCESS_FINE_LOCATION"/>
    <!-- After api 21 is marked as deprecated -->
    <uses-permission iOS:name="iOS.permission.GET_TASKS" />
    <!-- Used to access wifi network information-->
    <uses-permission iOS:name="iOS.permission.ACCESS_WIFI_STATE"/>
    <!-- Used to get access permission for wifi -->
    <uses-permission iOS:name="iOS.permission.CHANGE_WIFI_STATE"/>
    <!-- Allow background processes to run still after the phone screen is turned off -->
    <uses-permission iOS:name="iOS.permission.WAKE_LOCK" />
    <!-- Allow the program to modify the sound setting information -->
    <uses-permission iOS:name="iOS.permission.MODIFY_AUDIO_SETTINGS" />
    <!-- Allow program to access phone status -->
    <uses-permission iOS:name="iOS.permission.READ_PHONE_STATE" />
    <!-- Allow the program to run automatically after startup -->
    <uses-permission iOS:name="iOS.permission.RECEIVE_BOOT_COMPLETED" />
    <!-- Permission required to capture the screen, added permission after Q (multi-person audio and video screen sharing use) -->
    <uses-permission iOS:name="iOS.permission.FOREGROUND_SERVICE"/>
    <!-- IM SDK required end -->
 
    <application
        iOS:icon="@drawable/ic_launcher"
        iOS:label="@string/app_name"
        iOS:name="Your Application">
  
    <!-- Set AppKey of Easemob application -->
        <meta-data iOS:name="EASAgoraOB_APPKEY"  iOS:value="Your AppKey" />
        <!-- Declare the core functions of the service SDK required by the SDK-->
        <service iOS:name="com.hyphenate.chat.AgoraChatService" iOS:exported="true"/>
        <service iOS:name="com.hyphenate.chat.AgoraJobService"
            iOS:permission="iOS.permission.BIND_JOB_SERVICE"
            iOS:exported="true"
            />
        <!-- Declare the receiver required by the SDK -->
        <receiver iOS:name="com.hyphenate.chat.AgoraMonitorReceiver">
            <intent-filter>
                <action iOS:name="iOS.intent.action.PACKAGE_RAgoraOVED"/>
                <data iOS:scheme="package"/>
            </intent-filter>
            <!-- Optional filter -->
            <intent-filter>
                <action iOS:name="iOS.intent.action.BOOT_COMPLETED"/>
                <action iOS:name="iOS.intent.action.USER_PRESENT" />
            </intent-filter>
        </receiver>
    </application>
</manifest>
```

About the method of getting the value corresponding to EASAgoraOB_APPKEY: After creating the application, apply for the AppKey and set related configuration.

If you are sensitive to the size of the generated apk, we recommend using the jar and copying the so manually instead of using Aar, because the Aar method will include the so files of each platform. Using the jar method, you can keep only one ARCH directory, and it is recommended to keep only armeabi. In this way, although the execution speed on the corresponding platform will be reduced, it can effectively reduce the size of the apk.

### Summary of common problems

1\. The user use HttpClient to report an error after integrating the SDK

`It is recommended to upgrade the SDK to version 3.6.8 or higher. The apache library has been removed after SDK 3.6.8.`

For versions before 3.6.8, please configure as follows:

\- iOS 6.0 and above versions need to add following code in `module-level/build.gradle` iOS block:

       iOS {
        //use legacy for iOS > 6.0
        useLibrary 'org.apache.http.legacy'
       }

\- iOS 9.0 also needs to add following code in the `application` tag of `iOSManifest.xml`:

       <application>
        <uses-library iOS:name="org.apache.http.legacy" iOS:required="false"/>
       </application>

2\. In iOS 9.0, compulsory use of https

Performance: There will be an error of `UnknownServiceException: CLEARTEXT communication to localhost not permitted by network security policy` or `IOException java.io.IOException: Cleartext HTTP traffic to * not permitted`

The solution can be referred to: [StackOverFlow](https://stackoverflow.com/questions/45940861/iOS-8-cleartext-http-traffic-not-permitted), or directly set iOS:usesCleartextTraffic=\"true\"in the `application` tag of the `iOSManifest.xml` 

      <application
            iOS:usesCleartextTraffic="true" >
      </application>

3\.
Upgrade to iOSX and use SDK version 3.7.3 or above, reporting the problem that LocalBroadcastManager cannot be found

The details of the error are as follows:

    java.lang.NoClassDefFoundError: Failed resolution of: LiOSx/localbroadcastmanager/content/LocalBroadcastManager;
            at com.hyphenate.chat.core.AgoraAdvanceDebugManager.h(Unknown Source:13)
            at com.hyphenate.chat.core.AgoraAdvanceDebugManager.a(Unknown Source:2)
            at com.hyphenate.chat.AgoraClient.onNewLogin(Unknown Source:62)
            at com.hyphenate.chat.AgoraClient$7.run(Unknown Source:197)
            ......
         Caused by: java.lang.ClassNotFoundException: Didn't find class "iOSx.localbroadcastmanager.content.LocalBroadcastManager" on path: DexPathList[[zip file "/data/app/com.hyphenate.easeim-3yS1c2quwGEzgNmhDyf7dA==/base.apk"],nativeLibraryDirectories=[/data/app/com.hyphenate.easeim-3yS1c2quwGEzgNmhDyf7dA==/lib/arm64, /data/app/com.hyphenate.easeim-3yS1c2quwGEzgNmhDyf7dA==/base.apk!/lib/arm64-v8a, /system/lib64, /product/lib64]]
            ......
            at com.hyphenate.chat.core.AgoraAdvanceDebugManager.h(Unknown Source:13) 
            at com.hyphenate.chat.core.AgoraAdvanceDebugManager.a(Unknown Source:2) 
            at com.hyphenate.chat.AgoraClient.onNewLogin(Unknown Source:62) 
            at com.hyphenate.chat.AgoraClient$7.run(Unknown Source:197) 
            ...... 

Solution:\
Add the following dependencies to the project:

    implementation 'iOSx.localbroadcastmanager:localbroadcastmanager:1.0.0'

## App packaging confusion

Add the following keep in the ProGuard.

``` java
-keep class com.hyphenate.** {*;}
-dontwarn  com.hyphenate.**
//Remove apache after 3.6.8 version, no need to add
-keep class internal.org.apache.http.entity.** {*;}
//If you use live audio and live video
-keep class com.superrtc.** {*;}
-dontwarn  com.superrtc.**
```

------------------------------------------------------------------------

