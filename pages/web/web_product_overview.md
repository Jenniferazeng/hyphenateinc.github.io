---
title: web Produce Overview
keywords: web
sidebar: web_sidebar
toc: true
permalink: web_product_overview.html
folder: web
---
## Web IM Introduction 

Agora Web IM SDK provides complete instant messaging function development capabilities, <u>shields/encapsulate and block</u> its internal complex details, and provides a relatively simple and concise API interface to facilitate third-party applications to quickly integrate instant messaging functions for PC/mobile Web applications

## Function Description

Agora Web IM SDK already supports the following functions: 

-   The SDK itself already supports the mutual sending of text, expressions, pictures, audio, and address messages between IE9+, 
    FireFox10+, Chrome54+, and Safari6+. 

-   SDK supports the functions of adding and deleting friends between the web client side and Android/iOS client side.

-   SDK supports sending text, emoji, image, audio, and address messages among IOS and Android SDK.


-   The SDK handles messages as follows:

     - Text and emotion messages can be sent and received directly. 
    - Attachment(pictures, audios, files, etc.). The SDK uploads the attachment to the server, and then sends the basic information of the attachment (the attachment URL and file name uploaded by the sender). The receiver downloads them from the server in the form of a stream to the local processing according to the attachment URL, secret, and its own login information. 
    
* Provide Demo for reference. Demo has implemented chat, add/delete friends, group management, blacklist, audio and video   
        functions. 
        Note: 
    -  Demo Picture message format supported by default：PNG、JPG、BMP、GIF
    -  Demo Audio message format supported by default：MP3、AMR、WMV
    -  Demo File message format supported by default：zip、txt、doc、PDF

## Web IM Demo

Agora Web IM Demo shows how to use Agora Web IM SDK and quickly creates a complete web chat example like WeChat.
The shown functions include:

-   log in, log out, operate friends, send and receive individual messages/group messages, etc.

-   View public groups by page, search public groups by id 

-   Add ip policy function for http access to prevent DNS hijacking（`isHttpDNS:true`）

-   Web IM Demo supports mobile device layout 

-   Web IM Demo supports browser local cache(IndexDB)

Agora Web IM demo source code is open-source on GitHub [react version](https://github.com/easemob/webim)
[vue version](https://github.com/easemob/webim-vue-demo) to help developers better learn and understand Easemob SDK.

Demo uses the react framework and supports advanced browsers such as Microsoft Edge, Chrome54+, and Firefox. 
The SDK supports IE9+. 


## Compatibility

Web IM SDk:

-   Support IE9+, and all other modern browsers, support mobile WeChat and QQ

Web IM Demo:

-   Web IM H5 supports all modern browsers, mobile WeChat and QQ
-   IE6-11 is not supported currently, only support Microsoft Edge

Web IM SimpleDemo:

-   IE9+
-   Support all modern browsers, support mobile WeChat and QQ 

## Common Problems

Reference：[Easemob Knowledge Base](https://im.tickets.easemob.com/kb/index.php)

Still have questions, don't know how to solve it?

1\. Go to [Work Order System](https://im.tickets.easemob.com/) and ask questions

2\. The work order description should include but is not limited to the following information

-   Access address of your page？
-   Provide the apiUrl, socketUrl, appKey and your test login account password in your webim.config.js ？
-   Is the connection to your page established normally, and is the onOpen monitoring triggered?
-   Provide the log information of all consoles (see the figure below for how to open the console)



