---
title: Group chat read receipt
keywords: web
sidebar: web_sidebar
toc: true
permalink: web_grp_msg_read.html
folder: web
---

## Sending groups can receive messages with read receipts (group owner or administrator rights are required) 

``` javascript
 sendGroupReadMsg = () => {
  let id = conn.getUniqueId();                 // Generate local message id
  let msg = new WebIM.message('txt', id);      // Create text message
  msg.set({
     msg: 'message content',                  // Message content 
     to: 'username',                          // The recipient of the message (user id)
     chatType: 'groupChat',                   // Set to group chat 
     success: function (id, serverMsgId) {
         console.log('send private text Success');
     },
     fail: function(e){
         console.log("Send private text error");
     }
  });
  msg.body.msgConfig = { allowGroupAck: true } // read receipt is required to set this message 
  conn.send(msg.body);
}
 
```

-   Send the receipt after receiving the message that requires receipt

``` javascript
 sendReadMsg = () => {
  let msg = new WebIM.message("read", WebIM.conn.getUniqueId());
  msg.set({
      id：message.id,         // The id of the message that needs to send the read receipt
      to: 'groupId',
      msgConfig: { allowGroupAck: true },
      ackContent: JSON.stringify({}) // Receipt content
  })
  msg.setChatType('groupChat')
  WebIM.conn.send(msg.body);
}
```

## There are two situations to monitor the receipt of group messages: 1. Listen 
    to the receipt in the onReadMessage function online; 2. When receive the group message receipt offline, listen to the receipt in the onStatisticMessage function after logging in. 

``` javascript
// Monitor onReadMessage when you are online 
onReadMessage: (message) => {
  const { mid } = message;
  const msg = {
    id: mid
  };
  if(message.groupReadCount){
    // Message reads 
    msg.groupReadCount = message.groupReadCount[message.mid];
  }
}
      
// Receive the receipt offline, then monitor it here after logging in 
onStatisticMessage: (message) => {
  let statisticMsg = message.location && JSON.parse(message.location);
  let groupAck = statisticMsg.group_ack || [];
}
```

## View users who have read the message

``` jacascript
WebIM.conn.getGroupMsgReadUser({
    msgId,  // message id
    groupId // group id
}).then((res)=>{
    console.log(res)
})
```

