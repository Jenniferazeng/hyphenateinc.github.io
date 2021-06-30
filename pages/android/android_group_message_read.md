---
title: Android Group Message read
keywords: android
sidebar: android_sidebar
toc: true
permalink: android_group_message_read.html
folder: android
---


## Set whether the group message requires a read receipt (value-added service)

When the message is a group message, the message sender (currently the administrator and the group owner) can set whether the message needs a read receipt. If necessary, set the ChatMessage method setIsNeedGroupAck() to YES, and then send it.

     public ChatMessage createDingMessage(String to, String content) {
             ChatMessage message = ChatMessage.createTxtSendMessage(content, to);
             message.setIsNeedGroupAck(true);
             return message;
         }

## Send group message read receipt

``` objc
public void sendAckMessage(ChatMessage message) {
        if (!validateMessage(message)) {
            return;
        }

        if (message.isAcked()) {
            return;
        }

        // May a user login from multiple devices, so do not need to send the ack msg.
        if (ChatClient.getInstance().getCurrentUser().equalsIgnoreCase(message.getFrom())) {
            return;
        }

        try {
            if (message.isNeedGroupAck() && !message.isUnread()) {
                String to = message.conversationId(); // do not user getFrom() here
                String msgId = message.getMsgId();
                ChatClient.getInstance().chatManager().ackGroupMessageRead(to, msgId, ((TextMessageBody)message.getBody()).getMessage());
                message.setUnread(false);
                EMLog.i(TAG, "Send the group ack cmd-type message.");
            }
        } catch (Exception e) {
            EMLog.d(TAG, e.getMessage());
        }
    }
```

After sending the group read receipt, the groupAckCount attribute of the corresponding ChatMessage of the message sender will change accordingly;

## Group message read callback

The group message read callback is in the message listening class MessageListener.

         /**
          * \~chinese
          * Received the read receipt of the group message body, the recipient of the message has read the message.
          */
              void onGroupMessageRead(List<GroupReadAck> groupReadAcks) {
             }

## Get the details of the group message read receipt

If you want to display the list of read receipts for group messages, you can get the details of read receipts through the following interface.

         /**
          * \~chinese
          * get the group message receipt details from the server
          * @param msgId message id
          * @param pageSize getted page size
          * @param startAckId The id of the read receipt. If it is null, start getting from the latest receipt.
          * @return returns the message list and the Cursor used to continue to get the group message receipt
          */
         public void asyncFetchGroupReadAcks(final String msgId, final int pageSize,
                 final String startAckId, final ValueCallBack<CursorResult<GroupReadAck>> callBack) {
    
         }


------------------------------------------------------------------------
