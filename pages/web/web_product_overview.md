---
title: Produce Overview
keywords: web
sidebar: web_sidebar
toc: true
permalink: web_product_overview.html
folder: web
---
## Agora chat sdk Introduction 

Agora chat sdk provides complete instant messaging function development capabilities, <u>shields/encapsulate and block</u> its internal complex details, and provides a relatively simple and concise API interface to facilitate third-party applications to quickly integrate instant messaging functions for PC/mobile Web applications

## Function Description

Agora chat sdk already supports the following functions: 

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

## Web Chat Demo

Agora Web Chat Demo shows how to use Agora Web Chat SDK and quickly creates a complete web chat example like WeChat.
The shown functions include:

-   log in, log out, operate friends, send and receive individual messages/group messages, etc.

-   View public groups by page, search public groups by id 

-   Add ip policy function for http access to prevent DNS hijacking（`isHttpDNS:true`）

-   Web Chat Demo supports mobile device layout 

-   Web Chat Demo supports browser local cache(IndexDB)

Agora Web Chat demo source code is open-source on GitHub [react version](https://github.com/HyphenateInc/Hyphenate-Demo-Web)

Demo uses the react framework and supports advanced browsers such as Microsoft Edge, Chrome54+, and Firefox. 
The SDK supports IE9+. 

