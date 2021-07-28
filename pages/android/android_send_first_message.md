---
title: Send First Message
keywords: android
sidebar: android_sidebar
toc: true
permalink: android_send_first_message.html
folder: android
---

## Follow these steps to send your first message

1、Add remote library address of `mavenCentral` and remote library dependencies in your project

Add the remote library address to the `build.gradle` file in your project root directory

```
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
```
Then add the following code to the `build.gradle` of your module

``` java
android {
    
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

2、 Initialize the Chat SDK

```java
// set init sdk options
ChatOptions options = new ChatOptions();
// set your appkey
options.setAppKey("Your appkey");
//init hyphenate sdk with options
ChatClient.getInstance().init(context, options);
```

3、Login chat server

```java
ChatClient.getInstance().login(mAccount, mPassword, new CallBack() {
            /**
             * Sign in success callback
             */
            @Override 
            public void onSuccess() {
                // Sign in success jump MainActivity
                Intent intent = new Intent(mActivity, MainActivity.class);
                startActivity(intent);
                // finish activity;
                finish();
            }

            /**
             * Sign in failed callback
             * @param code failed code
             * @param message failed message
             */
            @Override 
            public void onError(final int code, final String message) {
                
            }

            @Override 
            public void onProgress(int progress, String content) {
                
            }
        });
```

4、Construct and send the message

```java
// create a message
ChatMessage message = ChatMessage.createTxtSendMessage("Hello world!", toChatUsername);
// send message
ChatClient.getInstance().chatManager().sendMessage(message);
```

------------------------------------------------------------------------
