---
title: Send First Message
keywords: ios
sidebar: ios_sidebar
toc: true
permalink: ios_send_first_message.html
folder: android
---

## Follow these steps to send your first message
1、Import the AgoraChat Framework

1).Import  'AgoraChat' in your podfile 
```
pod 'AgoraChat' 
```

2).Install dependencies.
```
pod install
```

2、 Initialize the Chat SDK

```objc 
AgoraOptions *options = [AgoraOptions optionsWithAppkey:@"your applyed key"];
// AgoraChat cert keys
[options setApnsCertName:"your certificate"];
[options setEnableConsoleLog:YES];
[options setIsDeleteMessagesWhenExitGroup:NO];
[options setIsDeleteMessagesWhenExitChatRoom:NO];
[options setUsingHttpsOnly:YES];
[[AgoraChatClient sharedClient] initializeSDKWithOptions:options];

```

3、Login chat server

```objc

[[AgoraChatClient sharedClient] loginWithUsername:@"username"
                password:@"password"
            completion:^(NSString *aUsername, AgoraError *aError) {
                [MBProgressHUD hideAllHUDsForView:self.view animated:YES];
                if (!aError) {
                    [[AgoraChatClient sharedClient].options setIsAutoLogin:YES];
                    [[NSNotificationCenter defaultCenter] postNotificationName:KNOTIFICATION_LOGINCHANGE object:@YES];
                } else {
                    NSString *alertStr = NSLocalizedString(@"login.failure", @"Login failure");
                    
                }
}];

```

4、Construct and send the message

```objc
// create a message
  Message *message = [[Message alloc] initWithConversationID:receiver
                                                              from:sender
                                                                to:receiver
                                                              body:body
                                                               ext:messageExt];
// send message
  [[AgoraChatClient sharedClient].chatManager sendMessage:message progress:nil completion:^(Message *message, AgoraError *error) {
    }];

```

------------------------------------------------------------------------
