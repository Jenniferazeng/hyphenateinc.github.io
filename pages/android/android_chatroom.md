---
title: Android Chatroom
keywords: android
sidebar: android_sidebar
toc: true
permalink: android_chatroom.html
folder: android
---
# Chat room management

-------------------------------------------------- ----------------------

The Agora chat room model supports 5000 members as the maximum. Unlike a group, after a member in the chat room is offline, the server will not send any pushes to this member after it detects that the member is offline.

-Support up to 5000 members;
-Agora’s chat room has three identities: owner, administrator and tourist;
-Support operations such as mute, blacklist, and kick;
-Client invitation is not supported;
-REST invitation is not supported.
-The chat room API is usually a synchronous operation and needs to be executed in a separate thread. If you need to use asynchronous API, please use the corresponding API  with async prefix

The main features of Agora chat room client include:

-Support for querying all APP chat rooms;
-Support for querying chat room details;
-Join the chat room;
-quit the chat room;
-Client API
     They are all operated by `ChatRoomManager(ChatClient.getInstance().chatroomManager())`.

### Server-side API

For REST operations related to server-side chat rooms, please refer to [chat room management](/im/server/basics/chatroom)

### Join the chat room

``` java
//roomId is the chat room ID
ChatClient.getInstance().chatroomManager().joinChatRoom(roomId, new ValueCallBack<ChatRoom>() {

        @Override
        public void onSuccess(ChatRoom value) {
            //Join the chat room successfully
        }

        @Override
        public void onError(final int error, String errorMsg) {
            //Failed to join the chat room
        }
    });
```

**Note: **For the chat room model, please be sure to wait until the Join callback is successful before you initialize conversation.

### Leave the chat room

``` java
ChatClient.getInstance().chatroomManager().leaveChatRoom(toChatUsername);
```

This method is asynchronous and will not block the current thread. There is no callback for this method. The reason is that if you exit the chat room in any situation, the SDK guarantees that the exit is successful, regardless of whether there is an Internet error or no Internet exit. For the chat room model, the leave method is usually called when exiting the conversation page.

### Create a chat room

``` java
/**
 * \~chinese
 * Create a chat room, the maximum number of people in the chat room is 5,000. Only specific users have permission to create chat rooms.
 * @param subject 							name
 * @param description 						description
 * @param welcomeMessage 					The message of inviting members to join the chat room
 * @param maxUserCount 						The maximum number of members allowed to join the chat room
 * @param members 							The list of members invited to join the chat room
 * @return ChatRoom 						chat room
 * @throws ChatException
 */
ChatClient.getInstance().chatroomManager().createChatRoom(String subject, String description, String welcomeMessage,
                                 int maxUserCount, List<String> members);
```

### Destroy the chat room

``` java
/**
 * Destroy the chat room, need owner's permission, synchronization method
 * @param chatRoomId
 * @throws ChatException
 */
ChatClient.getInstance().chatroomManager().destroyChatRoom(String chatRoomId);
```

### Get the list of chat rooms

``` java
CursorResult<ChatRoom> result = ChatClient.getInstance().chatroomManager().fetchPublicChatRoomsFromServer(pageSize, cursor)
```

parameter:

-pageSize: the item got this time
-cursor: the cursor ID needed in the background, get the pageSize item according to this ID, and pass null for the first time

return value:

CursorResult\<ChatRoom> contains the returned cursor and List\<ChatRoom> inside

`code`

### Get chat room details

``` java
ChatClient.getInstance().chatroomManager().fetchChatRoomFromServer(roomId)
```

``` java
room.getName();//Chat room name
room.getId();//Chat room id
room.getDescription();//Chat room description
room.getOwner();//Chat room creator
.
.
.
```

