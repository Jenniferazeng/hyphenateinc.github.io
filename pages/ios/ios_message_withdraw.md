---
title: iOS Message Withdraw
keywords: ios
sidebar: ios_sidebar
toc: true
permalink: ios_message_withdraw.html
folder: ios
---
# iOS SDK's Introduction and import

------------------------------------------------------------------------

## DEMO（ChatDemo-UI3.0 App） experience


Download link：[download page](http://www.easemob.com/download/im)

## 消息撤回

------------------------------------------------------------------------

消息撤回可实现的场景例如，当A用户发消息给B用户后，可以在一段时间内将消息撤回。

![消息回撤过程](/im/other/integrationcases/image020.png){.align-center}

A用户发消息给B用户，当需要撤回的时候，将要撤回消息的id通过扩展消息发送给B，B检测到撤回的透传消息后，将对应
messageid 的消息从数据库删除。

1.  A用户发送消息。
2.  A用户需要撤回某条消息，将消息id通过扩展消息发送到用户B。
3.  B用户收到扩展消息，解析其中的messageid，从数据库删除对应消息。

注："扩展消息"指透传消息，它是一种特殊类型的消息，收发双方不会存数据库，同时用户离线时也不会有推送，比较适合配合业务来处理一些功能。

## 发送要撤回的透传消息

#### Android示例

``` java
AgoraMessage cmdMsg = AgoraMessage.createSendMessage(AgoraMessage.Type.CMD);
// 如果是群聊，设置chattype，默认是单聊
if (chatType == CHATTYPE_GROUP){
    cmdMsg.setChatType(ChatType.GroupChat);
}
String action="REVOKE_FLAG";
CmdMessageBody cmdBody=new CmdMessageBody(action)；
// 设置消息body
cmdMsg.addBody(cmdBody);
// 设置要发给谁，用户username或者群聊groupid
cmdMsg.setReceipt(toChatUsername);
// 通过扩展字段添加要撤回消息的id
cmdMsg.setAttribute("msgId",msgid);

AgoraChatManager.getInstance().sendMessage(cmdMsg,new AgoraCallBack() {
    @Override
    public void onSuccess() {}
                
    @Override
    public void onProgress(int progress, String status) {}
                
    @Override
    public void onError(int code, String error) {}
});
```

#### iOS示例

``` objc
// 发送撤回的透传消息
- (void)revokeMessageWithMessageId:(NSString *)aMessageId
                    conversationId:(NSString *)conversationId
{
    AgoraCmdMessageBody *body = [[AgoraCmdMessageBody alloc] initWithAction:@"REVOKE_FLAG"];
    NSDictionary *ext = @{@"msgId":aMessageId};
    NSString *currentUsername = [AgoraChatClient sharedClient].currentUsername;
    AgoraMessage *message = [[AgoraMessage alloc] initWithConversationID:conversationId
                                                          from:currentUsername
                                                            to:conversationId
                                                          body:body
                                                           ext:ext];
    //发送cmd消息
    [[AgoraChatClient sharedClient].chatManager sendMessage:message
                                            progress:nil
                                          completion:^(AgoraMessage *message, AgoraError *error) {
                                              if (!error) {
                                                  NSLog(@"发送成功");
                                              }
                                          }];
    
}
```

## 接收透传信息，删除要撤回的消息

#### Android示例

``` java
AgoraChatManager.getInstance().registerEventListener(new AgoraEventListener() {
        
    @Override
    public void onEvent(AgoraNotifierEvent event) {
        switch (event.getEvent()) {
        case EventNewCMDMessage: // CMD消息
        {
            AgoraMessage message = (AgoraMessage) event.getData();
            CmdMessageBody cmdMsgBody = (CmdMessageBody) message.getBody();
            String action = cmdMsgBody.action;//获取自定义action
            if(action.equals("REVOKE_FLAG")){
                try {
                    String msgId = message.getStringAttribute("msgId");
                    AgoraConversation conversation = AgoraChatManager.getInstance().getConversation(message.getFrom());
                    --删除消息来表示撤回--
                    conversation.removeMessage(msgId);
                    // 如果需要，可以插入一条“XXX撤回一条消息”
                } catch (EaseMobException e) {
                    // TODO Auto-generated catch block
                    e.printStackTrace();
                }

            }
            break;
        }
        default:
            break;
        }           
    }
}, new AgoraNotifierEvent.Event[] { AgoraNotifierEvent.Event.EventNewCMDMessage});
```

#### iOS示例

``` objc
// 接收透传消息
- (void)cmdMessagesDidReceive:(NSArray *)aCmdMessages {
    NSMutableArray *revokeMessageIds = nil;
    for (AgoraMessage *cmdMessage in aCmdMessages)
    {
        AgoraCmdMessageBody *body = (AgoraCmdMessageBody *)cmdMessage.body;
        if ([body.action isEqualToString:@"REVOKE_FLAG"]) {
            NSString *revokeMessageId = cmdMessage.ext[@"msgId"];
            BOOL isSuccess = [self removeRevokeMessageWithChatter:cmdMessage.from
                                                 conversationType:(AgoraConversationType)cmdMessage.chatType
                                                        messageId:revokeMessageId];
            if (isSuccess) {
                // update ui，如果需要，可以插入一条“XXX撤回一条消息”
            }
        }
    }
}

// 删除消息
- (BOOL)removeRevokeMessageWithChatter:(NSString *)aChatter
                      conversationType:(AgoraConversationType)type
                             messageId:(NSString *)messageId{
    
    AgoraConversation *conversation = [[AgoraChatClient sharedClient].chatManager getConversation:aChatter type:type createIfNotExist:YES];
    AgoraError *error = nil;
    [conversation deleteMessageWithId:messageId error:&error];
    return !error;
}
```

------------------------------------------------------------------------


