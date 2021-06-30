---
title: Product Overview
keywords: server
sidebar: server_sidebar
toc: true
permalink: server_product_overview.html
folder: server
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


## REST Platform Overview

REST (Representational State Transfer) is a lightweight style of Web Service architecture that can be translated as "representational state transfer". It is significantly simpler to implement and operate than SOAP and XML-RPC, can be implemented entirely through the HTTP protocol, and can also use the cache Cache to improve the response speed, performance, efficiency and ease of use are better than the SOAP protocol.

The REST architecture follows the CRUD principle.
The CRUD principle requires only four actions for resources: Create, Read, Update, and Delete to complete their operations and processing. These four operations are atomic operations, and the operations on resources include getting, creating, modifying, and deleting resources, which correspond exactly to
The GET, POST, PUT, and DELETE methods provided by the HTTP protocol, so REST limits HTTP operations on a URL resources to POST, GET, PUT, and DELETE. This design and development approach for web application reduces development complexity and improves system scalability.

### Platform API

The Platform API platform provides a multi-tenant user system where resources are described in the form of collections, which include DataBase, enterprises (orgs), application (apps), Chat users (users), groups (chatgroups), messages (chatmessages), files (chatfiles), and so on. the inclusion relationship between them is:

- DB = {org1, org2, ...}
- org = {app1, app2, ...}
- app = {users, messages, chatfiles, chatmessages, chatgroups, ...}
- users = {user1, user2, ...}
- messages = {message1, message2, ...}
- chatfiles = {chatfile1, chatfile2, ...}
- chatmessages = {chatmessage1, chatmessage2, ...}
- chatgroups = {group1, group2, ...}

Multi-tenancy is a software architecture that supports one instance serving multiple users (Customers), each of which is called a tenant. The software gives the Tenant the ability to customize parts of the system, such as user interface colors or business rules, but they cannot customize the code that modifies the software.

In fact, the meaning of multi-tenancy has been extended in the cloud computing space. Software-as-a-Service (SaaS) providers, for example, use application running on a single database instance to provide web access to multiple users. In such a scenario, data is isolated between tenants and each user's data is guaranteed to be invisible to other tenants.

In Chat service system, user data between different orgs are isolated from each other, and different APPs under the same org The user data between the same org are isolated from each other.

![](/images/server/rest_platform_service_integration.png)

### Platform API server

