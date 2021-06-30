---
title: Android Relationship
keywords: android
sidebar: android_sidebar
toc: true
permalink: android_relationship.html
folder: android
---

## Get contact list

Get the username list of contact, the developer needs to go to your own server to get the details of your contacts based on the username.

``` java
List<String> usernames = ChatClient.getInstance().contactManager().getAllContactsFromServer();
```

## Find contacts

SDK is not provided the service of finding contacts. If you need to find contacts, you need to call the user query interface of the developer's own server.

In order to ensure that the found contacts can be added, the user data of the developer’s own server (the user’s chat ID), which can be imported into the chat server through the SDK's background interface.

## Add contact

``` java
//The parameters are the username of the contact to be added and the reason for adding
ChatClient.getInstance().contactManager().addContact(toAddUsername, reason);
```

## Delete contact

``` java
ChatClient.getInstance().contactManager().deleteContact(username);
```

## Approve contact request

The contact request is automatically approved by default, if you want to manually approve, you need to call it when the SDK is initialized
`opptions.setAcceptInvitationAlways(false);`.

``` java
ChatClient.getInstance().contactManager().acceptInvitation(username);
```

## Reject contact request

``` java
ChatClient.getInstance().contactManager().declineInvitation(username);
```

## Listen contact status events

``` java
 ChatClient.getInstance().contactManager().setContactListener(new ContactListener() {
   
   @Override
   public void onContactAgreed(String username) {
       //The contact request is approved
   }
   
   @Override
   public void onContactRefused(String username) {
       //contact request is rejected
   }
   
   @Override
   public void onContactInvited(String username, String reason) {
       //Received contact invitation
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

## Get the id of the same account logged in on other devices

After get the id, it can then be used to send messages between accounts logged in on different terminals, for example, the PC terminal and the mobile terminal can send messages to each other.

``` java
selfIds = ChatClient.getInstance().contactManager().getSelfIdsOnOtherPlatform();
```

------------------------------------------------------------------------
