---
title: Server Produce Overview
keywords: server
sidebar: server_sidebar
toc: true
permalink: server_product_overview.html
folder: server
---

## REST Platform Overview

REST (Representational State Transfer) is a lightweight style of Web Service architecture that can be translated as "representational state transfer". It is significantly simpler to implement and operate than SOAP and XML-RPC, can be implemented entirely through the HTTP protocol, and can also use the cache Cache to improve the response speed, performance, efficiency and ease of use are better than the SOAP protocol.

The REST architecture follows the CRUD principle.
The CRUD principle requires only four actions for resources: Create, Read, Update, and Delete to complete their operations and processing. These four operations are atomic operations, and the operations on resources include getting, creating, modifying, and deleting resources, which correspond exactly to
The GET, POST, PUT, and DELETE methods provided by the HTTP protocol, so REST limits HTTP operations on a URL resources to POST, GET, PUT, and DELETE. This design and development approach for web application reduces development complexity and improves system scalability.

More background on REST API can be found in [RESTful API
Design Guide](http://www.ruanyifeng.com/blog/2014/05/restful_api.html).

### Platform API

The Platform API platform provides a multi-tenant user system where resources are described in the form of collections, which include DataBase, enterprises (orgs), application (apps), IM users (users), groups (chatgroups), messages (chatmessages), files (chatfiles), and so on. the inclusion relationship between them is:

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

In Easemob service system, user data between different orgs are isolated from each other, and different APPs under the same org The user data between the same org are isolated from each other.

![](/images/rest_platform_service_integration.png)

### REST server

The server-side interfaces of Easemob are provided by means of [REST services](http://zh.wikipedia.org/zh-cn/REST). The REST API is based on the simplest HTTP protocol and provides support in various programming languages.

### REST client

The REST client is the program side that calls the REST API, and there are various ways to call it: Linux curl, browser, programming language HTTP request access implementation, etc.

Calling the REST API is essentially sending an HTTP request. Only you may commonly use HTTP GET and HTTP POST requests, but HTTP PUT and HTTP DELETE are also often used in REST.  In REST, these four operations are called verbs, which can be (but are not particularly accurate) imagined as adding, deleting, and checking.

The objects that the verbs operate on are called "resources" in REST,which is URLs, and these are also part of the standard HTTP protocol. In fact, when we open a website in a browser, for example, [Easemob's website](http://www.easemob.com), the browser is actually sending an HTTP GET request to the web server.

Note that the Easemob REST API is JSON-based, so when constructing an HTTP request, you need to specify in the HTTP HEADER.

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

In Java, there are various REST client implementations, such as JBOss RestEasy, Sun Jersey, Dropwizard, Apache HTTPClient. We recommend using [Jersey](https://jersey.java.net/) to call the Easemob REST service.

Jersy 2.x implements the JAX-RS 2.0 specification and provides asynchronous support. However, Jersey 2.x requires JDK 1.7 support, so if your server-side application does not yet have access to JDK 1.7, then you need to use the Jersey 1.x version. There are also many people who use [Apache Http Client](http://hc.apache.org/), we do not recommend the direct use of this library, mainly because this library is relatively low-level, need to deal with a lot of their own things, the API is also relatively cumbersome.

### PYTHON

For Python programs, we recommend using [Requests](http://docs.python-requests.org/en/latest/) library to call Ring's REST service.

------------------------------------------------------------------------

## Basic architecture of Chat Server

When a user registers in the [Easemob developer management backend](https://console.easemob.com/), he/she needs to fill in an "Enterprise ID". This is because Easemob is a multi-tenant cloud service platform, and Easemob supports a two-level structure of "Enterprise" (or team) - "App". That is, in the Easemob platform, the data between each enterprise ID is strictly isolated from each other, and the data between each APP within each enterprise ID is also strictly isolated from each other.

Imagine this model: there are three departments in a company, each department is responsible for one APP, then this company can register an account of Easemob, and then create three APPs (in Easemob) under the name of this account, each APP in Easemob APP corresponds to one department's APP.

In this way, the account at the time of the initial registration is this company's corporate administrator account in Easemob, and the corporate administrator can create new APPs, and create other enterprise administrators. In terms of access rights, the enterprise administrator has the highest rights and can see all the APPs under their own enterprise ID. At the same time, as mentioned above, the data between the APPs under the same enterprise ID is also isolated.
APPs under the same enterprise ID are isolated from each other, so it is completely possible to create users with the same user name in both APPs. So you can create users with the same username in both APPs.

On the other hand, if you are just an individual developer developing an APP, then you can fill in the Enterprise ID with any words, as long as it is same as any existing enterprise ID of Easemob. After successful creation, the APP cannot be deleted manually by users, and Easemob will keep all the APP data by default. APP data will be retained by default. If you want to delete the APP, you need to contact Easemob to complete the operation.

Finally, since Easemob provides REST service, and as mentioned above, REST is essentially a service that can be accessed via GET/POST/PUT/DELETE to control resources (URLs). So you can actually see that this idea is also reflected in Easemob's REST API.

### REST API example

**Suppose an enterprise ID is chat-demo, and there is an APP under this enterprise named
chatdemoui, then the REST API of Easemob will look like the following:**

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
    <td>The unique identifier of an enterprise, the enterprise ID that developers fill in when they register their accounts in <a href="https://console.easemob.com/"> Easemob Developer Management Backend </a></td>
  </tr>
  <tr>
    <td>app_name</td>
    <td>The unique identifier of an "app" under the same "company", the "app name" that the developer fills in when creating the app in <a href="https://console.easemob.com/"> Easemob Developer Management Backend </a>. app_name can only be a combination of letters, numbers, and horizontal lines. The length cannot exceed 32</td>
  </tr>
  <tr>
    <td>org_admin</td>
    <td>The "user name" that the developer fills in when registering in the <a href="https://console.easemob.com/"> Easemob Developer Management Backend </a>. Enterprise administrator has the permission to operate all resources under the enterprise account</td>
  </tr>
  <tr>
    <td>AppKey</td>
    <td>A unique identifier for the APP, the rule is ${org_name}#${app_name}</td>
  </tr>
</table>

### Security

Easemob's REST service is entirely based on HTTPS This protocol ensures that no one else can steal it when it is called.

Easemob's background service API is secure based on OAuth 2.0 standard and RBAC (Role Based Access Control) permission model.

Before calling the background service of Easemob, you need to login to get the token, and the way to get the token is different depending on the role of the request initiator.

For more information on OAuth 2.0, see [Understanding OAuth 2.0](https://oauth.net/2/).
