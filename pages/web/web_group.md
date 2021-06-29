---
title: web Group
keywords: web
sidebar: web_sidebar
toc: true
permalink: web_group.html
folder: web
---
# Group

Agora Web IM SDK supports the integration of group's functions. After integration, the following operations can be execute: 

-   Group management 

-   Group member management

-   Add group processing

-   Mute management

-   Blacklist management

-   Blacklist management

Though these operations, you can use any combinations to achieve IM requirements in a variety of scenarios.


**Note**: `1, the number of group owners and administrators cannot exceed 100, which means no more than 99 administrators. 2. The maximum number of group members (including the group owner) is set to 200 by default, and the maximum value is 3000. ` 

------------------------------------------------------------------------

## Group management 

Group management includes the following operations:

-   Get the list of groups that the user has joined

-   Paging access to the public group

-   Create group

-   Get group information

-   Edit group information

-   Remove member

-   Disband group

-   Leave group 

Examples of all the operations will be explained individually below.

### Get the list of groups that the user has joined

Call the `getGroup` function to get the list of all public groups joined by the currently logged-in user, an example is as follows: 

``` javascript
// List all groups joined by the currently logged-in user
conn.getGroup().then((res) => {
    console.log(res)
})
```

------------------------------------------------------------------------

### Paging access to the public group

Call the `listGroups` function to page to obtain a list of all public groups in the APP, an example is as follows: 

``` javascript
let limit = 20,
    cursor = globalCursor;
let options = {
    limit: limit,                                            // Expected number of records per page
    cursor: cursor,                                          // cursor
};
conn.listGroups(options).then((res) => {
    console.log(res)
})
```

**Note：**

-   cursor: If there is data in the next page, the API return value will include 
    this field, pass this field to get the data of the next page, the default value is `null`, when it is `null`, get the data of the first page 

------------------------------------------------------------------------

### Create group 

Call the `createGroupNew` function to create a group, the sample code is as follows:

``` javascript
let options = {
    data: {
        groupname: 'groupName',          // Group name
        desc: 'group description',       // Group description 
        members: ['user1', 'user2'],     // Array of usernames 
        public: true,                    // When pub is true, it is created as a public group
        approval: true,                  // If approval is true, adding a group requires approval; When it is false, adding a group does not require approval 
        allowinvites: allowInvites,      // true: allow group members to invite people to join this group, false: only the group owner can add people to the group. Note that the public group (public: true) does not allow group members to invite others to join this group 
        inviteNeedConfirm: false         // Invite to join the group, whether the invitee needs to be confirmed. true means that the invitee's consent is required to join the group 
    },
    success(res){},
    error(err){},
};
conn.createGroupNew(options).then((res) => {
    console.log(res)
})
```

**Note：**

-   After the group is successfully created, the `onCreateGroup` function will be called in the callback functions 

------------------------------------------------------------------------

### Get group information 

Call `getGroupInfo` to get group details according to the group id, an example is as follows: 

``` javascript
let options = {
    groupId: 'groupId'    // Group id
};
conn.getGroupInfo(options).then((res) => {
    console.log(res)
})
```

------------------------------------------------------------------------

### Edit group information

Only the administrator of the group can modify the group name and group profile, call `modifyGroup` to modify the group information, an example is as follows: 
``` javascript
// Modify group information
let option = {
    groupId: 'groupId',
    groupName: 'ChangeTest',                         // Group name
    description: 'Change group information test'     // Group introduction
};
conn.modifyGroup(option).then((res) => {
    console.log(res)
})
```

**Note：**

-   The ID of the group manager can be obtained when the group is obtained so that the front-end can decide whether to display the modify information button. 

------------------------------------------------------------------------

### Remove group members

Only the administrator of the group can remove group members. Call `removeSingleGroupMember` to remove group members. An example is as follows: 

``` javascript
// Remove group members
let option = {
    groupId: 'groupId',
    username: 'username'                         // Group member name 
};
conn.removeSingleGroupMember(option).then((res) => {
    console.log(res)
})
```

------------------------------------------------------------------------

### Disband group

-   Only the administrator of the group has the rights to kick members out of the group; 

-   All group members withdraw from the group after the group is disbanded.

Call `dissolveGroup` to dissolve the group, an example is as follows: 

``` javascript
// Disband a group
let option = {
    groupId: 'groupId'
};
conn.dissolveGroup(option).then((res) => {
    console.log(res)
})
```

**Note：**

