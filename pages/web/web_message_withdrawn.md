---
title: Message withdrawn
keywords: web
sidebar: web_sidebar
toc: true
permalink: web_message_withdrawn.html
folder: web
---

## Send a recall message. After receiving the message, the other party deletes the message to be withdrawn

sender
``` javascript
/**
  * Send a withdrawal message
 * @param {Object} option - 
 * @param {Object} option.mid -   Recall message id
 * @param {Object} option.to -   The recipient of the message
 * @param {Object} option.type -  chat (single chat) groupchat (group) chatroom (chat room)
 * @param {Object} option.success - Recall the successful callback
 * @param {Object} option.fail- Recall the failed callback (more than two minutes)
 */
WebIM.conn.recallMessage(option)
```

receiver
```js
conn.listen({
	onRecallMessage: function(message){
		//Delete message where id === message.mid
	}
})
```