Refer to [API document](http://www.easemob.com/apidoc/android/chat3.0/classcom_1_1hyphenate_1_1chat_1_1_e_m_chat_room.html)

roomId chat room id

fetchMembers whether to fetch chat room members

``` java
ChatClient.getInstance().chatroomManager().fetchChatRoomFromServer(roomId, fetchMembers)
```

### Modify chat room's name｜Description

``` java
// Modify the name of the chat room
ChatClient.getInstance().chatroomManager().changeChatRoomSubject(String chatRoomId, String newSubject);

// Modify the description of the chat room
ChatClient.getInstance().chatroomManager().changeChatroomDescription(String chatRoomId, String newDescription));

```

### Chat room mute

#### mute members

``` java
/**
 * \~chinese
 * muteroom members, the chat room owner or administrator permissions are required
 * @param chatRoomId
 * @param muteMembers 		muted user list
 * @param duration 			The duration of silence, in milliseconds
 * @return 					chat room
 * @throws ChatException
 */
ChatClient.getInstance().chatroomManager().muteChatRoomMembers(String chatRoomId, List<String> muteMembers, long duration);
```

#### Unmute chat room members

``` java
/**
  * \~chinese
  * To unmute, the owner or administrator of the chat room is required
  * @param 		chatRoomId
  * @param 		members
  * @return
  * @throws ChatException
  */
ChatClient.getInstance().chatroomManager().unMuteChatRoomMembers(String chatRoomId, List<String> members);
```

#### Get the list of muted members in the chat room by page

``` java
/**
 * \~chinese
 * To get the muted members list of the chat room, the owner or administrator authority is required
 * @param chatRoomId
 * @param pageNum 			 get the list of muted members by page
 * @param pageSize 			 the number of muted members contained by each page
 * @return Map.entry.key     is the mute member id, Map.entry.value is the time when the mute action will be expired, the unit is milliseconds
 * @throws ChatException
 */
Map<String, Long> mutes = ChatClient.getInstance().chatroomManager().fetchChatRoomMuteList(String chatRoomId, int pageNum, int pageSize);
```

### Turn on and off all mute

The owner and administrator can turn on and off the mute of all members.

``` java
     /**
     * \~chinese
     * Mute all members
     * @param chatRoomId
     */
    public void muteAllMembers(final String chatRoomId, final ValueCallBack<EMGroup> callBack)
    
    /**
     * \~chinese
     * unmute all members
     * @param chatRoomId
     */
    public void unmuteAllMembers(final String chatRoomId, final ValueCallBack<EMGroup> callBack)
```

### Whitelist Management

You can add users to the whitelist. The user whitelist takes effect when the administrator turns on all members' mute, and can allow whitelisted users to send messages.
In addition, you can remove the user from the whitelist, check whether you are in the whitelist, and get the whitelist list.

``` java
 /**
      * \~chinese
      * Add users to the whitelist
      * @param chatRoomId
      * @param members    member id list
      */
     public void addToChatRoomWhiteList(final String chatRoomId, final List<String> members, final CallBack callBack)

         /**
      * \~chinese
      * Remove users from the whitelist
      * @param chatRoomId
      * @param members    member id list
      */
     public void removeFromChatRoomWhiteList(final String chatRoomId, final List<String> members, final CallBack callBack)

         /**
      * \~chinese
      * Check if you are in the whitelist
      * @param groupId    group id
      */
     public void checkIfInChatRoomWhiteList(final String chatRoomId, ValueCallBack<Boolean> callBack)

         /**
      * \~chinese
      * Get the list of whitelisted members from the server
      * @param groupId   group id
      */
     public void fetchChatRoomWhiteList(final String chatRoomId, final ValueCallBack<List<String>> callBack)
```

### Chat Room Manager

``` java
    //Add chat room manager
    ChatRoom chatRoom = ChatClient.getInstance().chatroomManager().addChatRoomAdmin(String chatRoomId, String admin);
    
    //Delete chat room manager
    ChatRoom chatRoom = ChatClient.getInstance().chatroomManager().removeChatRoomAdmin(String chatRoomId, String admin);
    
    //To get the list of administrators, you need to get the group details first
    ChatRoom chatRoom = ChatClient.getInstance().chatroomManager().fetchChatRoomFromServer(String roomId);
    List<String> adminList = chatroom.getAdminList();
```

### Get chat room members by page

``` java
/**
     * \~chinese
     * Get the chat room member list. When getting the last page member list, ValueCallBack.getCursor() returns an null string.
     * @param chatRoomId
     * @param cursor
     * @param pageSize
     * @return
     * @throws ChatException
     */
    public CursorResult<String> fetchChatRoomMembers(String chatRoomId, String cursor, int pageSize);
```

### Delete chat room members

``` java
/**
 * To delete a chat room member, the owner or administrator's permission is required
 * @param     chatRoomId
 * @param     members
 * @return
 * @throws ChatException
 */
ChatRoom chatRoom = ChatClient.getInstance().chatroomManager().removeChatRoomMembers(String chatRoomId, List<String> members);
```

### Chat room blacklist

``` java
// Add members to the blacklist, forbid members from continuing to join the chat room, need owner or administrator permission
ChatRoom chatroom = ChatClient.getInstance().chatroomManager().blockChatroomMembers(String chatRoomId, List<String> members);

//To remove a member from the blacklist, the owner or administrator permission is required
ChatRoom chatroom = ChatClient.getInstance().chatroomManager().unblockChatRoomMembers(String chatRoomId, List<String> members);

//get the blacklist of chat rooms by page
List<String> blackList = ChatClient.getInstance().chatroomManager().fetchChatRoomBlackList(String chatRoomId, int pageNum, int pageSize);

```

### Get chat room announcement

``` java
ChatClient.getInstance().chatroomManager().fetchChatRoomAnnouncement(roomId);
```

It is also possible to get the message push of the chat room announcement through the chat room listening interface. See [Register for chat room listening](http://docs-im.easemob.com/im/android/basics/chatroom#Register for chat room monitoring)

### Update chat room announcement

``` java
ChatClient.getInstance().chatroomManager().updateChatRoomAnnouncement(chatRoomId, announcement);

```

It is also possible to get the message push of the chat room announcement through the chat room listening interface. See [Register for chat room listening](http://docs-im.easemob.com/im/android/basics/chatroom#Register for chat room monitoring)

### Register for chat room listening

Register listener on the conversation page to listen for members being kicked and chat rooms being deleted.

``` java
ChatClient.getInstance().chatroomManager().addChatRoomChangeListener(new ChatRoomChangeListener(){

    @Override
    public void onChatRoomDestroyed(String roomId, String roomName) {
    
    }
    
    @Override
    public void onMemberJoined(String roomId, String participant) {
    }
    
    @Override
    public void onMemberExited(String roomId, String roomName, String participant) {
        
    }
    
    @Override
    public void onMemberKicked(String roomId, String roomName, String participant) {
        
    }
    
    @Override
    public void onMuteListAdded(final String chatRoomId, final List<String> mutes, final long expireTime) {
    
    }
    
    @Override
    public void onMuteListRemoved(final String chatRoomId, final List<String> mutes) {
    
    }
    
     @Override
     public void onWhiteListAdded(final String chatRoomId, final List<String> whitelist){
        
     }
       
     @Override
     public void onWhiteListRemoved(final String chatRoomId, final List<String> whitelist) {
         
     }
    
    @Override
    public void onAllMemberMuteStateChanged(final String chatRoomId, final boolean isMuted) {
        
    }
    
    @Override
    public void onAdminAdded(final String chatRoomId, final String admin) {
    
    }
    
    @Override
    public void onAdminRemoved(final String chatRoomId, final String admin) {
    
    }
    
    @Override
    public void onOwnerChanged(final String chatRoomId, final String newOwner, final String oldOwner) {
    
    }
    @Override
    public void onAnnouncementChanged(String chatRoomId, final String announcement) {
            
    }

});
```

### Remove chat room listening

``` java
ChatClient.getInstance().chatroomManager().removeChatRoomChangeListener(chatroomListener)
```

-------------------------------------------------- ----------------------

\<WRAP group> \<WRAP half column>
Previous page: [Group Management](/im/android/basics/group) \</WRAP>

\<WRAP half column> Next page: [1V1 real-time call](/im/android/basics/audiovideo)
\</WRAP> \</WRAP>