-   The ID of the group manager can be obtained when the group is obtained, so that the front end can decide whether to display the dismiss button. 

------------------------------------------------------------------------

### Leave group 

Group members can voluntarily exit the group, call `quitGroup` to exit the group, an example is as follows: 

``` javascript
// Members voluntarily withdraw from the group 
let option = {
    groupId: 'groupId' 
};
conn.quitGroup(option).then((res) => {
    console.log(res)
})

```

------------------------------------------------------------------------

## Group announcement management

Group announcement management includes the following operations: 

-   Upload/modify group announcement 

-   Get group announcement

Examples of all the operations will be explained individually below. 

### Upload/modify group announcement

Call the updateGroupAnnouncement function to upload/modify group announcements, an example is as follows: 

``` javascript
let options = {
    groupId: 'groupId',                 // Group id   
    announcement: 'announcement'        // Announcement content                    
};
conn.updateGroupAnnouncement(options).then((res) => {
    console.log(res)
})
```

------------------------------------------------------------------------

### Get group announcement

Call the fetchGroupAnnouncement function to get the group announcement, an example is as follows: 

``` javascript
let options = {
    groupId: 'groupId'            // Group id                          
};
conn.fetchGroupAnnouncement(options).then((res) => {
    console.log(res)
})
```

------------------------------------------------------------------------

## Group file management

Group file management includes the following operations:

-   Upload group file

-   Download group file

-   Delete group file

-   Get group file list

Examples of all the operations will be explained one by one below. 

### Upload group file

Call the uploadGroupSharedFile function to upload group files, an example is as follows: 

``` javascript
let options = {
    groupId: 'groupId',                        // Group id 
    file: file,                                // <input type="file"/> obtained file object 
    onFileUploadProgress: function(resp) {},   // Callback of upload progress
    onFileUploadComplete: function(resp) {},   // Callback when upload is complete
    onFileUploadError: function(e) {},         // Callback of failed upload
    onFileUploadCanceled: function(e) {}       // Upload cancel callback
};
conn.uploadGroupSharedFile(options);
```

------------------------------------------------------------------------

### Download group file

Call the downloadGroupSharedFile function to download the group file, an example is as follows: 

``` javascript
let options = {
    groupId: 'groupId',                        // Group id 
    fileId: 'fileId',                          // File id                        
    onFileDownloadComplete: function(resp) {}, // Callback for successful download
    onFileDownloadError: function(e) {},       // Download failed callback
};
conn.downloadGroupSharedFile(options);
```

------------------------------------------------------------------------

### Delete group file

Call the deleteGroupSharedFile function to delete the group file, an example is as follows: 

``` javascript
let options = {
    groupId: 'groupId',                        // Group id 
    fileId: 'fileId'                           // File id                        
};
conn.deleteGroupSharedFile(options)then((res) => {
    console.log(res)
})
```

------------------------------------------------------------------------

### Get group file list

Call the fetchGroupSharedFileList function to get the group file list, an example is as follows:

``` javascript
let options = {
    groupId: 'groupId'                 // Group id                        
};
conn.fetchGroupSharedFileList(options).then((res) => {
    console.log(res)
})
```

------------------------------------------------------------------------

## Group member management

Group member management includes the following operations:

-   Query group members

-   Make a member as manager

-   Revoke the administrator

-   Get all administrators in the group

Examples of all the operations will be explained individually below.

### Query group members

Call the `listGroupMember` function to page to get all members of the current group, an example is as follows:

``` javascript
let pageNum = 1,
    pageSize = 1000;
let options = {
    pageNum: pageNum,                                               // Page number
    pageSize: pageSize,                                             // Expected number of records per page
    groupId: 'groupId'                                       
};
conn.listGroupMember(options).then((res) => {
    console.log(res)
})
```

------------------------------------------------------------------------

### Make a member as manager

Call `setAdmin` to set members as administrators, an example is as follows:

``` javascript
let options = {
    groupId: "groupId",            // Group id
    username: "user"               // User name
};
conn.setAdmin(options).then((res) => {
    console.log(res)
})
```

------------------------------------------------------------------------

### Revoke the administrator

Call `removeAdmin` to revoke the administrator, an example is as follows:

``` javascript
let options = {
    groupId: "groupId",             // Group id
    username: "user"                // User name
};
conn.removeAdmin(options).then((res) => {
    console.log(res)
})
```

------------------------------------------------------------------------

### Get all managers of the group

