---
title: send first message
keywords: web
sidebar: web_sidebar
toc: true
permalink: web_send_first_message.html
folder: web
---
1、Install the Chat SDK

Web SDK has been released to[NPM](https://www.npmjs.com/package/agora-chat-sdk). Use the following methods to integrate:
- Install the Web SDK via NPM.
```bash
  npm install agora-chat-sdk --save
```
- Import first, then use SDK APIs.
```bash
  import websdk from "agora-chat-sdk"
```

2、 Initialize the Chat SDK

```js
const WebIM = {};
const conn = WebIM.conn = new websdk.connection({
    appKey: 'your app key',
    isHttpDNS: true,
    https: true
})
```
3、Login chat server

```js
const options = { 
  user: 'username',
  pwd: 'password',
  appKey: 'your app key'
};
conn.open(options);
```

4、Construct and send the message

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