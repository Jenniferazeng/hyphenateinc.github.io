---
title: Message Withdraw
keywords: android
sidebar: android_sidebar
toc: true
permalink: android_message_withdraw.html
folder: android
---

The message withdrawal function can withdraw messages sent out within a certain period of time. The message withdrawal time limit is 2 minutes by default, which can be individually set in units of AppKey according to the developer's needs. If you need to modify it, please contact Agora Sales.

Message withdrawal is a value-added feature, please contact Agora Sales to activate.

``` java
ChatClient.getInstance().chatManager().recallMessage(contextMenuMessage);
```
------------------------------------------------------------------------