Call `getGroupAdmin` to get all the administrators in the group, an example is as follows:
``` javascript
let options = {
    groupId: "groupId"                 // Group id
};
conn.getGroupAdmin(options).then((res) => {
    console.log(res)
})
```

------------------------------------------------------------------------

## Add group processing

Add group includes the following operations:

-   Add friends to the group

-   Apply to join the group

-   Agree users to join the group

-   Deny users to join the group

Examples of all the operations will be explained indiviudally below.

### Add friends to the group

The administrator can add friends to the group. Call `inviteToGroup` to add friends to the group, an example is as follows:

``` javascript
let option = {
    users: ['user1', 'user2'],
    groupId: 'groupId'
};
conn.inviteToGroup(option).then((res) => {
    console.log(res)
})

```

------------------------------------------------------------------------

### Apply to the group to join the group

Call `joinGroup` to send a group membership application to the group, an example is as follows:

``` javascript
let options = {
    groupId: "groupId",         // Group ID
    message: "I am Tom"         // Request message
};
conn.joinGroup(options).then((res) => {
    console.log(res)
})
```

------------------------------------------------------------------------

### Agree users to join the group

Only the administrator has the authority to approve the user's request to join the group.

Call `agreeJoinGroup` to agree to the user's request to join the group, an example is as follows:

``` javascript
let options = {
    applicant: 'userId',                          // Username for applying to join the group
    groupId: 'groupId'                            // Group ID
};
conn.agreeJoinGroup(options).then((res) => {
    console.log(res)
})
```

------------------------------------------------------------------------

### Deny users to join the group

Only the administrator has the authority to reject the user's request to join the group.

Call `rejectJoinGroup` to reject the user's request to join the group, an example is as follows：

``` javascript
let options = {
    applicant: 'user',                 // Username for applying to join the group
    groupId: 'groupId'                 // Group ID
};
conn.rejectJoinGroup(options).then((res) => {
    console.log(res)
})
```

------------------------------------------------------------------------

## Mute management

Mute management includes the following operations:

-   Mute members

-   Unmute members

-   Get banned members of the group

Examples of the erations will be explained one by one below.

### Mute members

Call `mute` to mute members, an example is as follows:

``` javascript
let options = {
    username: "user",                      // Member username
    muteDuration: 886400000,               // The duration of the mute, in milliseconds
    groupId: "groupId"
};
conn.mute(options).then((res) => {
    console.log(res)
})
```

------------------------------------------------------------------------

### Unmute members

Call `removeMute` to unmute a member, an example is as follows:

``` javascript
let options = {
    groupId: "groupId",                  // Group ID
    username: "user"                     // Member username
};
conn.removeMute(options).then((res) => {
    console.log(res)
})
```

------------------------------------------------------------------------

### Get banned members of the group

Call `getMuted` to get all banned members in the group, the example is as follows:

``` javascript
let options = {
    groupId: "groupId"                // Group ID
};
conn.getMuted(options).then((res) => {
    console.log(res)
})
```

------------------------------------------------------------------------

### Turn on and off all mute

The owner and administrator can turn on and off the mute of all members.

``` javascript
//Mute all members in the group
let options = {
    groupId: "groupId", //Group id
};
conn.disableSendGroupMsg(options).then((res) => {
    console.log(res)
})

//Unmute all members in the group
conn.enableSendGroupMsg(options).then((res) => {
    console.log(res)
})
```

------------------------------------------------------------------------

### Whitelist management

Users can be added to the whitelist. The user whitelist will become effective once the administrator turns on all members' mute, and the whitelist can run to allow the users on the list to send messages.
In addition, the users on the whitelist can be removed. the users can check whether you are in the whitelist, and get the whitelist list.

``` javascript
//Add users to the whitelist
let options = {
    groupId: "groupId",          // Group id
    users: ["user1", "user2"]    // Member id list
};
conn.addUsersToGroupWhitelist(options);

//Remove user from whitelist
let options = {
    groupId: "groupId", // Group id
    userName: "user"    // The member to be removed
}
conn.rmUsersFromGroupWhitelist(options);

//Get the list of whitelisted members from the server
let options = {
    groupId: "groupId"  // Group id
}
conn.getGroupWhitelist(options);

//Query whether group members are whitelisted users, operation authority: app admin can query all users; app user can query themselves
let options = {
    groupId: "groupId", // Group id
    userName: "user"    // The member to be queried
}
conn.isGroupWhiteUser(options);
```

------------------------------------------------------------------------

## Blacklist management

Blacklist management includes the following operations:

-   Add members to the group blacklist (single)

