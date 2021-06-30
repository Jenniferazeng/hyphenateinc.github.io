---
title: Message Roaming
keywords: ios
sidebar: ios_sidebar
toc: true
permalink: ios_message_roaming.html
folder: ios
---
## Message roaming

You can pull historical messages from the server to the local, so that it can synchronize messages when users switch to different device (`This function is a value-added service, you need to contact Agora business to activate`)

``` objc
/**
 *  get the history of the specified Conversation from the server
 *
 *  Asynchronous method
 *
 *  @param  aConversationId     Conversation id of the roaming message to get
 *  @param  aConversationType   Conversation type roaming message to get
 *  @param  aStartMessageId     refers to the ID of the start message
 *  @param  aPageSize           Get the number of messages (up to 50 messages at a time)
 *  @param  aCompletionBlock    Get the callback for the end of the message
 */
- (void)asyncFetchHistoryMessagesFromServer:(NSString *)aConversationId
                           conversationType:(AgoraConversationType)aConversationType
                             startMessageId:(NSString *)aStartMessageId
                                   pageSize:(int)aPageSize
                                 complation:(void (^)(AgoraCursorResult *aResult, AgoraError *aError))aCompletionBlock;
                                 
// Call:
[[AgoraChatClient sharedClient].chatManager asyncFetchHistoryMessagesFromServer:@"6001" conversationType:AgoraConversationTypeChat startMessageId:messageId pageSize:10 completion:^(AgoraCursorResult *aResult, AgoraError *aError) {
    if (!aError) {
        NSLog(@"Get the message from the server successfully");
    } else {
        NSLog(@"The reason for the failure to get the message from the server --- %@", aError.errorDescription);
    }
}];
```

------------------------------------------------------------------------

