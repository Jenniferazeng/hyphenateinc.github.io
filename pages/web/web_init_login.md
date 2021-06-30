---
title: Connect to chat server
keywords: web
sidebar: web_sidebar
toc: true
permalink: web_init_login.html
folder: web
---

## Initialization 

### Create connection

``` javascript
const conn = WebIM.conn = new WebIM.connection({
    appKey: 'your app key',
    isHttpDNS: true,
    isMultiLoginSessions: false,
    https: true,
    autoReconnectNumMax: 5
})
```

### Add callback function

``` javascript
conn.listen({
    onOpened: function ( message ) {},         //Successful connection callback 
    onClosed: function ( message ) {},         //Connection closed callback 
    onTextMessage: function ( message ) {},    //Receive text message
    onEmojiMessage: function ( message ) {},   //Receive emoji message
    onPictureMessage: function ( message ) {}, //Receive picture message
    onCmdMessage: function ( message ) {},     //Receive command message 
    onAudioMessage: function ( message ) {},   //Receive audio message
    onLocationMessage: function ( message ) {},//Receive location message
    onFileMessage: function ( message ) {},    //Receive file message
    onVideoMessage: function (message) {
        var node = document.getElementById('privateVideo');
        var option = {
            url: message.url,
            headers: {
              'Accept': 'audio/mp4'
            },
            onFileDownloadComplete: function (response) {
                var objectURL = WebIM.utils.parseDownloadResponse.call(conn, response);
                node.src = objectURL;
            },
            onFileDownloadError: function () {
                console.log('File down load error.')
            }
        };
        WebIM.utils.download.call(conn, option);
    },   //Receive video message
    onPresence: function ( message ) {},       //Processing "broadcast" or "publish-subscribe" messages, such as contact subscription requests, processing groups, chat rooms being kicked and disbanded, etc.
    onRoster: function ( message ) {},         //Process friend request
    onInviteMessage: function ( message ) {},  //Process group invitation
    onOnline: function () {},                  //The local network connection is successful
    onOffline: function () {},                 //Local network dropped
    onError: function ( message ) {},          //Failure callback
    onBlacklistUpdate: function (list) {       //Blacklist changes
        // Query the blacklist, block a friend, and remove a friend from the blacklist will call back this function. The list is all the information of the existing friends in the blacklist
    },
    onRecallMessage: function(message){},      //Receive recall message callback
    onReceivedMessage: function(message){},    //Receipt received from message delivery server
    onDeliveredMessage: function(message){},   //Receipt of message delivery to the client
    onReadMessage: function(message){},        //Receive message read receipt
    onCreateGroup: function(message){},        //Receipt of successful group creation (createGroupNew needs to be called)
    onMutedMessage: function(message){},       //If a user is banned in group A, this callback will be sent to group A and the message will not be delivered to other members of the group
    onChannelMessage: function(message){}      //Receive the read receipt of the entire conversation, and the message will be received in this callback when the other party sends a channel ack
});
```

## Register

Register Agora Chat user acount with username/password/nickname:

``` javascript
  var options = { 
    username: 'username',
    password: 'password',
    nickname: 'nickname',
    appKey: 'your app key',
    success: function () { },  
    error: function (err) {
        let errorData = JSON.parse(err.data);
        if (errorData.error === 'duplicate_unique_property_exists') {
            console.log('user already existed！');
        } else if (errorData.error === 'illegal_argument') {
            if (errorData.error_description === 'USERNAME_TOO_LONG') {
                console.log('user name exceeds 64 bytes！')
            }else{
                console.log('illegal user name！')
            }
        } else if (errorData.error === 'unauthorized') {
            console.log('registration failed，no authority！')
        } else if (errorData.error === 'resource_limited') {
            console.log('Your APP user registration number has reached the limit, please upgrade to the enterprise version！')
        }
    }, 
  }; 
  conn.registerUser(options);
```

## Modify pushed nickname 

Set a nickname during registration. This nickname is used to display when pushing messages. Call the following API to modify the nickname 

``` javascript
conn.updateCurrentUserNick('newNick')
```

## Login 

### Username/password login 

Use username/password to log in to Agora Web IM: 

``` javascript
var options = { 
  user: 'username',
  pwd: 'password',
  appKey: 'your app key'
};
conn.open(options);
```

### Login with Token 

1\. Log in with username/password and get Token. 

``` javascript
var options = {
    user: 'username',
    pwd: 'password',
    appKey: 'your app key',
    success: function (res) {
      var token = res.access_token
    },
    error: function(){
    }
};
conn.open(options);
```

2\. Log in to Easemob Web IM using Token.

``` javascript
var options = {
    user: 'username',
    accessToken: 'token',
    appKey: 'your app key'
};
conn.open(options);
```

## Logout

``` javascript
conn.close();
```

## Upload push token

If using the SDK in the native client and integrate the third-party push functions, call this method to upload the token to the chat server

``` javascript
/**
 * @param {Object} options - 
 * @param {Object} options.deviceId - Device id, which can be defined by yourself, generally used to identify the same device
 * @param {Object} options.deviceToken - Push token
 * @param {Object} options.notifierName - The appId of the push service is senderId for FCM, and “appId+#+AppKey ”for VIVO 
 */
conn.uploadToken(options);
```


## Common Quastions

Q: Does it support token login, and does it support HTTPS? \ 
A: Yes, it supports.

Q: Does it support reconnection? \ 
A:
Yes. 1. Without using DNS: when the current connection cannot be established, it will try to reconnect. The number of connections can be configured in config; 2. Using DNS: when the current connection cannot be established, it will try to connect one by one according to the address of DNSconfig.

Q: Does ws have an uplink or a downlink? \ 
A:
It may be that the browser caches the wrong result returned bu ws. The solution is to add a timestamp parameter to force the browser not to cache. 

