---
title: Product Overview
keywords: ios
sidebar: ios_sidebar
toc: true
permalink: ios_product_overview.html
folder: ios
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
|  Push notification      |   Support push platforms such as Apple APNS and Google FCM. |
|  Messageing Roaming      |  The roaming service can realize that the terminal user's historical messages are saved and synchronized through the server to ensure that the messages are synchronized in real time  |
|  Message Withdraw      |   Users can withdraw the message within the withdrawal time limit after the message is sent. |


------------------------------------------------------------------------


