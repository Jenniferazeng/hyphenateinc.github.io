---
title: Message Withdraw
keywords: ios
sidebar: ios_sidebar
toc: true
permalink: ios_message_withdraw.html
folder: ios
---

## Message withdrawn

**Note:** The message withdrawal is a value-added function, please contact Agora Business Opening.

``` objc
/*!
*  Withdraw message
*
*  Asynchronous method
*
*  @param aMessageId           Message Id
*  @param aCompletionBlock     Completed callback
*/
- (void)recallMessageWithMessageId:(NSString *)aMessageId
                        completion:(void (^)(AgoraError *aError))aCompletionBlock;
           
// Call:
[[AgoraChatClient sharedClient].chatManager recallMessageWithMessageId:messageId completion:^(AgoraError *aError) {
    if (!aError) {
        NSLog(@"Withdraw the message successfully");
    } else {
        NSLog(@"Reasons for failure to withdraw the message--- %@", aError.errorDescription);
    }
}];
```

Message withdrawal receipt

``` objc
/*!
 *  withdrawn Received message 

 *
 *  @param aMessages  Withdraw messages list<AgoraMessage>
 */
- (void)messagesDidRecall:(NSArray *)aMessages;
```


------------------------------------------------------------------------


