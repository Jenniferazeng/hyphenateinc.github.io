---
title: Message
keywords: web
sidebar: web_sidebar
toc: true
permalink: web_message.html
folder: web
---

# Message

Message is one of the most important functions in client integration. The main methods of using WebIM messages are as follows:

-   Construct the message

-   Send message 

-   Recall the message

-   Receive the message

-   Handle the message

-   Message roaming 

-   New message notification

-   Message receipt

Through the integration of messages, you can have the fastest integrated experience of  how smooth IM can achieve in sending and receiving messages. 

## Construct the message 

``` javascript
let id = WebIM.conn.getUniqueId()
let msg = new WebIM.message('txt', id);
msg.set(option); //Message content option, will be introduced in detail below 
```

**Note:** The field is not necessary when sending ext extended message. However, if there is, the value cannot be in the form of \"ext:null\", otherwise the errors occur. 

## Send message

Agora supports sending messages to **single chat**, **group** **chat** and **chat room**: 

-   Text message

-   Emoji message

-   Sticker message

-   URL image message

-   Command message

-   Attachment message

-   Custom message

Diversified message types, covering the message requirements in a variety of scenarios.

### Send text messages (single chat, extended) 

The example of sending a text message in a single chat:

``` javascript
// Sending text messages in the single chat
function sendPrivateText() {
    let id = conn.getUniqueId();                 // Generate local message id 
    let msg = new WebIM.message('txt', id);      // Create text message 
    msg.set({
        msg: 'message content',                  // Message content 
        to: 'username',                          // The recipient of the message (user id)
        chatType: 'singleChat',                  // Set to single chat
        ext: {
            key: value,
            key2: {
                key3: value3
            }
        },                                  //Extend message
        success: function (id, serverMsgId) {
            console.log('send private text Success');  
        }, 
        fail: function(e){
            //Reason for failure:
            //e.type === '603' banned
            //e.type === '605' group does not exist
            //e.type === '602' not in a group or chat room
            //e.type === '504' the withdrawal time exceeded the withdrawal time when the message was withdrawn
            //e.type === '505' unopened message withdrawn
            //e.type === '506' not in the whitelist of the group or chat room
            //e.type === '503' unknown error 
            console.log("Send private text error");  
        }
    });
    conn.send(msg.body);
};
```

### Send text message (group) 

The example of sending group text messages: 

``` javascript
// Group sends text message 
function sendGroupText() {
    let id = conn.getUniqueId();            // Generate local message id
    let msg = new WebIM.message('txt', id); // Create text message
    let option = {
        msg: 'message content',             // Message content 
        to: 'group id',                     // The recipient of the message (group id)
        chatType: 'groupChat',              // Set group chat type to group chat
        ext: {},                            // Extend message 
        success: function () {
            console.log('send room text success');
        },                                  // For the related definition of success, the SDK will register the message id in the log for the backup process
        fail: function () {
            console.log('failed');
        }                                   // For the definition of failure, the sdk will register the message id in the log for the backup process
    };
    msg.set(option);
    conn.send(msg.body);
};
```

### Send text messages (chat room）

The example of sending chat room text messages：

``` javascript
// Sending text message in chat room
function sendRoomText() {
    let id = conn.getUniqueId();         // Generate local message id 
    let msg = new WebIM.message('txt', id); // Create text message 
    let option = {
        msg: 'message content',          // Message content
        to: 'chatroom id',               // Receive message object (chat room id)
        chatType: 'chatRoom',            // Set group chat type to chat room
        ext: {},                         // Extend message 
        success: function () {
            console.log('send room text success');
        },                               // For the related definition of success, the SDK will register the message id in the log for the backup process
        fail: function () {
            console.log('failed');
        }                                // For the definition of failure, the sdk will register the message id in the log for the backup process
    };
    msg.set(option);
    conn.send(msg.body);
};
```

**Note：** The performance examples of single chat and group chat are almost the same . The difference is that the recipients of the message are different. The object of single chat to is \*\*user ID **, and the object of group/chat room to is **group/chat room ID\*\*

------------------------------------------------------------------------

### Send emoji messages

Sending emoji messages is the same as sending text messages. The emoji text needs to be parsed into pictures on the other‘s client.

The example for sending emoji messages in a single chat is as follows:

