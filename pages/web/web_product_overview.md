---
title: Product Overview
keywords: web
sidebar: web_sidebar
toc: true
permalink: web_product_overview.html
folder: web
---
## Overview

AgoraChat is a cloud-based provider of in-app chat. We provide the SDKs and a hosted Platform API to add chat to mobile and web apps. We offer the ability to add one-to-one chat, group chat, chat rooms, text messages, image and file sharing, emojis, typing indicators, delivery status and self-destructing messages.

Stop reinventing the wheel and spending valuable engineering resources on building a chat infrastructure yourself. Chat has your in-app chat system covered, so you can focus on building and optimizing the core features of your app.

Chat provides SDKs for native mobile apps for Android and iOS, Javascript for Web, and the APIs for server communication between the Chat server and your developer server. 

## Client Server Relationship
![Client Server Relationship](/images/index/PlatformArchitecture.jpeg)
## Chat Platform Advantages

Chat offers five core architecture design enhancements that efficiently handle a variety of complex services:

1. Asynchronous - High concurrent asynchronous architecture that can easily handle millions of concurrent requests.
2. Stateless - Stateless modules allow services to seamlessly switch between clusters in real time.
3. Cluster Service - Ensures high availability.
4. Horizontal Scaling - Optimized server configuration to ensure that each service cluster has high horizontal scalability.
5. Peak Traffic Handling - Resource optimization with additional system redundancy, 50% for core services and 30% for other services, which takes care of the peak volume in various conditions.

## Chat Protocol

- MSYNC is Chat's patent pending protocol that is optimized for mobile device performance and built to handle sporadic and unstable mobile networks. It is based on a binary format instead of raw text like XML, which is able to compress message data up to 80%. MSYNC also reduces the message relay between the client and server. As a result, Chat is able to deliver more information with significantly less overhead, less network bandwidth consumption, and better battery preservation. In addition, MSYNC is based on a persistent socket connection to establish real-time message delivery with extremely low latency in milliseconds scale. 

- Chat employs a smart heartbeat, meaning it adjusts the frequency of packet relay dynamically based on the communication channel and environment. It minimizes the mobile device power consumption and cellular data usage without compromising connection continuity.

## Features 

|  Feature                      | Description     |
|  :----:                       |  :----         |
|  User Management     |   1. Provide basic user management functions, add/delete friends, and blacklist friends. Support user metadata, can store user profile information for easy integration.  |
|  Message    |  Support all message types, including: text, emoji, picture, voice, video, location, files, CMD, custom messages.  |
|  Group      |  Provides a complete group management function, supporting different roles in group: owner, administrator and member. Support group blacklist, user ban and other functions.  |
|  Chatroom      |   4. Provides a complete chatroom management function and supports live interactive scenes.  |
|  Multi-Device      |   Support one same account to log in to multiple devices at the same time, message roaming synchronization.  |
|  Messageing Roaming      |  The roaming service can realize that the terminal user's historical messages are saved and synchronized through the server to ensure that the messages are synchronized in real time  |
|  Message Withdraw      |   Users can withdraw the message within the withdrawal time limit after the message is sent. |

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