The server-side interfaces of Chat are provided by means of [Platform API services](http://zh.wikipedia.org/zh-cn/Platform API). The Platform API is based on the simplest HTTP protocol and provides support in various programming languages.

### Platform API client

The Platform API client is the program side that calls the Platform API, and there are various ways to call it: Linux curl, browser, programming language HTTP request access implementation, etc.

Calling the Platform API is essentially sending an HTTP request. Only you may commonly use HTTP GET and HTTP POST requests, but HTTP PUT and HTTP DELETE are also often used in Platform API.  In Platform API, these four operations are called verbs, which can be (but are not particularly accurate) imagined as adding, deleting, and checking.

The objects that the verbs operate on are called "resources" in Platform API,which is URLs, and these are also part of the standard HTTP protocol. In fact, when we open a website in a browser, for example, [Chat's website](http://www.easemob.com), the browser is actually sending an HTTP GET request to the web server.

Note that the Platform API is JSON-based, so when constructing an HTTP request, you need to specify in the HTTP HEADER.

<table border="1" cellspacing="0" bordercolor="#000000">
  <tr>
    <th>Header_name</th>
    <th>Header_value</th>
    <th>Description</th>
  </tr>
  <tr>
    <td>Accept</td>
    <td>application/json</td>
    <td>The type of data returned by the server to the client</td>
  </tr>
  <tr>
    <td>Content-Type</td>
    <td>application/json</td>
    <td>The type of data that the client sends to the server</td>
  </tr>
</table>

### JAVA

In Java, there are various Platform API client implementations, such as JBOss RestEasy, Sun Jersey, Dropwizard, Apache HTTPClient. We recommend using [Jersey](https://jersey.java.net/) to call the Platform API service.

Jersy 2.x implements the JAX-RS 2.0 specification and provides asynchronous support. However, Jersey 2.x requires JDK 1.7 support, so if your server-side application does not yet have access to JDK 1.7, then you need to use the Jersey 1.x version. There are also many people who use [Apache Http Client](http://hc.apache.org/), we do not recommend the direct use of this library, mainly because this library is relatively low-level, need to deal with a lot of their own things, the API is also relatively cumbersome.

### PYTHON

For Python programs, we recommend using [Requests](http://docs.python-requests.org/en/latest/) library to call Ring's Platform API service.

------------------------------------------------------------------------

## Basic architecture of Chat Server

When a user registers in the [Developer Console](https://console.easemob.com/), he/she needs to fill in an "Enterprise ID". This is because Chat is a multi-tenant cloud service platform, and Chat supports a two-level structure of "Enterprise" (or team) - "App". That is, in the Chat platform, the data between each enterprise ID is strictly isolated from each other, and the data between each APP within each enterprise ID is also strictly isolated from each other.

Imagine this model: there are three departments in a company, each department is responsible for one APP, then this company can register an account of Chat, and then create three APPs (in Chat) under the name of this account, each APP in Chat APP corresponds to one department's APP.

In this way, the account at the time of the initial registration is this company's corporate administrator account in Chat, and the corporate administrator can create new APPs, and create other enterprise administrators. In terms of access rights, the enterprise administrator has the highest rights and can see all the APPs under their own enterprise ID. At the same time, as mentioned above, the data between the APPs under the same enterprise ID is also isolated.
APPs under the same enterprise ID are isolated from each other, so it is completely possible to create users with the same user name in both APPs. So you can create users with the same username in both APPs.

On the other hand, if you are just an individual developer developing an APP, then you can fill in the Enterprise ID with any words, as long as it is same as any existing enterprise ID of Chat. After successful creation, the APP cannot be deleted manually by users, and Chat will keep all the APP data by default. APP data will be retained by default. If you want to delete the APP, you need to contact Chat to complete the operation.

Finally, since Chat provides Platform API service, and as mentioned above, Platform API is essentially a service that can be accessed via GET/POST/PUT/DELETE to control resources (URLs). So you can actually see that this idea is also reflected in Chat's Platform API.

### Platform API example

**Suppose an enterprise ID is chat-demo, and there is an APP under this enterprise named
chatdemoui, then the Platform API of Chat will look like the following:**

Create a new user under this APP

<table border="1" cellspacing="0" bordercolor="#000000">
  <tr>
    <th>POST</th>
    <th>/chat-demo/chatdemoui/users</th>
  </tr>
</table>

Get all users under this APP

<table border="1" cellspacing="0" bordercolor="#000000">
  <tr>
    <th>GET</th>
    <th>/chat-demo/chatdemoui/users</th>
  </tr>
</table>
  
Modify the details of the user example under this APP

<table border="1" cellspacing="0" bordercolor="#000000">
  <tr>
    <th>PUT</th>
    <th>/chat-demo/chatdemoui/users/example</th>
  </tr>
</table>

Delete a user under this APP example

<table border="1" cellspacing="0" bordercolor="#000000">
  <tr>
    <th>DELETE</th>
    <th>/chat-demo/chatdemoui/users/example</th>
  </tr>
</table>

From the above URL rules, you can also see the progressive relationship between "Enterprise" - "App" - "User".

 Explanation of Terms

<table border="1" cellspacing="0" bordercolor="#000000">
  <tr>
    <th>Terms</th>
    <th>Explanation</th>
  </tr>
  <tr>
    <td>org_name</td>
    <td>The unique identifier of an enterprise, the enterprise ID that developers fill in when they register their accounts in <a href="https://console.easemob.com/"> Developer Console </a></td>
  </tr>
  <tr>
    <td>app_name</td>
    <td>The unique identifier of an "app" under the same "company", the "app name" that the developer fills in when creating the app in <a href="https://console.easemob.com/"> Developer Console </a>. app_name can only be a combination of letters, numbers, and horizontal lines. The length cannot exceed 32</td>
  </tr>
  <tr>
    <td>org_admin</td>
    <td>The "user name" that the developer fills in when registering in the <a href="https://console.easemob.com/"> Developer Console </a>. Enterprise administrator has the permission to operate all resources under the enterprise account</td>
  </tr>
  <tr>
    <td>AppKey</td>
    <td>A unique identifier for the APP, the rule is ${org_name}#${app_name}</td>
  </tr>
</table>

### Security

Chat's Platform API service is entirely based on HTTPS This protocol ensures that no one else can steal it when it is called.

Chat's background service API is secure based on OAuth 2.0 standard and RBAC (Role Based Access Control) permission model.

Before calling the background service of Chat, you need to login to get the token, and the way to get the token is different depending on the role of the request initiator.

For more information on OAuth 2.0, see [Understanding OAuth 2.0](https://oauth.net/2/).