-   Add members to the group blacklist (bulk)

-   Remove members from group blacklist (single)

-   Remove members from group blacklist (bulk)

-   Get group blacklist

Examples of all the operations will be explained one by one below.

### Add members to the group blacklist (single)

Call `groupBlockSingle` to add a single member to the group blacklist, an example is as follows:

``` javascript
let options = {
    groupId: 'groupId',                     // Group ID
    username: 'username'                    // The user name to be added to the blacklist
};
conn.groupBlockSingle(options).then((res) => {
    console.log(res)
})
```

------------------------------------------------------------------------

### Add members to the group blacklist (bulk)

Call `groupBlockMulti` to add members to the group blacklist in batches, an example is as follows:

``` javascript
let options = {
    groupId: 'groupId',                           // GroupID
    usernames: ['user1', 'user2', ...users]       // Array of usernames to be added to the blacklist
};
conn.groupBlockMulti(options).then((res) => {
    console.log(res)
})
```

------------------------------------------------------------------------

### Remove members from group blacklist (single)

Call `removeGroupBlockSingle` to remove a single member from the group blacklist, an example is as follows:

``` javascript
let options = {
    groupId: "groupId",                     // Group ID              
    username: "user"                        // Username that needs to be removed
}
conn.removeGroupBlockSingle(options).then((res) => {
    console.log(res)
})
```

------------------------------------------------------------------------

### Remove members from group blacklist (bulk)

Call `removeGroupBlockMulti` to remove members from the group blacklist in batches, an example is as follows:

``` javascript
let options = {
    groupId: "groupId",                         // Group ID
    username: ["user1", "user2"]                // Array of usernames to be removed
}
conn.removeGroupBlockMulti(options).then((res) => {
    console.log(res)
})
```

------------------------------------------------------------------------

### Get group blacklist

Call `getGroupBlacklistNew` to get the group blacklist, an example is as follows:

``` javascript
// Get group blacklist
let option = {
    groupId: 'groupId',
};
conn.getGroupBlacklistNew(option).then((res) => {
    console.log(res)
})

```

------------------------------------------------------------------------

## Group event monitor

Group-related events can be monitored in the registered monitoring event onPresence:
``` javascript
conn.listen({
  onPresence: function(msg){
    switch(msg.type){
    case 'rmGroupMute':
      // Unblock group one-click muting
      break;
    case 'muteGroup':
      // Group one-click muting
      break;
    case 'rmUserFromGroupWhiteList':
      // Delete group whitelist member
      break;
    case 'addUserToGroupWhiteList':
      // Add group whitelist members
      break;
    case 'deleteFile':
      // Delete group file
      break;
    case 'uploadFile':
      // Upload group file
      break;
    case 'deleteAnnouncement':
      // Delete group announcement
      break;
    case 'updateAnnouncement':
      // Update group announcement
      break;
    case 'removeMute':
      // Unmute
      break;
    case 'addMute':
      // Mute
      break;
    case 'removeAdmin':
      // Remove manager
      break;
    case 'addAdmin':
      // Add manager
      break;
    case 'changeOwner':
      // Transfer group
      break;
    case 'direct_joined':
      // Pulled directly into the group
      break;
    case 'leaveGroup':
      // Exit group
      break;
    case 'memberJoinPublicGroupSuccess':
      // Successfully joined the public group
      break;
    case 'removedFromGroup':
      // Remove from group
      break;
    case 'invite_decline':
      // Decline to join group invitation
      break;
    case 'invite_accept':
      // Receive group invitation (group includes permissions)
      break;
    case 'invite':
      // Receive group invitation
      break;
    case 'joinPublicGroupDeclined':
      // Reject group application
      break;
    case 'joinPublicGroupSuccess':
      // Agree to join the group application
      break;
    case 'joinGroupNotifications':
      // Apply to join the group
      break;
    case 'leave':
      // Exit group
      break;
    case 'join':
      // Join group
      break;
    case 'deleteGroupChat':
      // Disband the group
      break;
    default:
      break;
  }}
})
```

------------------------------------------------------------------------

## Group news

Group messages include the following operations:

-   Send message

-   Receiving and messages

All operations will be explained one by one below.

### Send a message

See [send message](/im/web/basics/message#send message).

### Receiving and processing messages

-   Group chat receiving and processing messages are the same as single chat;

-  The message body and the single chat message are distinguished according to the type of message;

-   Single chat: chat, group chat: groupchat, and chat room: chatroom;

-   According to the types of the messages, they will be processed differently.


