---
title: iOS Message Roaming
keywords: ios
sidebar: ios_sidebar
toc: true
permalink: ios_message_roaming.html
folder: ios
---

## 消息漫游

## 服务设置

-   默认设置：关闭
-   是否增值服务：是

## 功能介绍

1.  同一用户所有已登录设备都可以获取该用户在所有设备上的历史消息；
2.  用户可从服务器获取全部历史消息，也可以根据本地数据库存储的消息有选择的获取服务器历史消息；
3.  消息漫游支持以会话为单位获取历史消息；
4.  漫游消息的存储不限制条数和消息类型，时间限制分为3个月和6个月两种，客户可根据自身需求进行选择；

## 使用方法

### iOS

``` objc
/**
 *  从服务器获取指定会话的历史消息
 *
 *  异步方法
 *
 *  @param  aConversationId     要获取漫游消息的Conversation id （就是要漫游与哪个环信id聊天的消息，就传哪个环信id）
 *  @param  aConversationType   要获取漫游消息的Conversation type （会话的类型，分单聊，群聊，聊天室会话类型）
 *  @param  aStartMessageId     参考起始消息的ID
 *  @param  aPageSize           获取消息条数
 *  @param  aCompletionBlock    获取消息结束的callback
 */
- (void)asyncFetchHistoryMessagesFromServer:(NSString *)aConversationId
                           conversationType:(AgoraConversationType)aConversationType
                             startMessageId:(NSString *)aStartMessageId
                                   pageSize:(int)aPageSize
                                 complation:(void (^)(AgoraCursorResult *aResult, AgoraError *aError))aCompletionBlock;
                                 
// 调用示例：
[[AgoraChatClient sharedClient].chatManager asyncFetchHistoryMessagesFromServer:@"conversationid"
                                                        conversationType:AgoraConversationTypeChat
                                                          startMessageId:nil
                                                                pageSize:20
                                                              completion:^(AgoraCursorResult *aResult, AgoraError *aError) {
    if (!aError) {
        NSLog(@"漫游消息成功---");
    } else {
        NSLog(@"漫游消息失败---%@", aError.errorDescription);
    }
}];
                               
```

------------------------------------------------------------------------

