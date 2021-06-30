---
title: Send First Message
keywords: android
sidebar: android_sidebar
toc: true
permalink: android_send_first_message.html
folder: android
---

## Follow these steps to send your first message

1、 Initialize the Chat SDK

```java
// set init sdk options
ChatOptions options = new ChatOptions();
// set your appkey
options.setAppKey("Your appkey");
//init hyphenate sdk with options
ChatClient.getInstance().init(context, options);
```

2、Login chat server

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

3、Construct and send the message

```java
// create a message
ChatMessage message = ChatMessage.createTxtSendMessage("Hello world!", toChatUsername);
// send message
ChatClient.getInstance().chatManager().sendMessage(message);
```

------------------------------------------------------------------------