``` javascript
conn.listen({
    onEmojiMessage: function (message) {
        console.log('Emoji');
        var data = message.data;
        for(var i = 0 , l = data.length ; i < l ; i++){
            console.log(data[i]);
        }
    },   //Receive emoji messages
});
// Single chat to send text messages
var sendPrivateText = function () {
    var id = conn.getUniqueId();                 // Generate local message id
    var msg = new WebIM.message('txt', id);      // Create text message
    msg.set({
        msg: 'message content',                  // Message content 
        to: 'username',                          // The recipient of the message (user id)
        chatType: 'singleChat',
        success: function (id, serverMsgId) {
            console.log('send private text Success');
        },
        fail: function(e){
            console.log("Send private text error");
        }
    });
    conn.send(msg.body);
};
```

**Note：** When the Emoji attribute is added to WebIM, if the sent message contains specific strings in WebIM.Emoji, the connection will automatically combine these strings and other characters into an array in order, and the structure of each array element is{type:\'emoji(or txt)\',  data:\'msg(or src)\'}

When type=\'emoji\', data means **path of emoji image**;

When type=\'txt\', data means **text message**.

------------------------------------------------------------------------

### Send command message

The example of sending command message:

``` javascript
var id = conn.getUniqueId();            //Generate local message id
var msg = new WebIM.message('cmd', id); //Create command message

msg.set({
  to: 'username',                        //The recipient of the message
  action : 'action',                     //User-defined, cmd message is required
  ext :{'extmsg':'extends messages'},    //User self-extended message content (same usage in group chat)
  success: function ( id,serverMsgId ) {}, //Message sent successfully callback
  fail: function(e){
      console.log("Fail"); //Fail to send a message if the user is banned or blocked
  }
});   

conn.send(msg.body);
```

------------------------------------------------------------------------

### Send attachment message

The attachment message is an important message type in the message. The attachment message mainly includes the following types:

* Image message
* File message
* Audio message
* Video message

**·** The SDK automatically executes to send an attachment message in two steps:

1. Upload the attachment to the server, and get the attachment information returned by the service, etc.;
2. Send the attachment message, the message body contains the basic information of the attachment, server path, secret, etc.

Note: There is a difference between the attachment message sent by the web terminal and the applet. The attachment message sent by the applet needs to upload the attachment to the ring letter server. The following is an image message to show how the applet sends the attachment message

The example of sending a image message in single chat:

``` javascript
// Web single chat to send image messages
var sendPrivateImg = function () {
    var id = conn.getUniqueId();                   // Generate local message id
    var msg = new WebIM.message('img', id);        // Create image message
    var input = document.getElementById('image');  // Select the input of the picture
    var file = WebIM.utils.getFileUrl(input);      // Convert pictures to binary files
    var allowType = {
        'jpg': true,
        'gif': true,
        'png': true,
        'bmp': true
    };
    if (file.filetype.toLowerCase() in allowType) {
        var option = {
            file: file,
            length: '3000',                       // Video file, unit (ms)
            ext: {
                file_length: file.data.size       // File size
            },
            to: 'username',                       // The recipient of the message
            chatType: 'singleChat',               // Set to single chat
            onFileUploadError: function () {      // Message upload failed
                console.log('onFileUploadError');
            },
            onFileUploadComplete: function () {   // Message uploaded successfully
                console.log('onFileUploadComplete');
            },
            success: function () {                // Message sent successfully
                console.log('Success');
            },
            fail: function(e){
                console.log("Fail");              //Fail to send a message if the user is banned or blocked
            },
            flashUpload: WebIM.flashUpload
        };
        msg.set(option);
        conn.send(msg.body);
    }
};

// Send image messages in applet single chat
sendImage(){
    const me = this;
    wx.chooseImage({
        count: 1,
        sizeType: ["original", "compressed"],
        sourceType: ["album"],
        success(res){
            me.upLoadImage(res);
        }
    });
}
upLoadImage(res){
    const me = this;
    let tempFilePaths = res.tempFilePaths;
    let token = WebIM.conn.context.accessToken
    wx.getImageInfo({
    src: res.tempFilePaths[0],
    success(res){
        let allowType = {jpg: true, gif: true, png: true, bmp: true};
        let str = WebIM.config.appkey.split("#");
        let width = res.width;
        let height = res.height;
        let index = res.path.lastIndexOf(".");
        let filetype = (~index && res.path.slice(index + 1)) || "";
        let domain = wx.WebIM.conn.apiUrl + '/'
        if(filetype.toLowerCase() in allowType){
            wx.uploadFile({
                url: domain + str[0] + "/" + str[1] + "/chatfiles",
                filePath: tempFilePaths[0],
                name: "file",
                header: {
                    "Content-Type": "multipart/form-data",
                    Authorization: "Bearer " + token
                },
                success(res){
                    if(res.statusCode === 400){
                        // The image upload to Alibaba Cloud inspection is illegal
                        let errData = JSON.parse(res.data);
                        if (errData.error === 'content improper') {
                            wx.showToast({
                                title: 'illegal image'
                            });
                            return
                        }
                    }
                    let data = res.data;
                    let dataObj = JSON.parse(data);
                    let id = WebIM.conn.getUniqueId(); // Generate local message id
                    let msg = new WebIM.message(msgType.IMAGE, id);
                    let file = {
                        type: msgType.IMAGE,
                        size: {
                            width: width,
                            height: height
                        },
                        url: dataObj.uri + "/" + dataObj.entities[0].uuid,
                        filetype: filetype,
                        filename: tempFilePaths[0]
                    };
                    msg.set({
                        apiUrl: WebIM.config.apiURL,
                        body: file,
                        from: me.data.username.myName,
                        to: me.getSendToParam(),
                        chatType: me.data.chatType,
                        success: function (argument) {},
                        fail: function(e){
                            console.log("Fail"); //Fail to send a message if mute or block the message
                        }
                    });
                    WebIM.conn.send(msg.body);
                }
            });
        }
    }
});
}
```

The example of a file message sent by a single chat is as follows:

``` javascript
// Single chat to send file message
var sendPrivateFile = function () {
    var id = conn.getUniqueId();                   // Generate local message id
    var msg = new WebIM.message('file', id);        // Create file message
    var input = document.getElementById('file');  // Select the input of the file
    var file = WebIM.utils.getFileUrl(input);      // Convert files to binary files
    var allowType = {
        'jpg': true,
        'gif': true,
        'png': true,
        'bmp': true,
        'zip': true,
        'txt': true,
        'doc': true,
        'pdf': true
    };
    if (file.filetype.toLowerCase() in allowType) {
        var option = {
            file: file,
            to: 'username',                       // The recipient of the message
            chatType: 'singleChat',               // Set to single chat
            onFileUploadError: function () {      // Message upload failed
                console.log('onFileUploadError');
            },
            onFileUploadComplete: function () {   // Message uploaded successfully
                console.log('onFileUploadComplete');
            },
            success: function () {                // Message sent successfully
                console.log('Success');
            },
            fail: function(e){
                console.log("Fail");              //Fail to send a message if mute or block the message
            },
            flashUpload: WebIM.flashUpload,
            ext: {file_length: file.data.size}
        };
        msg.set(option);
        conn.send(msg.body);
    }
};
```

The example of sending an audio message in a single chat is as follows:

``` javascript
// Single chat to send audio message
var sendPrivateAudio = function () {
    var id = conn.getUniqueId();                   // Generate local message id
    var msg = new WebIM.message('audio', id);      // Create audio message
    var input = document.getElementById('audio');  // Select audio input
    var file = WebIM.utils.getFileUrl(input);      // Convert audio to binary file
    var allowType = {
        'mp3': true,
        'amr': true,
        'wmv': true
    };
    if (file.filetype.toLowerCase() in allowType) {
        var option = {
            file: file,
            length: '3',                          // Audio file duration, unit (s)
            to: 'username',                       // The recipient of the message
            chatType: 'singleChat',               // Set to single chat
            onFileUploadError: function () {      // Message upload failed
                console.log('onFileUploadError');
            },
            onFileUploadComplete: function () {   // Message uploaded successfully
                console.log('onFileUploadComplete');
            },
            success: function () {                // Message sent successfully
                console.log('Success');
            },
            fail: function(e){
                console.log("Fail");              //Fail to send a message if mute or block the message
            },
            flashUpload: WebIM.flashUpload,
            ext: {file_length: file.data.size}
        };
        msg.set(option);
        conn.send(msg.body);
    }
};
```

The example of sending a video message in a single chat is as follows:

``` javascript
// Single chat to send video message
var sendPrivateVideo = function () {
    var id = conn.getUniqueId();                   // Generate local message id
    var msg = new WebIM.message('video', id);      // Create video message
    var input = document.getElementById('video');  // Select video input
    var file = WebIM.utils.getFileUrl(input);      // Convert video to binary file
    var allowType = {
        'mp4': true,
        'wmv': true,
        'avi': true,
        'rmvb':true,
        'mkv':true
    };
    if (file.filetype.toLowerCase() in allowType) {
        var option = {
            file: file,
            to: 'username',                       // The recipient of the message
            chatType: singleChat,                 // Set to single chat
            onFileUploadError: function () {      // Message upload failed
                console.log('onFileUploadError');
            },
            onFileUploadComplete: function () {   // Message uploaded successfully
                console.log('onFileUploadComplete');
            },
            success: function () {                // Message sent successfully
                console.log('Success');
            },
            fail: function(e){
                console.log("Fail");              // Fail to send a message if mute or block the message
            },
            flashUpload: WebIM.flashUpload,
            ext: {file_length: file.data.size}
        };
        msg.set(option);
        conn.send(msg.body);
    }
};
```
------------------------------------------------------------------------

### Send image message with url 

APP client side needs to be downloaded by the developer, and the Web client side needs to be set `useOwnUploadFun: true` in WebIMConfig.js.

The example of single chat to send image message through URL:

``` javascript
//  Send image messages via URL in single chat
 var sendPrivateUrlImg = function () {
    var id = conn.getUniqueId();                   // Generate local message id
    var msg = new WebIM.message('img', id);        // Create image message
    var option = {
        body: {
          type: 'file',
          url: url,
          size: {
            width: msg.width,
            height: msg.height,
           },
          length: msg.length,
          filename: msg.file.filename,
          filetype: msg.filetype
        },
        to: 'username',  // The recipient of the message
    };
    msg.set(option);
    conn.send(msg.body);
 }
```
------------------------------------
### Send custom message

The example of a custom message sent by a single chat is as follows:

``` javascript
var sendCustomMsg = function () {
    var id = conn.getUniqueId();                 // Generate local message id
    var msg = new WebIM.message('custom', id);   // Create custom message
    var customEvent = "customEvent";             // Create custom event
    var customExts = {};                         // Message content, key/value needs string type
    msg.set({
        to: 'username',                          // The recipient of the message(user id)
        customEvent,
        customExts,
        ext:{},                                  // Message extension
        roomType: false,
        success: function (id, serverMsgId) {},
        fail: function(e){}
    });
    conn.send(msg.body);
};
```

------------------------------------------------------------------------

## Receive message

Check the callback function, the callback function code for receiving various messages is as follows:

``` javascript
conn.listen({
    onOpened: function ( message ) {          //Successful connection callback
    },  
    onClosed: function ( message ) {},         //Connection closed callback
    onTextMessage: function ( message ) {},    //Receive text message
    onEmojiMessage: function ( message ) {},   //Receive emoji message
    onPictureMessage: function ( message ) {}, //Receive image message
    onCmdMessage: function ( message ) {},     //Receive command message
    onAudioMessage: function ( message ) {},   //Receive audio message 
    onLocationMessage: function ( message ) {},//Receive receive location
    onFileMessage: function ( message ) {},    //Receive file message
    onCustomMessage: function ( message ) {},  //Receive custom message
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
    onInviteMessage: function ( message ) {},  //Process group invitations
    onOnline: function () {},                  //The local network connection is successful
    onOffline: function () {},                 //The local network is offline
    onError: function ( message ) {},          //Failed callback
    onBlacklistUpdate: function (list) {       //Blacklist change
        // Query the blacklist, block a friend, and remove a friend from the blacklist will call back this function. The list is all the information of the existing friends in the blacklist
        console.log(list);
    },
    onRecallMessage: function( message ){},    //Reveive message withdrawal receipt
    onReceivedMessage: function(message){},    //Reveive message delivery server receipt
    onDeliveredMessage: function(message){},   //Reveive message delivery client receipt
    onReadMessage: function(message){},        //Reveive message read receipt
    onCreateGroup: function(message){},        //Receipt of successful group creation (createGroupNew needs to be called) 
    onMutedMessage: function(message){},       //If a user is banned in group A, this callback will be sent to group A and the message will not be delivered to other members of the group
    onChannelMessage: function(message){}      //Receive the read receipt of the entire conversation, and the message will be received in this callback when the other party sends a channel ack 
});
```

-------------

## Process the message

Here are examples of some special messages processing 
-   Emoji message

-   Image message 

-   Audio message

### Emoji message

Processing example of receiving emoji message：

``` javascript
conn.listen({
    onEmojiMessage: function (message) {
        console.log('Emoji');
        var data = message.data;
        for(var i = 0 , l = data.length ; i < l ; i++){
            console.log(data[i]);
        }
    },   //Receive emoji message
});
```

**Note:** When the Emoji attribute is added to WebIM, if the sent message contains specific strings in WebIM.Emoji, connection will automatically combine these strings and other text into an array in order, each array The structure of the element is
**{type:\'emoji(or txt)\',  data: msg(or src)}**

When type=\'emoji\', data means **path of emoji image**;

When type=\'txt\', data means **text message**. 

### Image message

The example of processing received image message:

``` javascript
conn.listen({
    onPictureMessage: function (message) {
        console.log("Location of Picture is ", message.url);
    }, //Reveive image message
});
```

### Audio message

Examples of processing received audio messages: 

``` javascript
conn.listen({
  onAudioMessage: function ( message ) {
    // Receive audio messages here
  }
})

// web
addAudioMessage: (message, bodyType) => {
  return (dispatch, getState) => {
    let options = {
          url: message.url,
          headers: {
            Accept: 'audio/mp3'
          },
          onFileDownloadComplete: function (response) {
            let objectUrl = WebIM.utils.parseDownloadResponse.call(WebIM.conn, response)
            message.audioSrcUrl = message.url
              message.url = objectUrl
            },
          onFileDownloadError: function () {}
        }
      WebIM.utils.download.call(WebIM.conn, options)
   }
}
```

**Note：**

-   Pictures and audio messages need to be downloaded first, and then displayed 
    or played.

------------------------------------------------------------------------

## New message reminder

When the SDK receives a new message, it will forward it to the logged-in user directly. After receiving the message, the demo will display the number of messages in red behind the friend or group. The developer change the its styles. 

------------------------------------------------------------------------

## Conversation list

`Need to contact a business colleague to open separately`

After sending a message to a target user or group, the target user or group will be added to the conversation list automatically. In addition, the conversation list can be queried by calling getSessionList. It is recommended that a page only requires to be called once at the initialization. To use this function, you need to contact your business manager to activate it. (You can scan the QR code to contact your business manager on the home page of the Agora Communication Cloud Management Backstage)
Special note: Do not use a mixed-case ID for the login ID. If the mixed-case ID is used in the pull session list, the pull session list will be empty.

``` javascript
WebIM.conn.getSessionList().then((res) => {
    console.log(res)
    /**
    Return parameter description
    channel_infos - all sessions
    channel_id - session id, username@easemob.com means single chat, groupid@conference.easemob.com means group chat
    meta - the last message
    unread_num - the number of unread messages in the current session
    
    data{
        channel_infos:[
            {
                channel_id: 'easemob-demo#chatdemoui_username@easemob.com',
                meta: {},
                unread_num: 0
            },
            {
                channel_id: 'easemob-demo#chatdemoui_93734273351681@conference.easemob.com',
                meta: {
                    from: "easemob-demo#chatdemoui_zdtest@easemob.com/webim_1610159114836",
                    id: "827197124377577640",
                    payload: "{"bodies":[{"msg":"1","type":"txt"}],"ext":{},"from":"zdtest","to":"93734273351681"}",
                    timestamp: 1610161638919,
                    to: "easemob-demo#chatdemoui_93734273351681@conference.easemob.com"
                },
                unread_num: 0
            }
        ]
    }
    */
    
})
```

When it is needed to clear the number of unread messages of the conversation, Please check the channel ack in the message receipt 

------------------------------------------------------------------------

## Message receipt

Single chat:

-Delivered receipt: Configure delivery to true in webim.config.js, and the delivery receipt will be automatically sent when a message is received. The callback function for the other party to receive the delivery receipt is onDeliveredMessage 

-   Read receipt:

1. When you think the user has read a specific message(s), you can generate a read receipt and send it back to the other party, and the other party will receive the read receipt in the onReadMessage callback
2. You can also reply to the channel ack message for the entire conversation, indicating that all the messages in the conversation have been read. This receipt message is to clear the unread messages in the conversation list obtained through getSessionList. For example, call getSessionList to get the conversation list, and the number of unread messages in one of the conversations is 5, then you can reply a channel message when you click on this conversation, this conversation The number of unread messages will be cleared. 

**Single chat to send read receipt**

``` javascript
var bodyId = message.id;         // The id of the message that needs to send the read receipt
var msg = new WebIM.message('read',conn.getUniqueId());
msg.set({
    id: bodyId
    ,to: message.from
});
conn.send(msg.body);
```

**Send the entire conversation read receipt**

``` javascript
var msg = new WebIM.message('channel',conn.getUniqueId());
msg.set({
    to: 'username'
});

// If it is group chat 
msg.set({
    to: 'groupid'，
    chatType: 'groupChat'
});

conn.send(msg.body);
```

