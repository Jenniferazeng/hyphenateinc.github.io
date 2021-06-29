---
title: Android Message Roaming
keywords: android
sidebar: android_sidebar
toc: true
permalink: android_message_roaming.html
folder: android
---
# Message Roaming

You can pull historical messages from the server to the local, so that it can synchronize messages when users switch to different device (`This function is a value-added service, you need to contact Agora business to activate`)

This method belongs to `ChatManager` class, which is called by `ChatClient.getInstance().chatManager()`, and the usage method refers to `loadMoreRoamingMessages()` method of the `EaseChatFragment` class in the demo

``` java
   /**
     * Get historical information from the server
     *
     * @param conversationId    conversation name
     * @param type        conversation type
     * @param pageSize     geted page size (up to 50 items at a time)
     * @param startMsgId    The starting message id of the roaming message, if it is null, it will be retrieved forward, starting from the latest message 
     * @return      return the message list and the Cursor used to continue to get history
     */
    public CursorResult<ChatMessage> fetchHistoryMessages(String conversationId, ConversationType type, int pageSize,String startMsgId);

    /**
     * Get history from the server
     *
     * @param conversationId    conversation name
     * @param type          conversation type
     * @param pageSize     geted page size
     * @param startMsgId    The starting message id of the roaming message, if it is null, it will be retrieved forward, starting from the latest message
     * @param callBack     return the message list and the Cursor used to continue to get historical messages
     */
    public void asyncFetchHistoryMessage(String conversationId, ConversationType type, int pageSize, String startMsgId, ValueCallBack<CursorResult<ChatMessage>> callBack)
```

------------------------------------------------------------------------
