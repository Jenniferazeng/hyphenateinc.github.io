---
title: Group
keywords: android
sidebar: android_sidebar
toc: true
permalink: android_group.html
folder: android
---

Many groups operation need authentication ahead , including whether the current user is in the group and whether they have administrator or owner permissions.
It is recommended that after the user logs in successfully, call ChatClient.getInstance().groupManager().getJoinedGroupsFromServer();
Refresh the list of local groups to ensure that the authentication works normally.


-------------------------------------------------- ----------------------

## Send and receive messages

For sending and receiving messages and chat records, see [Message](https://hyphenateinc.github.io/android_message.html) .

## New group

``` java
/**
 * Create a group
 * @param groupName    group name
 * @param desc         group introduction
 * @param allMembers   is the initial member of the group, if you only pass an null array by yourself (up to 100 members can be passed)
 * @param reason       reason for inviting members to join
 * @param option       group type option, you can set the maximum number of group users (default 200) and group type @see {@link GroupStyle}
 * 						option.inviteNeedConfirm indicates whether the other party's approval is required for inviting the other party into the group. By default, the invited party join the group automatically.
 * 						option.extField can set extended fields for the group when creating a group, which is convenient for customization.
 * @return 				created group
 * @throws ChatException
 */
GroupOptions option = new GroupOptions();
option.maxUsers = 200;
option.style = GroupStyle.GroupStylePrivateMemberCanInvite;

ChatClient.getInstance().groupManager().createGroup(groupName, desc, allMembers, reason, option);
```

Note: If option.inviteNeedConfirm is set to false, the invitee is directly added to the group. In this case, it  will not work that invitee set the non-automatic group joining .

- The GroupStyle in option are:

  - `GroupStylePrivateOnlyOwnerInvite`------Private group, only the owner can invite people;
  - `GroupStylePrivateMemberCanInvite`------Private group, group members can also invite people into the group;
  - `GroupStylePublicJoinNeedApproval`------Public group, to join this group, except for the invitation of the group owner, you can only join this group through application;
  - `GroupStylePublicOpenJoin` ------Public group, anyone can join this group.

## Add administrator permissions

``` java
/**
  * Add group administrator, owner permission is required
  * @param groupId
  * @param admin
  * @return
  * @throws ChatException
  */
ChatClient.getInstance().groupManager().addGroupAdmin(final String groupId, final String admin);//Asynchronous processing required
```

## Remove administrator permissions

``` java
/**
  * To delete group administrators, owner permissions are required
  * @param groupId
  * @param admin
  * @return
  * @throws ChatException
  */
ChatClient.getInstance().groupManager().removeGroupAdmin(String groupId, String admin);//Asynchronous processing required
```

## Change group owner

``` java
/**
  * Group ownership to others
  * @param groupId
  * @param newOwner
  * @return
  * @throws ChatException
  */
ChatClient.getInstance().groupManager().changeOwner(String groupId, String newOwner);//Asynchronous processing required
```

## Add people in the group

``` java
//when group owner adds people, call this method
ChatClient.getInstance().groupManager().addUsersToGroup(groupId, newmembers);//Asynchronous processing is required
//In a private group, if the group member invitation is open, the group member invitation calls the following method
ChatClient.getInstance().groupManager().inviteUser(groupId, newmembers, null);//Asynchronous processing is required
```

## Kick people in the group

``` java
//Remove username from the group
ChatClient.getInstance().groupManager().removeUserFromGroup(groupId, username);//Asynchronous processing is required
```

## Join a group

Can only be used to join public groups.

``` java
//If the public group is free to join, group.isMembersOnly() is false, join directly
ChatClient.getInstance().groupManager().joinGroup(groupid);//Asynchronous processing is required
//Need to apply and verify to join, group.isMembersOnly() is true, call the following method
ChatClient.getInstance().groupManager().applyJoinToGroup(groupid, "Request to join");//Asynchronous processing is required
```

## Leave the group

``` java
ChatClient.getInstance().groupManager().leaveGroup(groupId);//Asynchronous processing is required
```

## Disband group

``` java
ChatClient.getInstance().groupManager().destroyGroup(groupId);//Asynchronous processing is required
```

## Get a complete list of group members

``` java
//If there are many group members, you need to repeat the getting operation many times to get all of them

List<String> memberList = new ArrayList<>;
CursorResult<String> result = null;
final int pageSize = 20;
do {
     result = ChatClient.getInstance().groupManager().fetchGroupMembers(groupId,
             result != null? result.getCursor(): "", pageSize);
     memberList.addAll(result.getData());
} while (!TextUtils.isnull(result.getCursor()) && result.getData().size() == pageSize);
```

## Get group list

``` java
//Get the list of groups you have joined and created from the server. The group SDK got from this api will be automatically saved to the memory and db.
List<Group> grouplist = ChatClient.getInstance().groupManager().getJoinedGroupsFromServer();//Asynchronous processing is required

//Load group list from local
List<Group> grouplist = ChatClient.getInstance().groupManager().getAllGroups();

//Get the list of public groups
//pageSize is the number of groups to be fetched, cursor is used to tell the server where to start fetching
CursorResult<GroupInfo> result = ChatClient.getInstance().groupManager().getPublicGroupsFromServer(pageSize, cursor);//Asynchronous processing is required
List<GroupInfo> groupsList = List<GroupInfo> returnGroups = result.getData();
String cursor = result.getCursor();
```

## Modify group name｜Description

``` java
//Modify group name
ChatClient.getInstance().groupManager().changeGroupName(groupId,changedGroupName);//Asynchronous processing is required

//Modify group description
ChatClient.getInstance().groupManager().changeGroupDescription(groupId,description);//Asynchronous processing is required
```

## Group Information

Get information of a single group. The return result of getGroupFromServer(groupId) includes the group name, group description, group owner, and administrator list, but does not include group members.

getGroupFromServer(String groupId, boolean fetchMembers). If fetchMembers is true, the group members will also be get when fetching group information, the maximum number is 200.

``` java
//get the basic group information from the local according to the group ID
Group group = ChatClient.getInstance().groupManager().getGroup(groupId);
//get the basic group information from the server according to the group ID
Group group = ChatClient.getInstance().groupManager().getGroupFromServer(groupId);

group.getOwner();//Get the owner
List<String> members = group.getMembers();//Get the group members in memory
List<String> adminList = group.getAdminList();//Get the list of administrators
boolean isMsgBlocked = group.isMsgBlocked();//Get whether the group message has been blocked
...
```

For details of other methods, please refer to the [Agora Interface Document](https://hyphenateinc.github.io/android_reference/classio_1_1agora_1_1chat_1_1_group_manager.html).

## Block group messages

The call of Owner permission is not allowed.

``` java
/**
* After blocking group messages, you cannot receive messages from this group (still a member of the group, but no longer receive messages)
* @param groupId, group ID
* @throws ChatException
*/
ChatClient.getInstance().groupManager().blockGroupMessage(groupId);//Asynchronous processing is required
```

## Unblock group

``` java
/**
* Unblock group messages and you can receive all group messages normally
* @param groupId
* @throws ChatException
*/
ChatClient.getInstance().groupManager().unblockGroupMessage(groupId);//Asynchronous processing is required
```

## Group blacklist

### add a user into the group blacklist

``` java
/**
* Add users to the blacklist of the group. Users who are added to the blacklist cannot join the group and cannot send and receive messages from this group
* (Only the group owner can set the group blacklist)
* @param groupId,     ID of the group
* @param username,    username to be blocked
* @exception ChatException     will be thrown if an error occurs
*/
ChatClient.getInstance().groupManager().blockUser(groupId, username);//Asynchronous processing is required
```

### Remove users from the blacklist

``` java
/**
* Remove users from the blacklist（Only the group owner can call this function）
* @param groupId,     Group ID
* @param username,    Username to be unblocked
*/
ChatClient.getInstance().groupManager().unblockUser(groupId, username);//Asynchronous processing required
```

### Get the blacklisted user list of the group

``` java
/**
* Get the blacklist of the group
* (Only the group owner can call this function)
* @return List<String>
* @throws ChatException    get failed
*/
ChatClient.getInstance().groupManager().getBlockedUsers(groupId);//Asynchronous processing is required
```

## Group mute operation

### Add group members to the mute list

``` java
/**
 * to mute group members, the group owner or administrator permissions are required
 * @param               groupId
 * @param muteMembers   muted user list
 * @param duration      The duration of silence, in milliseconds
 * @return
 * @throws ChatException
 */
ChatClient.getInstance().groupManager().muteGroupMembers(String groupId, List<String> muteMembers, long duration);//Asynchronous processing is required
```

### Remove group members from the mute list

``` java
/**
 * To unmute, you need the group owner or administrator permissions
 * @param groupId
 * @param members
 * @return
 * @throws ChatException
 */

ChatClient.getInstance().groupManager().unMuteGroupMembers(String groupId, List<String> members);//Asynchronous processing is required
```

### Get the list of banned group members

``` java
/**
  * To get the mute list of the group, the group owner or administrator permissions are required
  * @param groupId
  * @param pageNum
  * @param pageSize
  * @return Map.entry.key        is the muted member id, and Map.entry.value is the time of the mute action lasting, in milliseconds.
  * @throws ChatException
  */
ChatClient.getInstance().groupManager().fetchGroupMuteList(String groupId, int pageNum, int pageSize)
```

### Turn on and off all mute

The owner and administrator can turn on and off the mute of all members.

``` java
     /**
     * \~chinese
     * Mute all members
     * @param groupId group id
     */
    public void muteAllMembers(final String groupId, final ValueCallBack<Group> callBack)
    
    /**
     * \~chinese
     * Lift all members' ban
     * @param groupId group id
     */
    public void unmuteAllMembers(final String groupId, final ValueCallBack<Group> callBack)
```

### Whitelist Management

Users can be added to the whitelist. The user whitelist takes effect when the administrator turns on all members' mute, and the whitelist can be run to allow members in it to send messages to users.
In addition, you can remove the user from the whitelist, check whether you are in the whitelist, and get the whitelist list.

``` java
         /**
     * \~chinese
     * Add users to the whitelist
     * @param groupId group id
     * @param members member id list
     */
    public void addToGroupWhiteList(final String groupId, final List<String> members, final CallBack callBack)

        /**
     * \~chinese
     * Remove users from the whitelist
     * @param groupId group id
     * @param members member id list
     */
    public void removeFromGroupWhiteList(final String groupId, final List<String> members, final CallBack callBack)

        /**
     * \~chinese
     * Check if you are in the whitelist
     * @param groupId group id
     */
    public void checkIfInGroupWhiteList(final String groupId, ValueCallBack<Boolean> callBack)

        /**
     * \~chinese
     * Get the list of whitelisted members from the server
     * @param groupId group id
     */
    public void fetchGroupWhiteList(final String groupId, final ValueCallBack<List<String>> callBack)
```

## Set/Update Group Announcement

``` java
/**
  * Update group announcement
  * @param groupId group id
  * @param announcement Announcement content
  * @throws ChatException
  */
ChatClient.getInstance().groupManager().updateGroupAnnouncement(groupId, announcement);
```

## Get group announcement

``` java
ChatClient.getInstance().groupManager().fetchGroupAnnouncement(groupId)

```

## Upload shared files

``` java
/**
  * Upload the shared file to the group, note that the callback is only for the progress callback
  * @param groupId group id
  * @param filePath file local path
  * @param callBack callback
  */
ChatClient.getInstance().groupManager().uploadGroupSharedFile(groupId, filePath, callBack)

```

## Delete group shared files

``` java
/**
  * Delete this shared file from the group
  * @param groupId group id
  * @param fileId file id
  */
ChatClient.getInstance().groupManager().deleteGroupSharedFile(groupId, fileId);

```

## Get the group shared file list

``` java
/**
  * Get the group's shared file list from the server
  * @param groupId group id
  * @param pageNum page number
  * @param pageSize page size
  *
  */
ChatClient.getInstance().groupManager().fetchGroupSharedFileList(groupId, pageNum, pageSize)

```

## Download group shared files

``` java
/**
  * Download a shared file in the group, note that the callback is only for progress callback
  * @param groupId    group id
  * @param fileId     file id
  * @param savePath   file saved path
  * @param callBack   callback
  */
ChatClient.getInstance().groupManager().downloadGroupSharedFile(groupId, fileId, savePath, callBack);
```

## Update group extension field

``` java
ChatClient.getInstance().groupManager().updateGroupExtension(groupId, extension);
```

## Group event monitoring

``` java
ChatClient.getInstance().groupManager().addGroupChangeListener(new GroupChangeListener() {
@Override
    public void onInvitationReceived(String groupId, String groupName, String inviter, String reason) {
        //Received an invitation to join the group
    }

    @Override
    public void onRequestToJoinReceived(String groupId, String groupName, String applyer, String reason) {
        //Users apply to join the group
    }

    @Override
    public void onRequestToJoinAccepted(String groupId, String groupName, String accepter) {
        //Add group application is approved
    }

    @Override
    public void onRequestToJoinDeclined(String groupId, String groupName, String decliner, String reason) {
        //The group application is rejected
    }

    @Override
    public void onInvitationAccepted(String groupId, String inviter, String reason) {
        //Group invitation is approved
    }

    @Override
    public void onInvitationDeclined(String groupId, String invitee, String reason) {
        //Group invitation rejected
    }
    
    @Override
    public void onAutoAcceptInvitationFromGroup(String groupId, String inviter, String inviteMessage) {
        //Notice to automatically join the group when receiving an invitation
    }

    @Override
    public void onMuteListAdded(String groupId, final List<String> mutes, final long muteExpire) {
        //Notice of muting a member
    }

    @Override
    public void onMuteListRemoved(String groupId, final List<String> mutes) {
        //The notification of removing the members from the mute list
    }
    
    @Override
    public void onWhiteListAdded(String groupId, List<String> whitelist) {
          //Members are added to the whitelist
    }

    @Override
    public void onWhiteListRemoved(String groupId, List<String> whitelist) {
         //The member is removed from the whitelist
    }

    @Override
    public void onAllMemberMuteStateChanged(String groupId, boolean isMuted) {
          //Whether the mute of all members is enabled
    }

    @Override
    public void onAdminAdded(String groupId, String administrator) {
        //notification of adding the administrator
    }

    @Override
    public void onAdminRemoved(String groupId, String administrator) {
        //Notice of removal of administrator 
    }

    @Override
    public void onOwnerChanged(String groupId, String newOwner, String oldOwner) {
        //Notification of group owner change
    }
    @Override
    public void onMemberJoined(final String groupId, final String member){
        //Notify of new members joining the group
    }
    @Override
    public void onMemberExited(final String groupId, final String member) {
        //notification of Group member exiting 
    }

    @Override
    public void onAnnouncementChanged(String groupId, String announcement) {
        //notification of Group announcement changed
    }

    @Override
    public void onSharedFileAdded(String groupId, EMMucSharedFile sharedFile) {
        //notification of adding shared files
    }

    @Override
    public void onSharedFileDeleted(String groupId, String fileId) {
        //notification of Group shared file deleted
    }
});
```

------------------------------------------------------------------------
