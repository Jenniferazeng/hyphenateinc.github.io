---
title: Android Relationship
keywords: android
sidebar: android_sidebar
toc: true
permalink: android_relationship.html
folder: android
---
# Friend management

-------------------------------------------------- ----------------------

## Get friends list

Get the username list of friends, the developer needs to go to your own server to get the details of your friends based on the username.

``` java
List<String> usernames = ChatClient.getInstance().contactManager().getAllContactsFromServer();
```

## find friends

SDK
The service of finding friends is not provided. If you need to find friends, you need to call the user query interface of the developer's own server.

In order to ensure that the found friends can be added, the user data of the developer’s own server (the user’s Agora ID), which can be imported into the Agora server through the SDK's background interface.

## add friend

``` java
//The parameters are the username of the friend to be added and the reason for adding
ChatClient.getInstance().contactManager().addContact(toAddUsername, reason);
```

## delete friend

``` java
ChatClient.getInstance().contactManager().deleteContact(username);
```

## Approve friend request

The friend request is automatically approved by default, if you want to manually approve, you need to call it when the SDK is initialized
`opptions.setAcceptInvitationAlways(false);`.

``` java
ChatClient.getInstance().contactManager().acceptInvitation(username);
```

## Reject friend request

``` java
ChatClient.getInstance().contactManager().declineInvitation(username);
```

## Listen friend status events

``` java
 ChatClient.getInstance().contactManager().setContactListener(new ContactListener() {
   
   @Override
   public void onContactAgreed(String username) {
       //The friend request is approved
   }
   
   @Override
   public void onContactRefused(String username) {
       //Friend request is rejected
   }
   
   @Override
   public void onContactInvited(String username, String reason) {
       //Received friend invitation
   }
   
   @Override
   public void onContactDeleted(String username) {
       //Call back this method when deleted
   }
   
   
   @Override
   public void onContactAdded(String username) {
       //Call back this method when a contact is added
   }
});
```

## Blacklist

### Get the blacklist from the server

``` java
ChatClient.getInstance().contactManager().getBlackListFromServer();
```

### Get blacklist from local db

``` java
ChatClient.getInstance().contactManager().getBlackListUsernames();
```

### Add users to the blacklist

``` java
//The effect of true and false is the same. I can send messages to users on the blacklist, but I cannot receive them when they send me messages.
ChatClient.getInstance().contactManager().addUserToBlackList(username,true);
```

### Remove users from the blacklist

``` java
ChatClient.getInstance().contactManager().removeUserFromBlackList(username);
```

## Get the id of the same account logged in on other ends

After get the id, it can then be used to send messages between accounts logged in on different terminals, for example, the PC terminal and the mobile terminal can send messages to each other.

``` java
selfIds = ChatClient.getInstance().contactManager().getSelfIdsOnOtherPlatform();
```

## Demo and SDK download

[Download Demo and SDK](http://www.easemob.com/download/im)

For detailed document, please refer to [Java Doc](/im/android/sdk/apidoc)

-------------------------------------------------- ----------------------

\<WRAP group> \<WRAP half column>
Previous page: [Message](/im/android/basics/message) \</WRAP>

\<WRAP half column> Next page: [Group Management](/im/android/basics/group)
\</WRAP> \</WRAP>


------------------------------------------------------------------------
