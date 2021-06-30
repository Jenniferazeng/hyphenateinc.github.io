---
title: web Chatroom
keywords: web
sidebar: web_sidebar
toc: true
permalink: web_chatroom.html
folder: web
---

# Chat room management

Agora Web IM SDK supports the integration of chat room management functions. The following operations can be executed after the integration: 

-   Get chat room list

-   Join the chat room 

-   Exit the chat room

-   Send message 

-   Receive and process messages

-   Related chat room callbacks

Through these operations, you can use any combinations to achieve IM requirements in a variety of scenarios.

## Create chat room 

Creating chat room requires Super administrator rights, 
Call the \"createChatRoom\" function to create a chat room, the example is as follows: 

``` javascript
let options = {
    name: 'chatRoomName', // Chat room name
    description: 'description', // Chat room description
    maxusers: 200, // The maximum number of chat room members (including the creator of the chat room), the default value is 200, the maximum value is 5000
    members: ['user1', 'user2'] // Members of the chat room, this attribute is optional, but if you add this option, there is at least one array element 
}
conn.createChatRoom(options).then((res) => {
    console.log(res)
})
```

------------------------------------------------------------------------

## Destroy chat room 

Destroy the chat room requires[Super administrator rights]
(http://docs-im.easemob.com/im/server/basics/chatroom#%E7%AE%A1%E7%90%86%E8%B6%85%E7%BA%A7%E7%AE%A1%E7%90%86%E5%91%98)，
Call the \"destroyChatRoom\" function to destroy the chat room, the example is as follows: 

``` javascript
let options = {
    chatRoomId: '1234567890'   // Chat room id
}
conn.destroyChatRoom(options).then((res) => {
    console.log(res)
})
```

## Get chat room lists

Call the `getChatRooms` function to get chat room list, the example is as follows: 

``` javascript
// List all chat rooms, support paging query 
let option = {
    pagenum: 1,                                 // Number of pages
    pagesize: 20                                // Number per page
};
conn.getChatRooms(option).then((res) => {
    console.log(res)
})

```

------------------------------------------------------------------------

## Get chat room details

Call the `getChatRoomDetails` function to get the chat room details, an example is as follows: 

``` javascript
let options = {
    chatRoomId: 'chatRoomId'   // Chat room id
}
conn.getChatRoomDetails(options).then((res) => {
    console.log(res)
})
```

## Change chat room details

Call the `modifyChatRoom` function to change the chat room details, an example is as follows: 

``` javascript
let options = {
    chatRoomId: 'chatRoomId',     // Chat room id
    chatRoomName: 'chatRoomName', // Chat room name
    description: 'description',   // Chat room description
    maxusers: 200                 // Maximum number of chat rooms
}
conn.modifyChatRoom(options).then((res) => {
    console.log(res)
})
```

## Join the chat room

Call `joinChatRoom` to join the chat room, an example is as follows: 

``` javascript
let options = {
    roomId: 'roomId',   // Chat room id
    message: 'reason'   // Reason (optional parameter) 
}
conn.joinChatRoom(options).then((res) => {
    console.log(res)
})

```

------------------------------------------------------------------------

## Exit the chat room

Call `quitChatRoom` to exit the chat room, an example is as follows: 

``` javascript
let options = {
    roomId: 'roomId' // Chat room id
}
conn.quitChatRoom(options).then((res) => {
    console.log(res)
})

```

------------------------------------------------------------------------

## Get chat room members

Call the `listChatRoomMember` tab to get the members of the chat room, an example is as follows: 

``` javascript
let options = {
    pageNum: 1,
    pageSize: 10,
    chatRoomId: 'chatRoomId'
}
conn.listChatRoomMember(options).then((res) => {
    console.log(res)
})

```

## Set up chat room manager

Call `setChatRoomAdmi` to set the chat room administrator, an example is as follows: 

``` javascript
let options = {
    chatRoomId: 'chatRoomId', // Chat room id
    username: 'user1'         // User id 
}
conn.setChatRoomAdmin(options).then((res) => {
    console.log(res)
})
```

## Remove chat room manager

Call `removeChatRoomAdmin` to remove the chat room administrator, an example is as follows:

``` javascript
let options = {
    chatRoomId: 'chatRoomId', // Chat room id
    username: 'user1'         // User id 
}
conn.removeChatRoomAdmin(options).then((res) => {
    console.log(res)
})
```

## Get all managers in the chat room

Call `getChatRoomAdmin` to get all the administrators in the chat room, an example is as follows: 

``` javascript
let options = {
    chatRoomId: 'chatRoomId'   // Chat room id
}
conn.getChatRoomAdmin(options).then((res) => {
    console.log(res)
})
```

## Send message

See[Send message](/web_message.html#send-text-messages-single-chat-extended)。

------------------------------------------------------------------------

## Chat room announcement

The chat room announcement management includes the following processing operations:

-   Upload/modify chat room announcements 

-   Get chat room announcements

Examples of all the operations will be explained individually below. 

### Upload/modify chat room announcement

Call the updateChatRoomAnnouncement function to upload/modify the chat room announcement, an example is as follows: 

``` javascript
let options = {
    roomId: 'roomId',                 // Chat room id   
    announcement: 'hello everyone'    // Announcement content                         
};
conn.updateChatRoomAnnouncement(options)then((res) => {
    console.log(res)
})
```

------------------------------------------------------------------------

### Get chat room announcements

Call the `fetchChatRoomAnnouncement` function to get the chat room announcement, an example is as follows: 

``` javascript
var options = {
    roomId: 'roomId'            // Chat room id                         
};
conn.fetchChatRoomAnnouncement(options).then((res) => {
    console.log(res)
})
```

------------------------------------------------------------------------

## Chat room muted

### Mute members

Call `muteChatRoomMember` to prohibit chat room users from speaking. An example is as follows: 

``` javascript
let options = {
    chatRoomId: "chatRoomId", // Chat room id
    username: 'username',     // id of the banned chat room member
    muteDuration: -1000       // The duration of the ban, in ms. "-1000" means permanent 
};
conn.muteChatRoomMember(options).then((res) => {
    console.log(res)
})
```

------------------------------------------------------------------------

### Unmute members

Call `removeMuteChatRoomMember` to unmute chat room users, an example is as follows: 

``` javascript
let options = {
    chatRoomId: "1000000000000", // Chat room id
    username: 'username'         // id of the banned chat room member
};
conn.removeMuteChatRoomMember(options).then((res) => {
    console.log(res)
})
```

------------------------------------------------------------------------

### Get all banned members of the chat room

Call `getChatRoomMuted` to get all banned members in the chat room, an example is as follows: 

``` javascript
let options = {
    chatRoomId: "chatRoomId" // Chat room id
};
conn.getChatRoomMuted(options).then((res) => {
    console.log(res)
})
```

------------------------------------------------------------------------

### Turn on and off all mute

The owner and administrator can turn on and off the mute of all members.

``` javascript
// Mute all members in the chat room 
let options = {
    chatRoomId: "chatRoomId"  // Chat room id
};
conn.disableSendChatRoomMsg(options).then((res) => {
    console.log(res)
})

// Unmute all members in the chat room
conn.enableSendChatRoomMsg(options).then((res) => {
    console.log(res)
})
```

------------------------------------------------------------------------

### Whitelist management

Users can be added to the whitelist. The user whitelist will become effective once the administrator turns on all members' mute, and the whitelist can run to allow the users on the list to send messages.
In addition, the users on the whitelist can be removed. the users can check whether you are in the whitelist, and get the whitelist list.

``` javascript
// Add user to the whitelist
let options = {
    chatRoomId: "chatRoomId",   // Chat room id
    users: ["user1", "user2"]   // Member id list 
};
conn.addUsersToChatRoomWhitelist(options);

// Remove user from whitelist
let options = {
    chatRoomId: "chatRoomId",  // Group id 
    userName: "user"           // Member to be removed
}
conn.rmUsersFromChatRoomWhitelist(options);

// Get the list of whitelisted members from the server
let options = {
    chatRoomId: "chatRoomId"   // Chat room id
}
conn.getChatRoomWhitelist(options);

// Query whether a member is a whitelist user, operation authority: app admin can query all users; app user can query themselves 
let options = {
    chatRoomId: "chatRoomId", // Chat room id
    userName: "user"          // Member to be queried
}
conn.isChatRoomWhiteUser(options);
```

------------------------------------------------------------------------

## Blacklist management

Blacklist management includes the following  operations:

-   Add members to the group blacklist (single)

-   Add members to the group blacklist (bulk)

-   Remove members from group blacklist (single)

-   Remove members from group blacklist (bulk)

-   Get blacklist of chat rooms

Examples of all operations will be explained individually below.

### Add members to the chat room blacklist (single)

Call chatRoomBlockSingle to add a single member to the chat room blacklist, an example is as follows: 

``` javascript
let options = {
    chatRoomId: 'chatRoomId',       // Chat room id
    username: 'username'            // User name to be added to the blacklist 
};
conn.chatRoomBlockSingle(options);
```

------------------------------------------------------------------------

### Add members to the chat room blacklist (bulk)

Call chatRoomBlockMulti to add a batches of member to the chat room blacklist, an example is as follows: 

``` javascript
let options = {
    chatRoomId: 'chatRoomId',          // Chat room id
    usernames: ['user1', 'user2']      // Array of user ids
};
conn.chatRoomBlockMulti(options);
```

------------------------------------------------------------------------

### Remove members from the chat room blacklist (single)

Call `removeChatRoomBlockSingle` to remove a single member from the chat room blacklist, an example is as follows：

``` javascript
let options = {
    chatRoomId: "chatRoomId",                     // Group id               
    username: "user"                              // Username that needs to be removed 
}
conn.removeChatRoomBlockSingle(options);
```

------------------------------------------------------------------------

### Remove members from the chat room blacklist (bulk)

Call `removeChatRoomBlockMulti` to remove members from the chat room blacklist in batches. An example is as follows:
``` javascript
let options = {
    chatRoomId: "chatRoomId",                      // Chat room id
    usernames: ["user1", "user2"]                  // Array of usernames to be removed 
}
conn.removeChatRoomBlockMulti(options);
```

------------------------------------------------------------------------

### Get blacklist of chat rooms

Call `getChatRoomBlacklistNew` to get the chat room blacklist, an example is as follows: 

``` javascript
let options = {
    chatRoomId: "chatRoomId",                    // Chat room id
};
conn.getChatRoomBlacklistNew(options);
```

------------------------------------------------------------------------

## Receive and process messages

-   Group chat receive and process messages are the same as single chat; 

-   The message body and the single chat message are distinguished according to the type of message;

-   Single chat: chat, group chat: groupchat, and chat room: chatroom; 

-   according to the types of the messages, they will be processed differently.

## Chat room event monitor

You can monitor chat room related events in the registered monitoring event onPresence:

``` javascript
conn.listen({
  onPresence: function(msg){
    switch(msg.type){
    case 'rmChatRoomMute':
      // Release the one-click muting in chat rooms
      break;
    case 'muteChatRoom':
      // One-click muting in chat rooms 
      break;
    case 'rmUserFromChatRoomWhiteList':
      // Delete members from the chat room whitelist
      break;
    case 'addUserToChatRoomWhiteList':
      // Add members to chat room whitelist
      break;
    case 'deleteFile':
      // Delete chat room files
      break;
    case 'uploadFile':
      // Upload chat room files
      break;
    case 'deleteAnnouncement':
      // Delete chat room announcement
      break;
    case 'updateAnnouncement':
      // Update chat room announcement 
      break;
    case 'removeMute':
      // Unmute
      break;
    case 'addMute':
      // Mute
      break;
    case 'removeAdmin':
      // Remove admin
      break;
    case 'addAdmin':
      // Add admin
      break;
    case 'changeOwner':
      // Change the owner of the chat room
      break;
    case 'leaveChatRoom':
      // Leave the chat room
      break;
    case 'memberJoinChatRoomSuccess':
      // Join the chat room
      break;
    case 'leave':
      // Leave the group
      break;
    case 'join':
      // Join the group
      break;
    default:
      break;
  }}
})
```
