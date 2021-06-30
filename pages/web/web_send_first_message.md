---
title: web send first message
keywords: web
sidebar: web_sidebar
toc: true
permalink: web_send_first_message.html
folder: web
---

# Send your first message

## Follow these steps to send your first message

1、 Initialize the Chat SDK

```js
const conn = WebIM.conn = new WebIM.connection({
    appKey: 'your app key',
    isHttpDNS: true,
    https: true
})
```

2、Login chat server

```js
const options = { 
  user: 'username',
  pwd: 'password',
  appKey: 'your app key'
};
conn.open(options);
```

3、Construct and send the message

```js
let id = conn.getUniqueId()
let msg = new WebIM.message('txt', id);
msg.set({
    msg: 'message content', 
    to: 'username',
    chatType: 'singleChat',
    success: function () {
        console.log('send private text Success');  
    }, 
    fail: function(e){ }
});
conn.send(msg.body);
```