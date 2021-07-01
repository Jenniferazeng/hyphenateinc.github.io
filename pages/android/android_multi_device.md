---
title: Multi Device
keywords: android
sidebar: android_sidebar
toc: true
permalink: android_multi_device.html
folder: android
---

## The ID of the device logged in by other devices

When the PC terminal and the mobile terminal log in to the same account, the device ID of the PC terminal can be got through a specific method on the mobile terminal. The device ID is equivalent to a special friend Username and can be used directly for chat, and the usage method is similar to friend.

### Interface

``` java
class ContactManager:

     /**
      * get the ID of the logged-in user on other devices from the server
      *
      * @return ID list of other devices login
      * @throws ChatException
      */
     public List<String> getSelfIdsOnOtherPlatform() throws ChatException
    
     /**
      * Asynchronous operation, get the ID of the logged-in user on other devices from the server
      *
      * @param callback contains the user's login ID on other devices
      */
     public void aysncGetSelfIdsOnOtherPlatform(final ValueCallBack<List<String>> callback);
```

### Usage example

``` java
     List<String> selfIds = ChatClient.getInstance().contactManager().getSelfIdsOnOtherPlatform();
```

## Multi-device event callback

Account A is logged in to device A and device B at the same time. Account A performs some operations on device A. Device B will receive notifications corresponding to these operations. The specific instructions are as follows:

### Interface MultiDeviceListener

``` java
interface MultiDeviceListener {
    /**
     * The friend has been removed from another device
     */
    int CONTACT_REMOVE = 2;

    /**
     * The friend request has been approved on other device
     */
    int CONTACT_ACCEPT = 3;

    /**
     * The friend request has been rejected on other device
     */
    int CONTACT_DECLINE = 4;

    /**
     * The current user adds someone to the blacklist on other device
     */
    int CONTACT_BAN = 5;

    /**
     * Friends are removed from the blacklist on other device
     */
     int CONTACT_ALLOW = 6;

    /**
     * Created a group
     */
    int GROUP_CREATE = 10;

    /**
     * Destroyed the group
     */
    int GROUP_DESTROY = 11;

    /**
     * Already joined the group
     */
    int GROUP_JOIN = 12;

    /**
     * Already left the group
     */
    int GROUP_LEAVE = 13;

    /**
     * send group application
     */
    int GROUP_APPLY = 14;

    /**
     * approve group application
     */
    int GROUP_APPLY_ACCEPT = 15;

    /**
     * Reject group application
     */
    int GROUP_APPLY_DECLINE = 16;

    /**
     * Invite group members
     */
    int GROUP_INVITE = 17; //

    /**
     * apporve the group invitation
     */
    int GROUP_INVITE_ACCEPT = 18; //

    /**
     * Decline group invitation
     */
    int GROUP_INVITE_DECLINE = 19;

    /**
     * Kick someone out of the group
     */
    int GROUP_KICK = 20;

    /**
     * add to the group blacklist
     */
    int GROUP_BAN = 21; //Join the group blacklist

    /**
     * Remove from group blacklist
     */
    int GROUP_ALLOW = 22;

    /**
     * Block group
     */
    int GROUP_BLOCK = 23;

    /**
     * Unblock group
     */
    int GROUP_UNBLOCK = 24;

    /**
     * Transfer group's ownership
     */
    int GROUP_ASSIGN_OWNER = 25;

    /**
     * Add administrator
     */
    int GROUP_ADD_ADMIN = 26;

    /**
     * Remove administrator
     */
    int GROUP_REMOVE_ADMIN = 27;

    /**
     * mute user
     */
    int GROUP_ADD_MUTE = 28;

    /**
     * unmute user
     */
    int GROUP_REMOVE_MUTE = 29;

    /**
     * Multi-device contact events
     */
    void onContactEvent(int event, String target, String ext);

    /**
     * Multi-device group event
     */
    void onGroupEvent(int event, String target, List<String> usernames);
}
```

### Usage example

``` java
//Register to listen
ChatClient.getInstance().addMultiDeviceListener(new MyMultiDeviceListener()); // class MyMultiDeviceListener implements MultiDeviceListener

//Cancel listening
ChatClient.getInstance().removeMultiDeviceListener(myMultiDeviceListener);
```

------------------------------------------------------------------------
