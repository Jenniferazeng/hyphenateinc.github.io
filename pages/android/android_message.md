---
title: Message
keywords: android
sidebar: android_sidebar
toc: true
permalink: android_message.html
folder: android
---

## Send a message

Send text, voice, picture, location and other messages (common for single chat/group chat).

### Send text message

``` java
//reate a text message. content is the text content of the message, and toChatUsername is the id of the other party user or group chat.
ChatMessage message = ChatMessage.createTxtSendMessage(content, toChatUsername);
//If it is a group chat, set the chat type. Default is single chat
if (chatType == CHATTYPE_GROUP)
    message.setChatType(ChatType.GroupChat);
//Send a message
ChatClient.getInstance().chatManager().sendMessage(message);
```

### Send emoji message

Sending emoticons is essentially sending text messages. After receiving the text message, the receiver first queries whether the text message is an emoticon message, and if it is, the text message is displayed as a corresponding emoticon picture. You can refer to [emoji list](https://unicode.org/emoji/charts/full-emoji-list.html) to map emoji pictures and corresponding text strings. You can also maintain the mapping between emoticons and text strings by yourself.

``` java
//Create an emoticon message. The emoticon message is essentially a text message. emojiCode is the text string corresponding to the emoji picture, and toChatUsername is the id of the other party user or group chat, all in the following text
ChatMessage message = ChatMessage.createTxtSendMessage(emojiCode, toChatUsername);
//If it is a group chat, set the chattype, the default is single chat
if (chatType == CHATTYPE_GROUP)
     message.setChatType(ChatType.GroupChat);
//Send a message
ChatClient.getInstance().chatManager().sendMessage(message);
```

### Send voice message

``` java
//voiceUri is the local resource identifier of the voice file, length is the recording time (seconds)
ChatMessage message = ChatMessage.createVoiceSendMessage(voiceUri, length, toChatUsername);
//If it is a group chat, set the chattype, the default is single chat
if (chatType == CHATTYPE_GROUP)
     message.setChatType(ChatType.GroupChat);
ChatClient.getInstance().chatManager().sendMessage(message);
```

After sending successfully, get the voice message attachment:

```
    VoiceMessageBody voiceBody = (VoiceMessageBody) msg.getBody();
    //Get the address of the voice file on the server
    String voiceRemoteUrl = voiceBody.getRemoteUrl();
    //Resource path of local voice file
    Uri voiceLocalUri = voiceBody.getLocalUri();
```
    

### Send video message

``` java
//videoLocalUri is the video local resource identifier, thumbLocalUri is the video preview image path, videoLength is the video time length
ChatMessage message = ChatMessage.createVideoSendMessage(videoLocalUri, thumbLocalUri, videoLength, toChatUsername);
//If it is a group chat, set the chat type, the default is single chat
if (chatType == CHATTYPE_GROUP)
   message.setChatType(ChatType.GroupChat);
ChatClient.getInstance().chatManager().sendMessage(message);
```

After sending successfully, get the thumbnail of video message and attachment

    VideoMessageBody videoBody = (VideoMessageBody) message.getBody();
    //Get the path of the video file on the server
    String videoRemoteUrl = videoBody.getRemoteUrl();
    //Get the path of the thumbnail on the server
    String thumbnailUrl = videoBody.getThumbnailUrl();
    //The resource path of the local video file
    Uri videoLocalUri = videoBody.getLocalUri();
    //Local video thumbnail resource path
    Uri localThumbUri = videoBody.getLocalThumbUri();


### Send picture message

``` java
//imageUri is the image local resource identifier, false means the original image will not be sent (by default the image over 100k will be compressed and sent to the other party), you need to send the original image to true
ChatMessage.createImageSendMessage(imageUri, false, toChatUsername);
//If it is a group chat, set the chattype, the default is single chat
if (chatType == CHATTYPE_GROUP)
    message.setChatType(ChatType.GroupChat);
ChatClient.getInstance().chatManager().sendMessage(message);
```

After sending successfully, get the thumbnail of image message and attachment

    ImageMessageBody imgBody = (ImageMessageBody) message.getBody();
    //Get the path of the image file on the server
    String imgRemoteUrl = imgBody.getRemoteUrl();
    //Get the path of the image thumbnail on the server
    String thumbnailUrl = imgBody.getThumbnailUrl();
    //The resource path of the local image file
    Uri imgLocalUri = imgBody.getLocalUri();
    //Local image thumbnail resource path
    Uri thumbnailLocalUri = imgBody.thumbnailLocalUri();


### Send geolocation message

``` java
//latitude is latitude, longitude is longitude, locationAddress is the detailed location content
ChatMessage message = ChatMessage.createLocationSendMessage(latitude, longitude, locationAddress, toChatUsername);
//If it is a group chat, set the chat type, the default is single chat
if (chatType == CHATTYPE_GROUP)
    message.setChatType(ChatType.GroupChat);
ChatClient.getInstance().chatManager().sendMessage(message);
```

### Send file message

``` java
//fileLocalUri is a local resource identifier
ChatMessage message = ChatMessage.createFileSendMessage(fileLocalUri, toChatUsername);
// If it is a group chat, set the chattype, the default is single chat
if (chatType == CHATTYPE_GROUP)
    message.setChatType(ChatType.GroupChat);
ChatClient.getInstance().chatManager().sendMessage(message);
```

After sending successfully, get the file message attachment

    NormalFileMessageBody fileMessageBody = (NormalFileMessageBody) message.getBody();
    //Get the path of the file on the server
    String fileRemoteUrl = fileMessageBody.getRemoteUrl();
    //Resource path of local file
    Uri fileLocalUri = fileMessageBody.getLocalUri();


### Send pass-through message

What can the pass-through message do: update the avatar, nickname, etc. The pass-through message can be understood as a command. By sending this command to the other party, telling the other party the action to be done, the received message can be customized to process a message. (pass-through messages will not be stored in the local database, so they will not be displayed on the UI). In addition, the actions beginning with "em\_" and "easemob::" are internal reserved fields. Be careful not to use them

``` java
ChatMessage cmdMsg = ChatMessage.createSendMessage(ChatMessage.Type.CMD);

//Support single chat and group chat, the default single chat, if it is a group chat, add the following line
cmdMsg.setChatType(ChatType.GroupChat)
String action="action1";//action can be customized
CmdMessageBody cmdBody = new CmdMessageBody(action);
String toUsername = "test1";//Send to someone
cmdMsg.setTo(toUsername);
cmdMsg.addBody(cmdBody);
ChatClient.getInstance().chatManager().sendMessage(cmdMsg);
```

### Send custom type messages

Except for the above types of messages, users can define their own message types to facilitate the user's business processing. The custom message type supports users to set a message type name by themselves, so that users can add a variety of custom messages. The content part of the custom message is in key and value format, and the user needs to add and parse the content by himself.

``` java
ChatMessage customMessage = ChatMessage.createSendMessage(ChatMessage.Type.CUSTOM);
// event is a custom message event that needs to be delivered, such as a gift message, you can set event = "gift"
CustomMessageBody customBody = new CustomMessageBody(event);
// The params type is Map<String, String>
customBody.setParams(params);
customMessage.addBody(customBody);
// to refers to the other party's chat id (or group id, chat room id)
customMessage.setTo(to);
// If it is a group chat, set the chat type, the default is single chat
customMessage.setChatType(chatType);
ChatClient.getInstance().chatManager().sendMessage(customMessage);
```

### Send extended message

When the message types provided by the SDK do not meet the requirements, developers can use the text, voice, picture, location and other message types provided by SDK  to generate the message type you need.

Here is an extended text message. If this custom message needs to use voice or pictures, it can be extended from voice, picture messages, or location messages.

``` java
ChatMessage message = ChatMessage.createTxtSendMessage(content, toChatUsername);
 
// Add your own specific attributes
message.setAttribute("attribute1", "value");
message.setAttribute("attribute2", true);
...
ChatClient.getInstance().chatManager().sendMessage(message);

//Get extended attributes when receiving messages
//Get a custom attribute, the second parameter is the default value returned when there is no such defined attribute
message.getStringAttribute("attribute1",null);
message.getBooleanAttribute("attribute2", false);
...
```

## Receive messages

Receive messages by registering for message listeners.

``` java
ChatClient.getInstance().chatManager().addMessageListener(msgListener);
MessageListener msgListener = new MessageListener() {
    
    @Override
    public void onMessageReceived(List<ChatMessage> messages) {
        //Received the news
    }
    
    @Override
    public void onCmdMessageReceived(List<ChatMessage> messages) {
        //Receive a pass-through message
    }
    
    @Override
    public void onMessageRead(List<ChatMessage> messages) {
        //Receive a read receipt
    }
    
    @Override
    public void onMessageDelivered(List<ChatMessage> message) {
        //Receipt of send receipt
    }
       @Override
    public void onMessageRecalled(List<ChatMessage> messages) {
        //The message was withdrawn
    }
    
    @Override
    public void onMessageChanged(ChatMessage message, Object change) {
        //Message status changes
    }
};

Remember to remove the listener when you don’t need it. For example, in onDestroy() of the activity, ChatClient.getInstance().chatManager().removeMessageListener(msgListener);
```

## Download thumbnails and attachments

### Download thumbnail

If automatic download is set,  ChatClient.getInstance().getOptions().getAutodownloadThumbnail() is true, the SDK will download the thumbnail after receiving the message;\
If the automatic download is not set, you need to call `ChatClient.getInstance().chatManager().downloadThumbnail(message)` to download. \
After the download is completed, call thumbnailLocalUri() of the corresponding message body to get the thumbnail path. \
E.g:

    ImageMessageBody imgBody = (ImageMessageBody) message.getBody();
    //resource path of local image thumbnail 
    Uri thumbnailLocalUri = imgBody.thumbnailLocalUri();

### Download attachments

The method of downloading attachments is: `ChatClient.getInstance().chatManager().downloadAttachment(message)`;\
After the download is completed, call getLocalUri() of the corresponding message body to get the attachment path. \
E.g:

    ImageMessageBody imgBody = (ImageMessageBody) message.getBody();
    //The resource path of the local image file
    Uri imgLocalUri = imgBody.getLocalUri();

## listen message status

Set the sending and receiving status of the message through message.\
**Note:** You need to set up this callback listener before sendMessage

``` java
message.setMessageStatusCallback(new CallBack(){});
```

## Get chat history

``` java
Conversation conversation = ChatClient.getInstance().chatManager().getConversation(username);
//Get all messages of this conversation
List<ChatMessage> messages = conversation.getAllMessages();
//When the SDK is initialized, the loaded chat records are 20. When it reaches the top, you need to go to the DB to get more
//Get the pagesize messages before startMsgId, the messages SDK got by this method will be automatically stored in this conversation, and there is no need to add the got messages to the conversation in the APP again
List<ChatMessage> messages = conversation.loadMoreMsgFromDB(startMsgId, pagesize);
```

## Get the number of unread messages in the conversation

``` java
Conversation conversation = ChatClient.getInstance().chatManager().getConversation(username);
conversation.getUnreadMsgCount();
```

## Get the number of all unread messages

``` java
ChatClient.getInstance().chatManager().getUnreadMessageCount();
```

## Clear the number of unread messages

``` java
Conversation conversation = ChatClient.getInstance().chatManager().getConversation(username);
//clear unread message in the specified conversation 
conversation.markAllMessagesAsRead();
//Set a message as read
conversation.markMessageAsRead(messageId);
//Clear all unread messages
ChatClient.getInstance().chatManager().markAllConversationsAsRead();
```

## Get the total number of conversation messages

``` java
Conversation conversation = ChatClient.getInstance().chatManager().getConversation(username);
//Get the number of all local messages of this conversation
conversation.getAllMsgCount();
//If you just get the number of current messages in memory, call
conversation.getAllMessages().size();
```

## Message read receipt

The message read receipt function is currently only available for single chat (ChatType.Chat). The recommended solution is the combination of conversation read receipt(conversation
ack) and single message read acknowledgement (read ack), which can reduce the amount of read ack messages sent. \
**`Note: The group message read receipt function is a value-added service. For specific usage, please skip to the group message read receipt. `**

### Send a read receipt 

It is recommended that you first send a  conversation read receipt (conversation ack) when you enter the conversation.

``` java
try {
    ChatClient.getInstance().chatManager().ackConversationRead(conversationId);
} catch (ChatException e) {
    e.printStackTrace();
}
```

On the conversation page, when a message is received, a message read receipt(read ack) can be sent according to the message type, as shown below

``` java
ChatClient.getInstance().chatManager().addMessageListener(new MessageListener() {
    ......
    
    @Override
    public void onMessageReceived(List<ChatMessage> messages) {
        ......
        sendReadAck(message);
        ......
    }
    
    ......
});

/**
* Send a read receipt
* @param message
*/
public void sendReadAck(ChatMessage message) {
    // is a received message, read ack message has not been sent and it is a single chat
    if(message.direct() == ChatMessage.Direct.RECEIVE
            && !message.isAcked()
            && message.getChatType() == ChatMessage.ChatType.Chat) {
        ChatMessage.Type type = message.getType();
        //Videos, voices and files need to be clicked and then send, this can be adjusted according to needs
        if(type == ChatMessage.Type.VIDEO || type == ChatMessage.Type.VOICE || type == ChatMessage.Type.FILE) {
            return;
        }
        try {
            ChatClient.getInstance().chatManager().ackMessageRead(message.getFrom(), message.getMsgId());
        } catch (ChatException e) {
            e.printStackTrace();
        }
    }
}
```

### listen the read receipt callback

(1) Set up the listening of the conversation read receipt

``` java
ChatClient.getInstance().chatManager().addConversationListener(new ConversationListener() {
    ......

    @Override
    public void onConversationRead(String from, String to) {
        //Add logic such as refresh page notification
    }
});
```

Received conversation read receipt (channel ack) After the callback, the SDK will internally set the conversation-related messages to be read by the other party. After receiving this callback, the developer needs to execute operations such as page refresh. \

(2) Set up the listening of the message read receipt

``` java
ChatClient.getInstance().chatManager().addMessageListener(new MessageListener() {
    ......

    @Override
    public void onMessageRead(List<ChatMessage> messages) {
        //Add logic such as refresh message
    }

    ......
});
```

Received read
After the ack callback, the SDK will set the message as read by the other party internally, and the developer needs to execute operations such as message refresh after receiving this callback.

**Note: Use the `isAcked()` method to determine whether the other party has read the message. Return true to indicate that the other party has read, and the developer can display and refresh the UI interface according to this field.**

## Get history message records by page

``` java
try {
    ChatClient.getInstance().chatManager().fetchHistoryMessages(
            toChatUsername, EaseCommonUtils.getConversationType(chatType), pagesize, "");
    final List<ChatMessage> msgs = conversation.getAllMessages();
    int msgCount = msgs != null ? msgs.size() : 0;
    if (msgCount < conversation.getAllMsgCount() && msgCount < pagesize) {
        String msgId = null;
        if (msgs != null && msgs.size() > 0) {
            msgId = msgs.get(0).getMsgId();
        }
        conversation.loadMoreMsgFromDB(msgId, pagesize - msgCount);
    }
    messageList.refreshSelectLast();
} catch (ChatException e) {
    e.printStackTrace();
}
```

## Get all local conversations

``` java
Map<String, Conversation> conversations = ChatClient.getInstance().chatManager().getAllConversations();
```

occasionally, if the size of conversations returned is 0, it is very likely that `ChatClient.getInstance().chatManager().loadAllConversations()` is not called or the calling sequence is wrong. For specific usage, please refer to [Login](30androidsdkbasics#Login) chapter.

## Get conversation from server

`Need to contact Sales for activation`

It is recommended that this api should be called when the application is installed for the first time or when there is no local conversation. At other times, you can use the local conversation api. By default, up to 100 pieces of data can be returned. \
To use this function, you need to contact your Agora Sales to activate it. (You can scan the QR code to contact your Sales manager on the home page of the Agora Communication Cloud Management Console)

    ChatClient.getInstance().chatManager().asyncFetchConversationsFromServer(new ValueCallBack<Map<String, Conversation>>() {
        @Override
        public void onSuccess(Map<String, Conversation> value) {
            // the processing logic of the getting conversation is successful
        }
        @Override
        public void onError(int error, String errorMsg) {
            // processing logic of failure toget conversation  
        }
    });

## Delete conversation and delete chat history

``` java
//Delete a conversation with a user. If you need to keep the chat history, please deliver false
ChatClient.getInstance().chatManager().deleteConversation(username, true);
//Delete a chat history of the current conversation
Conversation conversation = ChatClient.getInstance().chatManager().getConversation(username);
conversation.removeMessage(deleteMsg.msgId);
```

## Search conversation messages based on keywords

``` java
List<ChatMessage> messages = conversation.searchMsgFromDB(keywords, timeStamp, maxCount, from, Conversation.SearchDirection.UP);
```

## Insert message

``` java
//Insert messages based on conversation
Conversation conversation = ChatClient.getInstance().chatManager().getConversation(username);
conversation.insertMessage(message);

//Insert message directly
ChatClient.getInstance().chatManager().saveMessage(message);
```

------------------------------------------------------------------------
