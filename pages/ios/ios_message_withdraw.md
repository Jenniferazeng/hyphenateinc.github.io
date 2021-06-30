---
title: ios Group
keywords: ios
sidebar: ios_sidebar
toc: true
permalink: ios_message_withdraw.html
folder: ios
---
# iOS SDK's Introduction and import

------------------------------------------------------------------------

## DEMO（EaseIM App） experience

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
EMMessage cmdMsg = EMMessage.createSendMessage(EMMessage.Type.CMD);
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

EMChatManager.getInstance().sendMessage(cmdMsg,new EMCallBack() {
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
    EMCmdMessageBody *body = [[EMCmdMessageBody alloc] initWithAction:@"REVOKE_FLAG"];
    NSDictionary *ext = @{@"msgId":aMessageId};
    NSString *currentUsername = [EMClient sharedClient].currentUsername;
    EMMessage *message = [[EMMessage alloc] initWithConversationID:conversationId
                                                          from:currentUsername
                                                            to:conversationId
                                                          body:body
                                                           ext:ext];
    //发送cmd消息
    [[EMClient sharedClient].chatManager sendMessage:message
                                            progress:nil
                                          completion:^(EMMessage *message, EMError *error) {
                                              if (!error) {
                                                  NSLog(@"发送成功");
                                              }
                                          }];
    
}
```

## 接收透传信息，删除要撤回的消息

#### Android示例

``` java
EMChatManager.getInstance().registerEventListener(new EMEventListener() {
        
    @Override
    public void onEvent(EMNotifierEvent event) {
        switch (event.getEvent()) {
        case EventNewCMDMessage: // CMD消息
        {
            EMMessage message = (EMMessage) event.getData();
            CmdMessageBody cmdMsgBody = (CmdMessageBody) message.getBody();
            String action = cmdMsgBody.action;//获取自定义action
            if(action.equals("REVOKE_FLAG")){
                try {
                    String msgId = message.getStringAttribute("msgId");
                    EMConversation conversation = EMChatManager.getInstance().getConversation(message.getFrom());
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
}, new EMNotifierEvent.Event[] { EMNotifierEvent.Event.EventNewCMDMessage});
```

#### iOS示例

``` objc
// 接收透传消息
- (void)cmdMessagesDidReceive:(NSArray *)aCmdMessages {
    NSMutableArray *revokeMessageIds = nil;
    for (EMMessage *cmdMessage in aCmdMessages)
    {
        EMCmdMessageBody *body = (EMCmdMessageBody *)cmdMessage.body;
        if ([body.action isEqualToString:@"REVOKE_FLAG"]) {
            NSString *revokeMessageId = cmdMessage.ext[@"msgId"];
            BOOL isSuccess = [self removeRevokeMessageWithChatter:cmdMessage.from
                                                 conversationType:(EMConversationType)cmdMessage.chatType
                                                        messageId:revokeMessageId];
            if (isSuccess) {
                // update ui，如果需要，可以插入一条“XXX撤回一条消息”
            }
        }
    }
}

// 删除消息
- (BOOL)removeRevokeMessageWithChatter:(NSString *)aChatter
                      conversationType:(EMConversationType)type
                             messageId:(NSString *)messageId{
    
    EMConversation *conversation = [[EMClient sharedClient].chatManager getConversation:aChatter type:type createIfNotExist:YES];
    EMError *error = nil;
    [conversation deleteMessageWithId:messageId error:&error];
    return !error;
}
```

------------------------------------------------------------------------


