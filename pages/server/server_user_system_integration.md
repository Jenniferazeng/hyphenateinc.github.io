---
title: User System Integration
keywords: server
sidebar: server_sidebar
toc: true
permalink: server_user_system_integration.html
folder: server
---

# User System Integration

------------------------------------------------------------------------

## Overview

Introduce the data structure and usage rules of Appkey and Easemob ID.

### Appkey data structure

When you apply for an AppKey, you will get a string in **xxxx#xxxx** format, the string can only consist of lowercase alphanumeric, the AppKey is the unique identifier of the Easemob application. The first half **org_name** is the unique tenant identifier under the multi-tenant system, and the second half **app_name** is the unique identifier of the app under the tenant (the app id filled in when creating an app in the Easemob backend is the app_name). In the following REST API, **/{org_name}/{app_name}** requests are made for a unique appkey. At present, the appkey registered by Easemob cannot be deleted by users themselves at the moment, If you are interested in APP deletion, you need to contact Easemob to complete the operation.

<table border="1px" cellspacing="0px" bordercolor="#000000">
  <tr>
    <th>Appkey</th>
    <th>xxxx</th>
    <th>Separator</th>
    <th>xxxx</th>
    <th>Description</th>
  </tr>
  <tr>
    <td>Unique identifier for Easemob app</td>
    <td>org_name</td>
    <td>\#</td>
    <td>app_name</td>
    <td>The app_name can only be a combination of letters, numbers, and horizontal lines. Length cannot exceed 32</td>
  </tr>
</table>

### Easemob ID data structure

As a chat channel, you only need to provide Easemob ID (also known as IM username) and password is all that is needed.

<table border="1" cellspacing="0" bordercolor="#000000">
  <tr>
    <th>Name</th>
    <th>Field Name</th>
    <th>Data Type</th>
    <th>Description</th>
  </tr>
  <tr>
    <td>Easemob ID</td>
    <td>username</td>
    <td>String</td>
    <td>Unique user name within the scope of AppKey, user ID is 64 bytes or less in length.</td>
  </tr>
  <tr>
    <td>User Password</td>
    <td>password</td>
    <td>String</td>
    <td>The password used by the user to log in to Easemob.</td>
  </tr>
</table>

### Rules for using Easemob ID

When the APP is integrated with Easemob, you need to integrate the APP system, create a Easemob account for each existing user (Easemob And when APP has new user registration, it needs to register in Easemob synchronously.

**When registering for an Easemob account, you need to pay attention to the rules of Easemob ID：**

-   Use a combination of English letters and/or numbers
-   Chinese is not available
-   No email addresses can be used
-   UUID cannot be used
-   The length of the user ID is 64 bytes or less
-   No spaces or special characters such as tic-tac-toe (#) in between
-   Allowed user name rules "\[a-z0-9\_-. \]\*" (a\~z lowercase letters/numbers/underscores/crosses/English periods), all others are not allowed
    **If it is an uppercase letter it will be automatically converted to lowercase**
-   Not case-sensitive. The system ignores case and considers AA, Aa, aa, aA to be the same. If the system already has a user with Easemob ID AA, and then try to register a new user with aa as Easemob ID, the system returns a duplicate user name, and so on, so it is recommended that users use lowercase letters, otherwise they may encounter some exceptions. However, please note: the form of Easemob ID in the data is still the form of the user's initial registration, and the upper case used in the registration is saved in upper case, and the lower case is saved in lower case. That is: if you register with AA, the ID saved by Easemob is AA; if you register with Aa, the ID saved by Easemob is Aa, and so on.

Also: This document may use the terms "Easemob ID" and "Easemob username", but please note that both mean the same thing here.

Because a user's Easemob ID and his username in the APP does not need to be the same, there just needs to be a clear correspondence. For example, if the user name is example\@easemob.com, when this user logs into the APP When this user logs into the APP, he can log into Easemob's server after successful login. so at this time, he only needs to be able to log in from example\@easemob.com to derive this user's Easemob ID.

**Note：**All of the following APIs require org administrator or APP administrator permission to access.

It is strongly recommended to protect the org administrator, APP administrator username and password as well as APP client_id and client_secret, and try to do the management of adding, deleting, changing and checking for Easemob users only in APP's server backend, including new user registration. For your information security, please make sure not to write org administrator or APP administrator username and password in the mobile client, because the mobile APP can be easily decompiled, which will lead others to get your administrator account and password and lead to data leakage.

------------------------------------------------------------------------

# REST API

Introduces the REST API that needs to be used in the integration process of the IM user system. The documentation details that can be tested online using the [Easemob REST API](http://api-docs.easemob.com/) embedded in the documentation for online testing.

## Request Domain

Easemob's REST API requests for domain names from different data centers：

<table border="1" cellspacing="0" bordercolor="#000000">
  <tr>
    <th>Data Center</th>
    <th>REST API Request Address</th>
  </tr>
  <tr>
    <td>Domestic Area 1</td>
    <td>a1.easemob.com</td>
  </tr>
  <tr>
    <td>Domestic Area 2</td>
    <td>a31.easemob.com</td>
  </tr>
  <tr>
    <td>Domestic VIP area</td>
    <td>Please consult with the business manager</td>
  </tr>
  <tr>
    <td>For Customer Service</td>
    <td>Please consult with your business manager</td>
  </tr>
  <tr>
    <td>Singapore Area 1</td>
    <td>a1-sgp.easemob.com</td>
  </tr>
</table>

The data center where the request is located can be viewed in the Easemob user administration backend > Request Information：

![](/images/console-01.png)

`Note：`

`1.To meet the business needs of different customers, Easemob has deployed data centers in multiple locations. The REST API request domain name varies from data center to data center. Please select the request domain name according to your data center.`

`2.Domestic VIP area, customer service area customers please contact the business manager to ask for the REST API request address.`

`3.Support http,https.`

## Get administrator permission

The REST API provided by Easemob requires permission to access it, which is reflected by sending an HTTP request with a token The following describes how to get the token. Note: When describing the API, the Parameter such as {APP's client_id} and other Parameter need to be replaced with specific values.

`Important Reminder: The server will return the token expiration date when getting the token, please refer to the expires_in field returned by the interface for the specific value. Due to network latency, the system does not guarantee that the token is absolutely valid within the expiration date indicated by this value, if you find that the token is used abnormally, please get a new token, for example, "http response code" returns 401. In addition, please do not send requests frequently to the server to get token, the same account will be blocked if you send this request more often than a certain number of times. Please take care of it!!`

The client_id and client_secret can be seen in the Easemob administration backend on the [APP details page](http://www.google.com).

#### HTTP Request

<table border="1" cellspacing="0" bordercolor="#000000">
  <tr>
    <th>POST</th>
    <th>/{org_name}/{app_name}/token</th>
  </tr>
</table>

#### Request Headers

<table border="1" cellspacing="0" bordercolor="#000000">
  <tr>
    <th>Parameter</th>
    <th>Description</th>
  </tr>
  <tr>
    <td>Content-Type</td>
    <td>application/json</td>
  </tr>
</table>

#### Request Body

<table border="1" cellspacing="0" bordercolor="#000000">
  <tr>
    <th>Parameter</th>
    <th>Description</th>
  </tr>
  <tr>
    <td>grant_type</td>
    <td>client_credentials</td>
  </tr>
  <tr>
    <td>client_id</td>
    <td>App's client_id, which can be found in <a href="https://console.easemob.com/app-detail/detail"> app details page </a></td>
  </tr>
  <tr>
    <td>client_secret</td>
    <td>App's client_secret, which can be found on <a href="https://console.easemob.com/app-detail/detail"> app details page </a></td>
  </tr>
</table>

#### Response Body

<table border="1" cellspacing="0" bordercolor="#000000">
  <tr>
    <th>Parameter</th>
    <th>Description</th>
  </tr>
  <tr>
    <td>access_token</td>
    <td>Valid token string</td>
  </tr>
  <tr>
    <td>expires_in</td>
    <td>token validity time in seconds, no need to get repeatedly during the validity period</td>
  </tr>
  <tr>
    <td>request</td>
    <td>The UUID value of the current App</td>
  </tr>
</table>

#### Request Example

``` php
curl -X POST -H 'Content-Type: application/json' -H 'Accept: application/json' -d '{ 
   "grant_type": "client_credentials",  
   "client_id": "YXA6i-Ak8Ol4Eei2l11ZjV-EAg", 
   "client_secret": "YXA6VunqiNxoB7IwXHInk1cGiXOOJfc"  
 }' 'http://a1.easemob.com/easemob-demo/testapp/token'
```

#### Examples of possible returned results

**returns a value of 200, indicating a successful token return**

``` json
{
  "access_token": "YWMte3bGuOukEeiTkNP4grL7iwAAAAAAAAAAAAAAAAAAAAGL4CTw6XgR6LaXXVmNX4QCAgMAAAFnKdc-ZgBPGgBFTrLhhyK8woMEI005emtrLJFJV6aoxsZSioSIZkr5kw",
  "expires_in": 5184000,
  "application": "8be024f0-e978-11e8-b697-5d598d5f8402"
}
```

**returns 400, indicating client_id or client_secret error**

``` json
{
  "error_description": "client_id does not match",
  "error": "invalid_grant"
}
```

If the returned result is <font color='red'> 429, 503 </font> or other <font color='red'> 5xx </font>, it may mean that the interface is flow restricted, please pause a little and retry. See [interface flow restriction description](/server_rest_interface_flow_limiting_instructions.html) for details

[Online testing using Easemob REST API](http://api-docs.easemob.com/)

------------------------------------------------------------------------

## User Management

Easemob provides several interfaces to manage the registration, acquisition, modification and deletion of IM users.

<table border="1" cellspacing="0" bordercolor="#000000">
  <tr>
    <th>Name</th>
    <th>Request</th>
    <th>Description</th>
  </tr>
  <tr>
    <td>Register a single user</td>
    <td>/{org_name}/{app_name}/users</td>
    <td>Register an IM user in open registration and authorized registration modes</td>
  </tr>
  <tr>
    <td>Bulk registration of users</td>
    <td>/{org_name}/{app_name}/users</td>
    <td>Register multiple IM users, only authorized registration is supported</td>
  </tr>
  <tr>
    <td>Get a single user</td>
    <td>/{org_name}/{app_name}/users/{username}</td>
    <td>Get a single IM user</td>
  </tr>
  <tr>
    <td>Bulk user acquisition</td>
    <td>/{org_name}/{app_name}/users</td>
    <td>Get multiple IM users under the application</td>
  </tr>
  <tr>
    <td>Delete a single user</td>
    <td>/{org_name}/{app_name}/users/{username}</td>
    <td>Bulk delete multiple IM users</td>
  </tr>
  <tr>
    <td>Bulk delete users</td>
    <td>/{org_name}/{app_name}/users</td>
    <td>Bulk delete multiple</td>
  </tr>
  <tr>
    <td>Modify user password</td>
    <td>/{org_name}/{app_name}/users/{username}/password</td>
    <td>Modify the password of the IM user</td>
  </tr>
  <tr>
    <td>Set the name to show in push messages</td>
    <td>/{org_name}/{app_name}/users/{username}</td>
    <td>Set the name displayed for IM users' push messages</td>
  </tr>
  <tr>
    <td>Set how to display push messages</td>
    <td>/{org_name}/{app_name}/users/{username}</td>
    <td>Set whether IM user push messages are displayed as notifications only or details</td>
  </tr>
  <tr>
    <td>Set no disturbance</td>
    <td>/{org_name}/{app_name}/users/{username}</td>
    <td>Set whether IM users enable Do Not Disturb mode and the time to turn it on/off</td>
  </tr>
</table> 

Create a new user in the org and APP specified by the URL Create a new user in two modes: **Open Registration** and **Authorized Registration**.

-   "Open registration" mode: when registering for an Easemob account, you do not need to bring your administrator identification information.
-   "Authorized Registration" mode: When registering for an Easemob account, you must bring your administrator identification information. It is recommended to use "Authorized Registration" to prevent people who have already geted the registration URLs and people with knowledge of the registration process from registering a large number of spam users to the server.

`Note：` \'\' \* The \${token} mentioned in the following API is a variable that needs to be replaced with the token geted through the APP's client_id and client_secret.
` ` \* When registering Easemob ids, it is recommended not to use ordered ids to prevent others from knowing the order of registered ids and sending a lot of spam messages maliciously. \'\'

\'\' \*

In the server side to their own user registration account at the same time, will go to call the rest interface of the Easemob letter to the user in the registration of a Easemob letter id and their own user binding, and return to the client, the client get their own server user account password login, and then take the Easemob letter id password in the login Easemob letter server\'\'

### Register a single user (open)

An interface that allows users to register an account by themselves with an account password after logging into the client SDK.

#### HTTP Request

<table border="1" cellspacing="0" bordercolor="#000000">
  <tr>
    <th>POST</th>
    <th>/{org_name}/{app_name}/users</th>
  </tr>
</table>

#### Request Headers

  <table border="1" cellspacing="0" bordercolor="#000000">
  <tr>
    <th>Parameter</th>
    <th>Description</th>
  </tr>
  <tr>
    <td>Content-Type</td>
    <td>application/json</td>
  </tr>
</table>

#### Request Body

  <table border="1" cellspacing="0" bordercolor="#000000">
  <tr>
    <th>Parameter</th>
    <th>Description</th>
  </tr>
  <tr>
    <td>username</td>
    <td>Easemob ID ;It is the unique login account of IM user name, the length cannot exceed 64 characters length</td>
  </tr>
  <tr>
    <td>password</td>
    <td>Login password, length cannot exceed 64 characters length</td>
  </tr>
  <tr>
    <td>nickname</td>
    <td>Nickname (optional), the nickname that will be used in iOS Apns push (only the nickname displayed in the push notification bar), not the nickname of the user's personal information, Easemob does not save the user's nickname, avatar and other personal information, you need to save it on your own server and bind it to the IM user name registered to your user, the length cannot exceed 100 characters</td>
  </tr>
</table>

#### Response Body

View the information contained in the entities field in the return value

<table border="1" cellspacing="0" bordercolor="#000000">
  <tr>
    <th>Parameter</th>
    <th>Description</th>
  </tr>
  <tr>
    <td>uuid</td>
    <td>User's UUID, identification field</td>
  </tr>
  <tr>
    <td>type</td>
    <td>"user" user type</td>
  </tr>
  <tr>
    <td>username</td>
    <td>Username, also known as Easemob ID</td>
  </tr>
  <tr>
    <td>nickname</td>
    <td>Nickname (optional), the nickname that will be used in iOS Apns push (only the nickname displayed in the push notification bar), not the nickname of the user's personal information, Easemob does not save the user's nickname, avatar and other personal information, you need to save it on your own server and bind it to the IM username registered to your user</td>
  </tr>
  <tr>
    <td>activated</td>
    <td>Whether the user is activated, "true" is activated, "false" is blocked, the block needs to be unblocked through the unblock interface in order to log in normally</td>
  </tr>
</table>

#### Request Example

``` php
curl -X POST -i "https://a1.easemob.com/easemob-demo/testapp/users" -d '{"username":"user1","password":"123","nickname":"testuser"}'
```

#### Examples of possible returned results

**returns 200, indicating that the IM user was created successfully**

``` json
{
  "action": "post",
  "application": "8be024f0-e978-11e8-b697-5d598d5f8402",
  "path": "/users",
  "uri": "https://a1.easemob.com/easemob-demo/testapp/users",
  "entities": [
    {
      "uuid": "0ffe2d80-ed76-11e8-8d66-279e3e1c214b",
      "type": "user",
      "created": 1542795196504,
      "modified": 1542795196504,
      "username": "user1",
      "activated": true,
      "nickname": "testuser"
    }
  ],
  "timestamp": 1542795196515,
  "duration": 0,
  "organization": "easemob-demo",
  "applicationName": "testapp"
}
```

**return value 400, means the user already exists, user name or password is empty, user name is not legal**

``` json
{
  "error": "duplicate_unique_property_exists",
  "timestamp": 1542795141631,
  "duration": 0,
  "exception": "org.apache.usergrid.persistence.exceptions.DuplicateUniquePropertyExistsException",
  "error_description": "Request null Entity user requires that property named username be unique, value of User1 exists"
}
```

If the return result is <font color='red'> 429, 503 </font> or other <font color='red'> 5xx </font>, it may mean that the interface is stream-limited, please pause for a while and retry. See [interface flow restriction description](/server_rest_interface_flow_limiting_instructions.html) for details

[Online testing using Easemob REST API](http://api-docs.easemob.com/)

------------------------------------------------------------------------

### Register a single user (authorization)

The server side needs to verify a valid token permission to operate and authorize the registered interface.

#### HTTP Request

<table border="1" cellspacing="0" bordercolor="#000000">
  <tr>
    <th>POST</th>
    <th>/{org_name}/{app_name}/users</th>
  </tr>
</table>

#### Request Headers

<table border="1" cellspacing="0" bordercolor="#000000">
  <tr>
    <th>Parameter</th>
    <th>Description</th>
  </tr>
  <tr>
    <td>Content-Type</td>
    <td>application/json</td>
  </tr>
  <tr>
    <td>Authorization</td>
    <td>Bearer \${token}</td>
  </tr>
</table>

#### Request Body

<table border="1" cellspacing="0" bordercolor="#000000">
  <tr>
    <th>Parameter</th>
    <th>Description</th>
  </tr>
  <tr>
    <td>username</td>
    <td>Easemob ID ;It is the unique login account of IM user name, the length cannot exceed 64 characters length</td>
  </tr>
  <tr>
    <td>password</td>
    <td>Login password, length cannot exceed 64 characters length</td>
  </tr>
  <tr>
    <td>nickname</td>
    <td>Nickname (optional), the nickname that will be used in iOS Apns push (only the nickname displayed in the push notification bar), not the nickname of the user's personal information, Easemob does not save the user's nickname, avatar and other personal information, you need to save it on your own server and bind it to the IM user name registered to your user, the length cannot exceed 100 characters</td>
  </tr>
</table>

#### Response Body

View the information contained in the entities field in the return value

<table border="1" cellspacing="0" bordercolor="#000000">
  <tr>
    <th>Parameter</th>
    <th>Description</th>
  </tr>
  <tr>
    <td>uuid</td>
    <td>User's UUID, identification field</td>
  </tr>
  <tr>
    <td>type</td>
    <td>"user" user type</td>
  </tr>
  <tr>
    <td>username</td>
    <td>Username, also known as Easemob ID</td>
  </tr>
  <tr>
    <td>nickname</td>
    <td>Nickname (optional), the nickname that will be used in iOS Apns push (only the nickname displayed in the push notification bar), not the nickname of the user's personal information, Easemob does not save the user's nickname, avatar and other personal information, you need to save it on your own server and bind it to the IM username registered to your user</td>
  </tr>
  <tr>
    <td>activated</td>
    <td>Whether the user is activated, "true" is activated, "false" is blocked, the block needs to be unblocked through the unblock interface in order to log in normally</td>
  </tr>
</table>

#### Request Example

``` php
curl -X POST -H 'Content-Type: application/json' -H 'Accept: application/json' -H 'Authorization: Bearer WMte3bGuOukEeiTkNP4grL7iwAAAAAAAAAAAAAAAAAAAAGL4CTw6XgR6LaXXVmNX4QCAgMAAAFnKdc-ZgBPGgBFTrLhhyK8woMEI005emtrLJFJV6aoxsZSioSIZkr5kw12' -d '[  
   {  
     "username": "user1",  
     "password": "123",  
     "nickname": "testuser"  
   }  
 ]' 'http://a1.easemob.com/easemob-demo/testapp/users'
```

#### Examples of possible returned results

**returns 200, indicating that the IM user was created successfully**

``` json
{
    "action": "post",
    "application": "a2e433a0-ab1a-11e2-a134-85fca932f094",
    "params": {},
    "path": "/users",
    "uri": "https://a1.easemob.com/easemob-demo/testapp/users",
    "entities": [{
        "uuid": "7f90f7ca-bb24-11e2-b2d0-6d8e359945e4",
        "type": "user",
        "created": 1368377620796,
        "modified": 1368377620796,
        "username": "user1",
        "activated": true
        "nickname": "testuser"
    }],
    "timestamp": 1368377620793,
    "duration": 125,
    "organization": "easemob-demo",
    "applicationName": "testapp"
}
```

**return value 400, means the user already exists, user name or password is empty, user name is not legal**

``` json
{
    "error": "duplicate_unique_property_exists",
    "timestamp": 1537871738097,
    "duration": 0,
    "exception": "org.apache.usergrid.persistence.exceptions.DuplicateUniquePropertyExistsException",
    "error_description": "Requestnull Entity user requires that property named username be unique, value of ccccc1 exists"
}
```

**Return value 401, means unauthorized \ [no token, token error, token expired \]**

``` json
{
  "error": "auth_bad_access_token",
  "timestamp": 1543479294349,
  "duration": 0,
  "exception": "org.apache.usergrid.rest.exceptions.SecurityException",
  "error_description": "Unable to authenticate due to corrupt access token"
}
```

If the return result is <font color='red'> 429, 503 </font> or other <font color='red'> 5xx </font>, it may mean that the interface is flow-limited, please pause slightly and retry. See [interface flow restriction description](/server_rest_interface_flow_limiting_instructions.html) for details

[Online testing using Easemob REST API](http://api-docs.easemob.com/)

------------------------------------------------------------------------

### Bulk registration of users

Batch registration is the authorized registration method, and the server needs to verify the valid token authority to operate. Each time the interface is called, there is a maximum limit on the number of registered users, see [interface limit description](/server_rest_interface_flow_limiting_instructions.html) for details.

#### HTTP Request

<table border="1" cellspacing="0" bordercolor="#000000">
  <tr>
    <th>POST</th>
    <th>/{org_name}/{app_name}/users</th>
  </tr>
</table>

#### Request Headers

<table border="1" cellspacing="0" bordercolor="#000000">
  <tr>
    <th>Parameter</th>
    <th>Description</th>
  </tr>
  <tr>
    <td>Content-Type</td>
    <td>application/json</td>
  </tr>
  <tr>
    <td>Authorization</td>
    <td>Bearer \${token}</td>
  </tr>
</table>

#### Request Body

<table border="1" cellspacing="0" bordercolor="#000000">
  <tr>
    <th>Parameter</th>
    <th>Description</th>
  </tr>
  <tr>
    <td>username</td>
    <td>Easemob ID ;It is the unique login account of IM user name, the length cannot exceed 64 characters length</td>
  </tr>
  <tr>
    <td>password</td>
    <td>Login password, length cannot exceed 64 characters length</td>
  </tr>
  <tr>
    <td>nickname</td>
    <td>Nickname (optional), the nickname that will be used in iOS Apns push (only the nickname displayed in the push notification bar), not the nickname of the user's personal information, Easemob does not save the user's nickname, avatar and other personal information, you need to save it on your own server and bind it to the IM user name registered to your user, the length cannot exceed 100 characters</td>
  </tr>
</table>

#### Response Body

<table border="1" cellspacing="0" bordercolor="#000000">
  <tr>
    <th>Parameter</th>
    <th>Description</th>
  </tr>
  <tr>
    <td>uuid</td>
    <td>User's UUID, identification field</td>
  </tr>
  <tr>
    <td>type</td>
    <td>"user" user type</td>
  </tr>
  <tr>
    <td>username</td>
    <td>Username, also known as Easemob ID</td>
  </tr>
  <tr>
    <td>nickname</td>
    <td>Nickname (optional), the nickname that will be used in iOS Apns push (only the nickname displayed in the push notification bar), not the nickname of the user's personal information, Easemob does not save the user's nickname, avatar and other personal information, you need to save it on your own server and bind it to the IM username registered to your user</td>
  </tr>
  <tr>
    <td>activated</td>
    <td>Whether the user is activated, "true" is activated, "false" is blocked, the block needs to be unblocked through the unblock interface in order to log in normally</td>
  </tr>
</table>

#### Request Example 1

``` php
curl -X POST -H "Authorization: Bearer YWMtP_8IisA-EeK-a5cNq4Jt3QAAAT7fI10IbPuKdRxUTjA9CNiZMnQIgk0LEUE" -i  "https://a1.easemob.com/easemob-demo/testapp/users" -d '[{"username":"user1", "password":"123","nickname":"testuser1"}, {"username":"user2", "password":"456","nickname":"testuser2"}]'
```

#### Examples of possible returned results

**returns a value of 200, indicating a successful token return**

``` json
{
  "action": "post",
  "application": "22bcffa0-8f86-11e6-9df8-516f6df68c6d",
  "path": "/users",
  "uri": "https://a1.easemob.com/easemob-demo/testapp/users",
  "entities": [
    {
      "uuid": "278b5e60-e27b-11e8-8f9b-d5d83ebec806",
      "type": "user",
      "created": 1541587920710,
      "modified": 1541587920710,
      "username": "user1",
      "activated": true,
      "nickname": "testuser1"
    },
    {
      "uuid": "278bac80-e27b-11e8-b192-73e4cd5078a5",
      "type": "user",
      "created": 1541587920712,
      "modified": 1541587920712,
      "username": "user2",
      "activated": true,
      "nickname": "testuser2"
    }
  ],
  "timestamp": 1541587920714,
  "data": [],
  "duration": 0,
  "organization": "1122161011178276",
  "applicationName": "testapp"
}
```

#### Request Example 2

``` php
curl -X POST -H "Authorization: Bearer YWMtP_8IisA-EeK-a5cNq4Jt3QAAAT7fI10IbPuKdRxUTjA9CNiZMnQIgk0LEUE" -i  "https://a1.easemob.com/easemob-demo/testapp/users" -d '[{"username":"user1", "password":"123","nickname":"testuser1"}, {"username":"user2", "password":"456","nickname":"testuser2"}, {"username":"user3", "password":"789","nickname":"testuser3"}]'
```

When there is a registered user3 in the request body, then the request will be successful and user1 and user2 will also be registered, and the data array in the response will return the user with the problem.

#### Examples of possible returned results

**returns a value of 200, indicating a successful token return**

``` json
{
  "action": "post",
  "application": "22bcffa0-8f86-11e6-9df8-516f6df68c6d",
  "path": "/users",
  "uri": "https://a1.easemob.com/easemob-demo/testapp/users",
  "entities": [
    {
      "uuid": "278b5e60-e27b-11e8-8f9b-d5d83ebec806",
      "type": "user",
      "created": 1541587920710,
      "modified": 1541587920710,
      "username": "user1",
      "activated": true,
      "nickname": "testuser1"
    },
    {
      "uuid": "278bac80-e27b-11e8-b192-73e4cd5078a5",
      "type": "user",
      "created": 1541587920712,
      "modified": 1541587920712,
      "username": "user2",
      "activated": true,
      "nickname": "testuser2"
    }
  ],
  "timestamp": 1541587920714,
  "data": [
        {
            "username": "user3",
            "registerUserFailReason": "the user3 already exists"
        }
    ],
  "duration": 0,
  "organization": "1122161011178276",
  "applicationName": "testapp"
}
```

If the returned result is <font color='red'> 429, 503 </font> or other \<wrapem>5xx\</wrap>, it may mean that the interface is flow restricted, please pause a little and retry. See [interface flow restriction description](/server_rest_interface_flow_limiting_instructions.html) for details

[Online testing using Easemob REST API](http://api-docs.easemob.com/)

------------------------------------------------------------------------

### Get a single user

Interface to get the details of a single IM user.

#### HTTP Request

<table border="1" cellspacing="0" bordercolor="#000000">
  <tr>
    <th>GET</th>
    <th>/{org_name}/{app_name}/users/{username}</th>
  </tr>
</table>

You need to fill in {username} corresponding to the IM username you need to get when requesting.

#### Request Headers

<table border="1" cellspacing="0" bordercolor="#000000">
  <tr>
    <th>Parameter</th>
    <th>Description</th>
  </tr>
  <tr>
    <td>Content-Type</td>
    <td>application/json</td>
  </tr>
  <tr>
    <td>Authorization</td>
    <td>Bearer \${token}</td>
  </tr>
</table>

#### Response Body

View the information contained in the entities field in the return value

<table border="1" cellspacing="0" bordercolor="#000000">
  <tr>
    <th>Parameter</th>
    <th>Description</th>
  </tr>
  <tr>
    <td>uuid</td>
    <td>User's UUID, identification field</td>
  </tr>
  <tr>
    <td>type</td>
    <td>"user" User Type</td>
  </tr>
  <tr>
    <td>username</td>
    <td>Username, also known as Easemob ID</td>
  </tr>
  <tr>
    <td>activated</td>
    <td>Whether the user is activated, "true" is activated, "false" is blocked, the block needs to be unblocked through the unblock interface in order to log in normally</td>
  </tr>
  <tr>
    <td>nickname</td>
    <td>Nickname (optional), the nickname that will be used in iOS Apns push (only the nickname displayed in the push notification bar), not the nickname of the user's personal information, Easemob does not save the user's nickname, avatar and other personal information, you need to save it on your own server and bind it to the IM username registered to your user</td>
  </tr>
  <tr>
    <td>notification_display_style</td>
    <td>Message alert mode, "0" notification only, "1" notification and message details, no set return will not return</td>
  </tr>
  <tr>
    <td>notification_no_disturbing</td>
    <td>Do not disturb setting. \ "false\" means no disturbance off, "true" no disturbance on, no setting back will not return</td>
  </tr>
  <tr>
    <td>notification_no_disturbing_start</td>
    <td>The start time of Do Not Disturb. The number represents the start time, e.g. "8" means no-disturbance will start at 8:00 every day, and will not return if not set</td>
  </tr>
  <tr>
    <td>notification_no_disturbing_end</td>
    <td>The end time of Do Not Disturb. The number represents the end time, e.g. "18" means that it will be turned off at 18:00 every day, and will not return if it is not set</td>
  </tr>
  <tr>
    <td>notification_ignore_63112447328257</td>
    <td>The setting to block/unblock the offline push of group messages. The number represents the group id, "true" is blocked, "false" is not blocked, no setting will be returned</td>
  </tr>
  <tr>
    <td>notifier_name</td>
    <td>The client pushes the name of the certificate and does not return it if it is not set</td>
  </tr>
  <tr>
    <td>device_token</td>
    <td>Push token, no token will be returned if there is none</td>
  </tr>
</table>

#### Request Example

``` php
curl -X GET -H 'Accept: application/json' -H 'Authorization: Bearer YWMte3bGuOukEeiTkNP4grL7iwAAAAAAAAAAAAAAAAAAAAGL4CTw6XgR6LaXXVmNX4QCAgMAAAFnKdc-ZgBPGgBFTrLhhyK8woMEI005emtrLJFJV6aoxsZSioSIZkr5kw' 'http://a1.easemob.com/easemob-demo/testapp/users/user1'
```

#### Examples of possible returned results

**The return value is 200, which means the user information was successfully geted.**

``` json
{
  "action": "get",
  "path": "/users",
  "uri": "http://a1.easemob.com/easemob-demo/testapp/users/user1",
  "entities": [
    {
      "uuid": "0ffe2d80-ed76-11e8-8d66-279e3e1c214b",
      "type": "user",
      "created": 1542795196504,
      "modified": 1542795196504,
      "username": "user1",
      "activated": true,
      "nickname": "testuser"
    }
  ],
  "timestamp": 1542798985011,
  "duration": 6,
  "count": 1
}
```

**returns a value of 404, indicating that the user does not exist**

``` json
{
  "error": "service_resource_not_found",
  "timestamp": 1542799099701,
  "duration": 0,
  "exception": "org.apache.usergrid.services.exceptions.ServiceResourceNotFoundException",
  "error_description": "Service resource not found"
}
```

**return value 401, unauthorized\[no token, token error, token expired\]**

``` json
{
  "error": "unauthorized",
  "timestamp": 1542799184277,
  "duration": 0,
  "exception": "org.apache.shiro.authz.UnauthorizedException",
  "error_description": "Subject does not have permission [applications:get:8be024f0-e978-11e8-b697-5d598d5f8402:/users/b2aade90-e978-11e8-a974-f3368f82e4f1]"
}
```

If the return result is <font color='red'> 429, 503 </font> or other <font color='red'> 5xx </font>, it may mean that the interface is restricted, please pause a little and retry. See [interface flow restriction description](/server_rest_interface_flow_limiting_instructions.html) for details

[Online testing using the Easemob REST API](http://api-docs.easemob.com/)

------------------------------------------------------------------------

### Bulk user acquisition

This interface returns by default sorted by creation time, if you need to specify the number of fetches, you need to add the parameter limit=N, N is the number value. About paging: If the number in the DB is greater than N, it returns JSON will carry a field "cursor", we call it "cursor", the cursor can be understood as a pointer to the result set, the value is variable. When fetching data down the page with the cursor, you can get the value of the next page. If there is a next page, the return value still has this field, until there is no field, it means that the last page has been reached. cursor's meaning is data (true) paging.

#### HTTP Request

<table border="1" cellspacing="0" bordercolor="#000000">
  <tr>
    <th>GET</th>
    <th>/{org_name}/{app_name}/users/{username}</th>
  </tr>
</table>

You need to fill in {username} corresponding to the IM username you need to get when requesting.

#### Request Headers

<table border="1" cellspacing="0" bordercolor="#000000">
  <tr>
    <th>Parameter</th>
    <th>Description</th>
  </tr>
  <tr>
    <td>Content-Type</td>
    <td>application/json</td>
  </tr>
  <tr>
    <td>Authorization</td>
    <td>Bearer \${token}</td>
  </tr>
</table>

#### Response Body

View the information contained in the entities field in the return value

<table border="1" cellspacing="0" bordercolor="#000000">
  <tr>
    <th>Parameter</th>
    <th>Description</th>
  </tr>
  <tr>
    <td>uuid</td>
    <td>User's UUID, identification field</td>
  </tr>
  <tr>
    <td>type</td>
    <td>"user" User Type</td>
  </tr>
  <tr>
    <td>username</td>
    <td>Username, also known as Easemob ID</td>
  </tr>
  <tr>
    <td>activated</td>
    <td>Whether the user is activated, "true" is activated, "false" is blocked, the block needs to be unblocked through the unblock interface in order to log in normally</td>
  </tr>
  <tr>
    <td>nickname</td>
    <td>Nickname (optional), the nickname that will be used in iOS Apns push (only the nickname displayed in the push notification bar), not the nickname of the user's personal information, Easemob does not save the user's nickname, avatar and other personal information, you need to save it on your own server and bind it to the IM username registered to your user</td>
  </tr>
  <tr>
    <td>notification_display_style</td>
    <td>Message alert mode, "0" notification only, "1" notification and message details, no set return will not return</td>
  </tr>
  <tr>
    <td>notification_no_disturbing</td>
    <td>Do not disturb setting. \ "false\" means no disturbance off, "true" no disturbance on, no setting back will not return</td>
  </tr>
  <tr>
    <td>notification_no_disturbing_start</td>
    <td>The start time of Do Not Disturb. The number represents the start time, e.g. "8" means no-disturbance will start at 8:00 every day, and will not return if not set</td>
  </tr>
  <tr>
    <td>notification_no_disturbing_end</td>
    <td>The end time of Do Not Disturb. The number represents the end time, e.g. "18" means that it will be turned off at 18:00 every day, and will not return if it is not set</td>
  </tr>
  <tr>
    <td>notification_ignore_63112447328257</td>
    <td>The setting to block/unblock the offline push of group messages. The number represents the group id, "true" is blocked, "false" is not blocked, no setting will be returned</td>
  </tr>
  <tr>
    <td>notifier_name</td>
    <td>The client pushes the name of the certificate and does not return it if it is not set</td>
  </tr>
  <tr>
    <td>device_token</td>
    <td>Push token, no token will be returned if there is none</td>
  </tr>
</table>

#### Request Example

``` php
curl -X GET -H 'Accept: application/json' -H 'Authorization: Bearer YWMt7CoyjusbEeixOi3iod4eDAAAAAAAAAAAAAAAAAAAAAGL4CTw6XgR6LaXXVmNX4QCAgMAAAFnJlhJIwBPGgCqtjiyVnR209iyr8kNbhJhhanNQDdP9CMmpK2G-NIUOQ' 'http://a1.easemob.com/easemob-demo/testapp/users?limit=2'
```

Use the returned cursor to get the next page

``` php
curl -X GET -H 'Accept: application/json' -H 'Authorization: Bearer YWMt7CoyjusbEeixOi3iod4eDAAAAAAAAAAAAAAAAAAAAAGL4CTw6XgR6LaXXVmNX4QCAgMAAAFnJlhJIwBPGgCqtjiyVnR209iyr8kNbhJhhanNQDdP9CMmpK2G-NIUOQ' 'http://a1.easemob.com/easemob-demo/testapp/users?limit=2&cursor=LTgzNDAxMjM3OToxTEFnNE9sNEVlaVQ0UEdhdmJNR2tB'
```

#### Examples of possible returned results

**returns 200, indicating success in getting user information**

``` json
{
  "action": "get",
  "params": {
    "limit": [
      "2"
    ]
  },
  "path": "/users",
  "uri": "http://a1.easemob.com/easemob-demo/testapp/users",
  "entities": [
    {
      "uuid": "ab90eff0-e978-11e8-9174-8f161649a182",
      "type": "user",
      "created": 1542356511855,
      "modified": 1542356511855,
      "username": "user1",
      "activated": true,
      "nickname": "user1"
    },
    {
      "uuid": "b2aade90-e978-11e8-a974-f3368f82e4f1",
      "type": "user",
      "created": 1542356523769,
      "modified": 1542356523769,
      "username": "user2",
      "activated": true,
      "nickname": "user2"
    }
  ],
  "timestamp": 1542558467056,
  "duration": 11,
  "cursor": "LTgzNDAxMjM3OToxTEFnNE9sNEVlaVQ0UEdhdmJNR2tB",
  "count": 2
}
```

**return value 401, means unauthorized\[no token, token error, token expired\]**

``` json
{
  "error": "unauthorized",
  "timestamp": 1542559172189,
  "duration": 0,
  "exception": "org.apache.shiro.authz.UnauthorizedException",
  "error_description": "Subject does not have permission [applications:get:8be024f0-e978-11e8-b697-5d598d5f8402:/users]"
}
```

If the return result is <font color='red'> 429, 503 </font> or other <font color='red'> 5xx </font>, it may mean that the interface is flow-limited, please pause slightly and retry. See [interface flow restriction description](/server_rest_interface_flow_limiting_instructions.html) for details

[Online testing using the Easemob REST API](http://api-docs.easemob.com/)

> Pagination

#### Request Example

``` php
curl -X GET -H 'Accept: application/json' -H 'Authorization: Bearer YWMt7CoyjusbEeixOi3iod4eDAAAAAAAAAAAAAAAAAAAAAGL4CTw6XgR6LaXXVmNX4QCAgMAAAFnJlhJIwBPGgCqtjiyVnR209iyr8kNbhJhhanNQDdP9CMmpK2G-NIUOQ' 'http://a1.easemob.com/easemob-demo/testapp/users?limit=2&cursor=LTgzNDAxMjM3OToxTEFnNE9sNEVlaVQ0UEdhdmJNR2tB'
```

#### Examples of possible returned results

**returns 200, indicating success in getting user information**

``` json
{
  "action": "get",
  "params": {
    "cursor": [
      "LTgzNDAxMjM3OToxTEFnNE9sNEVlaVQ0UEdhdmJNR2tB"
    ],
    "limit": [
      "2"
    ]
  },
  "path": "/users",
  "uri": "http://a1.easemob.com/easemob-demo/testapp/users",
  "entities": [
    {
      "uuid": "fef7f250-e983-11e8-ba39-0fed7dcc3cdd",
      "type": "user",
      "created": 1542361376245,
      "modified": 1542361376245,
      "username": "testuser",
      "activated": true,
      "nickname": "testuser"
    }
  ],
  "timestamp": 1542559337702,
  "duration": 2,
  "count": 1
}
```

**return value 401, means unauthorized\[no token, token error, token expired\]**

``` json
{
  "error": "unauthorized",
  "timestamp": 1542559373319,
  "duration": 0,
  "exception": "org.apache.shiro.authz.UnauthorizedException",
  "error_description": "Subject does not have permission [applications:get:8be024f0-e978-11e8-b697-5d598d5f8402:/users]"
}
```

If the return result is <font color='red'> 429, 503 </font> or other <font color='red'> 5xx </font>, it may mean that the interface is flow-limited, please pause slightly and retry. See [interface flow restriction description](/server_rest_interface_flow_limiting_instructions.html) for details

[Online testing using Easemob REST API](http://api-docs.easemob.com/)

------------------------------------------------------------------------

### Delete a single user

When deleting a user, if the user is the owner of a group or chat room, the system will delete both the group and chat room. Please check when you do this.

#### HTTP Request

<table border="1" cellspacing="0" bordercolor="#000000">
  <tr>
    <th>DELETE</th>
    <th>/{org_name}/{app_name}/users/{username}</th>
  </tr>
</table>

You need to fill in {username} corresponding to the IM username that needs to be deleted when requesting.

#### Request Headers

<table border="1" cellspacing="0" bordercolor="#000000">
  <tr>
    <th>Parameter</th>
    <th>Description</th>
  </tr>
  <tr>
    <td>Content-Type</td>
    <td>application/json</td>
  </tr>
  <tr>
    <td>Authorization</td>
    <td>Bearer \${token}</td>
  </tr>
</table>

#### Request Example

``` php
curl -X DELETE -H 'Accept: application/json' -H 'Authorization: Bearer YWMt7CoyjusbEeixOi3iod4eDAAAAAAAAAAAAAAAAAAAAAGL4CTw6XgR6LaXXVmNX4QCAgMAAAFnJlhJIwBPGgCqtjiyVnR209iyr8kNbhJhhanNQDdP9CMmpK2G-NIUOQ' 'http://a1.easemob.com/easemob-demo/testapp/users/user1'
```

#### Examples of possible returned results

**returns 200, indicating that the user deleted successfully**

``` json
{
  "action": "delete",
  "application": "8be024f0-e978-11e8-b697-5d598d5f8402",
  "path": "/users",
  "uri": "https://a1.easemob.com/easemob-demo/testapp/users",
  "entities": [
    {
      "uuid": "ab90eff0-e978-11e8-9174-8f161649a182",
      "type": "user",
      "created": 1542356511855,
      "modified": 1542356511855,
      "username": "user1",
      "activated": true,
      "nickname": "user1"
    }
  ],
  "timestamp": 1542559539776,
  "duration": 39,
  "organization": "easemob-demo",
  "applicationName": "testapp"
}
```

**returns a value of 404, indicating that the user does not exist**

``` json
{
  "error": "service_resource_not_found",
  "timestamp": 1542559595134,
  "duration": 0,
  "exception": "org.apache.usergrid.services.exceptions.ServiceResourceNotFoundException",
  "error_description": "Service resource not found"
}
```

**return value 401, means unauthorized\[no token, token error, token expired\]**

``` json
{
  "error": "service_resource_not_found",
  "timestamp": 1542559595134,
  "duration": 0,
  "exception": "org.apache.usergrid.services.exceptions.ServiceResourceNotFoundException",
  "error_description": "Service resource not found"
}
```

If the return result is <font color='red'> 429, 503 </font> or other <font color='red'> 5xx </font>, it may mean that the interface is flow-limited, please pause slightly and retry. See [interface flow restriction description](/server_rest_interface_flow_limiting_instructions.html) for details

[Online testing using Easemob REST API](http://api-docs.easemob.com/)

------------------------------------------------------------------------

### Bulk delete users

Delete the specified number of Easemob accounts under an APP. You can delete N users at a time, and the value can be modified. It is recommended that this value is between 100-500 and not too large. It should be noted that here is just a batch to delete the N users, which ones are not specified, you can see which users are deleted in the return value.

#### HTTP Request

<table border="1" cellspacing="0" bordercolor="#000000">
  <tr>
    <th>DELETE</th>
    <th>/{org_name}/{app_name}/users/{username}</th>
  </tr>
</table>

#### Request Headers

<table border="1" cellspacing="0" bordercolor="#000000">
  <tr>
    <th>Parameter</th>
    <th>Description</th>
  </tr>
  <tr>
    <td>Content-Type</td>
    <td>application/json</td>
  </tr>
  <tr>
    <td>Authorization</td>
    <td>Bearer \${token}</td>
  </tr>
</table>

#### Request Example

``` php
curl -X DELETE -H 'Accept: application/json' -H 'Authorization: Bearer YWMte3bGuOukEeiTkNP4grL7iwAAAAAAAAAAAAAAAAAAAAGL4CTw6XgR6LaXXVmNX4QCAgMAAAFnKdc-ZgBPGgBFTrLhhyK8woMEI005emtrLJFJV6aoxsZSioSIZkr5kw' 'http://a1.easemob.com/easemob-demo/testapp/users?limit=2'
```

#### Examples of possible returned results

**returns 200, indicating that the user deleted successfully**

``` json
{
  "action": "delete",
  "application": "8be024f0-e978-11e8-b697-5d598d5f8402",
  "params": {
    "limit": [
      "2"
    ]
  },
  "path": "/users",
  "uri": "https://a1.easemob.com/easemob-demo/testapp/users",
  "entities": [
    {
      "uuid": "b2aade90-e978-11e8-a974-f3368f82e4f1",
      "type": "user",
      "created": 1542356523769,
      "modified": 1542597334500,
      "username": "user2",
      "activated": true,
      "nickname": "testuser"
    },
    {
      "uuid": "b98ad170-e978-11e8-8fce-7f76daa76557",
      "type": "user",
      "created": 1542356535303,
      "modified": 1542356535303,
      "username": "user3",
      "activated": true,
      "nickname": "user3"
    }
  ],
  "timestamp": 1542867197779,
  "duration": 504,
  "organization": "easemob-demo",
  "applicationName": "testapp",
  "cursor": "LTgzNDAxMjM3OTpfdmZ5VU9tREVlaTZPUV90ZmN3ODNR"
}
```

**return value 401, means unauthorized\[no token, token error, token expired\]**

``` json
{
  "error": "unauthorized",
  "timestamp": 1542867266449,
  "duration": 0,
  "exception": "org.apache.shiro.authz.UnauthorizedException",
  "error_description": "Subject does not have permission [applications:delete:8be024f0-e978-11e8-b697-5d598d5f8402:/users]"
}
```

If the return result is <font color='red'> 429, 503 </font> or other <font color='red'> 5xx </font>, it may mean that the interface is flow-limited, please pause slightly and retry. See [interface flow restriction description](/server_rest_interface_flow_limiting_instructions.html) for details

[Online testing using Easemob REST API](http://api-docs.easemob.com/)

------------------------------------------------------------------------

### Modify user password

You can change the login password of IM users through the server-side interface, without providing the original password.

#### HTTP Request

<table border="1" cellspacing="0" bordercolor="#000000">
  <tr>
    <th>PUT</th>
    <th>/{org_name}/{app_name}/users/{username}/password</th>
  </tr>
</table>

You need to fill in the request with {username}, the IM username that needs to change the password.

#### Request Headers

<table border="1" cellspacing="0" bordercolor="#000000">
  <tr>
    <th>Parameter</th>
    <th>Description</th>
  </tr>
  <tr>
    <td>Content-Type</td>
    <td>application/json</td>
  </tr>
  <tr>
    <td>Authorization</td>
    <td>Bearer \${token}</td>
  </tr>
</table>

#### Request Body

<table border="1" cellspacing="0" bordercolor="#000000">
  <tr>
    <th>Parameter</th>
    <th>Description</th>
  </tr>
  <tr>
    <td>newpassword</td>
    <td>${String specified by new password}</td>
  </tr>
</table>

#### Request Example

``` php
curl -X PUT -H 'Content-Type: application/json' -H 'Accept: application/json' -H 'Authorization: Bearer YWMte3bGuOukEeiTkNP4grL7iwAAAAAAAAAAAAAAAAAAAAGL4CTw6XgR6LaXXVmNX4QCAgMAAAFnKdc-ZgBPGgBFTrLhhyK8woMEI005emtrLJFJV6aoxsZSioSIZkr5kw' -d '{  
   "newpassword": "123"  
 }' 'http://a1.easemob.com/easemob-demo/testapp/users/user1/password'
```

#### Examples of possible returned results

**returns 200, indicating that the user's password was changed successfully**

``` json
{
  "action": "set user password",
  "timestamp": 1542595598924,
  "duration": 8
}
```

**returns a value of 404, indicating that the user does not exist**

``` json
{
  "error": "entity_not_found",
  "timestamp": 1542595648340,
  "duration": 0,
  "exception": "org.apache.usergrid.persistence.exceptions.EntityNotFoundException",
  "error_description": "User null not found"
}
```

If the return result is <font color='red'> 429, 503 </font> or other <font color='red'> 5xx </font>, it may mean that the interface is flow-limited, please pause slightly and retry. See [interface flow restriction description](/server_rest_interface_flow_limiting_instructions.html) for details

[Online testing using the Easemob REST API](http://api-docs.easemob.com/)

------------------------------------------------------------------------

### Set Push Nickname

Set the user's push nickname to be used when pushing offline.

#### HTTP Request

<table border="1" cellspacing="0" bordercolor="#000000">
  <tr>
    <th>PUT</th>
    <th>/{org_name}/{app_name}/users/{username}</th>
  </tr>
</table>

You need to fill in {username} corresponding to the request, and you need to modify the IM username of the push nickname.

#### Request Headers

<table border="1" cellspacing="0" bordercolor="#000000">
  <tr>
    <th>Parameter</th>
    <th>Description</th>
  </tr>
  <tr>
    <td>Content-Type</td>
    <td>application/json</td>
  </tr>
  <tr>
    <td>Authorization</td>
    <td>Bearer \${token}</td>
  </tr>
</table>

#### Request Body

<table border="1" cellspacing="0" bordercolor="#000000">
  <tr>
    <th>Parameter</th>
    <th>Description</th>
  </tr>
  <tr>
    <td>nickname</td>
    <td>Information about the push nickname to be changed to</td>
  </tr>
</table>

#### Response Body

View the information contained in the entities field in the return value

<table border="1" cellspacing="0" bordercolor="#000000">
  <tr>
    <th>Parameter</th>
    <th>Description</th>
  </tr>
  <tr>
    <td>uuid</td>
    <td>User's UUID, identification field</td>
  </tr>
  <tr>
    <td>type</td>
    <td>"user" user type</td>
  </tr>
  <tr>
    <td>username</td>
    <td>Username, also known as Easemob ID</td>
  </tr>
  <tr>
    <td>nickname</td>
    <td>Nickname (optional), the nickname that will be used in iOS Apns push (only the nickname displayed in the push notification bar), not the nickname of the user's personal information, Easemob does not save the user's nickname, avatar and other personal information, you need to save it on your own server and bind it to the IM username registered to your user</td>
  </tr>
  <tr>
    <td>activated</td>
    <td>Whether the user is activated, "true" is activated, "false" is blocked, the block needs to be unblocked through the unblock interface in order to log in normally</td>
  </tr>
</table>

#### Request Example

``` php
curl -X PUT -H 'Content-Type: application/json' -H 'Accept: application/json' -H 'Authorization: Bearer YWMte3bGuOukEeiTkNP4grL7iwAAAAAAAAAAAAAAAAAAAAGL4CTw6XgR6LaXXVmNX4QCAgMAAAFnKdc-ZgBPGgBFTrLhhyK8woMEI005emtrLJFJV6aoxsZSioSIZkr5kw' -d '{ 
   "nickname": "testuser"  
 }' 'http://a1.easemob.com/easemob-demo/testapp/users/user1'
```

#### Examples of possible returned results

**return value 200, indicating that the user pushed the nickname change successfully**

``` json
{
  "action": "put",
  "application": "8be024f0-e978-11e8-b697-5d598d5f8402",
  "path": "/users",
  "uri": "https://a1.easemob.com/easemob-demo/testapp/users",
  "entities": [
    {
      "uuid": "4759aa70-eba5-11e8-925f-6fa0510823ba",
      "type": "user",
      "created": 1542595573399,
      "modified": 1542596083687,
      "username": "user1",
      "activated": true,
      "nickname": "testuser"
    }
  ],
  "timestamp": 1542596083685,
  "duration": 6,
  "organization": "easemob-demo",
  "applicationName": "testapp"
}
```

**returns a value of 400, indicating that the user does not exist**

``` json
{
  "error": "required_property_not_found",
  "timestamp": 1542597341919,
  "duration": 0,
  "exception": "org.apache.usergrid.persistence.exceptions.RequiredPropertyNotFoundException",
  "error_description": "Entity user requires a property named username"
}
```

**return value 401, means unauthorized\[no token, token error, token expired\]**

``` json
{
  "error": "unauthorized",
  "timestamp": 1542597533232,
  "duration": 0,
  "exception": "org.apache.shiro.authz.UnauthorizedException",
  "error_description": "Subject does not have permission [applications:put:8be024f0-e978-11e8-b697-5d598d5f8402:/users]"
}
```

If the return result is <font color='red'> 429, 503 </font> or other <font color='red'> 5xx </font>, it may mean that the interface is flow-limited, please pause slightly and retry. See [interface flow restriction description](/server_rest_interface_flow_limiting_instructions.html) for details

[Online testing using Easemob REST API](http://api-docs.easemob.com/)

------------------------------------------------------------------------

### Set how to display push messages

Set the way of pushing messages to the client, which is effective in time after modification. The server side corresponds to different settings and sends messages to users with different display methods.

#### HTTP Request

<table border="1" cellspacing="0" bordercolor="#000000">
  <tr>
    <th>PUT</th>
    <th>/{org_name}/{app_name}/users/{username}</th>
  </tr>
</table>

You need to fill in {username} corresponding to the request, the IM username that needs to be pushed.

#### Request Headers

<table border="1" cellspacing="0" bordercolor="#000000">
  <tr>
    <th>Parameter</th>
    <th>Description</th>
  </tr>
  <tr>
    <td>Content-Type</td>
    <td>application/json</td>
  </tr>
  <tr>
    <td>Authorization</td>
    <td>Bearer \${token}</td>
  </tr>
</table>

#### Request Body

<table border="1" cellspacing="0" bordercolor="#000000">
  <tr>
    <th>Parameter</th>
    <th>Description</th>
  </tr>
  <tr>
    <td>notification_display_style</td>
    <td>"0" for notification only, "1" for notification and message details</td>
  </tr>
</table>

#### Response Body

View the information contained in the entities field in the return value

<table border="1" cellspacing="0" bordercolor="#000000">
  <tr>
    <th>Parameter</th>
    <th>Description</th>
  </tr>
  <tr>
    <td>uuid</td>
    <td>User's UUID, identification field</td>
  </tr>
  <tr>
    <td>type</td>
    <td>"user" User Type</td>
  </tr>
  <tr>
    <td>username</td>
    <td>Username, also known as Easemob ID</td>
  </tr>
  <tr>
    <td>activated</td>
    <td>Whether the user is activated, "true" is activated, "false" is blocked, the block needs to be unblocked through the unblock interface in order to log in normally
  notification_display_style   Message alert method, "0" notification only, "1" notification and message details</td>
  </tr>
</table>

#### Request Example

``` php
curl -X PUT -H "Authorization: Bearer YWMtSozP9jHNEeSQegV9EKeAQAAAUlmBR2bTGr-GP2xNh8GhUCdKViBFDSEF2E" -i  https://a1.easemob.com/easemob-demo/testapp/users/a -d '{"notification_display_style": "1"}'
```

#### Examples of possible returned results

**return value 200, indicating successful modification**

``` json
{
  "action" : "put",
  "application" : "17d59e50-0aee-11e8-8092-0dc80c0f5e99",
  "path" : "/users",
  "uri" : "https://a1.easemob.com/easemob-demo/testapp/users",
  "entities" : [ {
    "uuid" : "3b8c9890-7b9a-11e8-9d88-f50bf55cafad",
    "type" : "user",
    "created" : 1530276298905,
    "modified" : 1534407146060,
    "username" : "user1",
    "activated" : true,
    "notification_no_disturbing" : false,
    "notification_no_disturbing_start" : 1,
    "notification_no_disturbing_end" : 3,
    "notification_display_style" : 1,
    "nickname" : "testuser",
    "notifier_name" : "2882303761517426801"
  } ],
  "timestamp" : 1534407146058,
  "duration" : 3,
  "organization" : "1112171214115068",
  "applicationName" : "testapp"
}
```

If the return result is <font color='red'> 429, 503 </font> or other <font color='red'> 5xx </font>, it may mean that the interface is flow-limited, please pause slightly and retry. See [interface flow restriction description](/server_rest_interface_flow_limiting_instructions.html) for details
[Online testing using Easemob REST API](http://api-docs.easemob.com/)

------------------------------------------------------------------------

### Set no disturbance

Set IM users to be free from interruptions, during which they will not receive offline message pushes.

#### HTTP Request

<table border="1" cellspacing="0" bordercolor="#000000">
  <tr>
    <th>PUT</th>
    <th>/{org_name}/{app_name}/users/{username}</th>
  </tr>
</table>

You need to fill in {username} when requesting, and you need to set the IM user name for no disturbance

#### Request Headers

<table border="1" cellspacing="0" bordercolor="#000000">
  <tr>
    <th>Parameter</th>
    <th>Description</th>
  </tr>
  <tr>
    <td>Content-Type</td>
    <td>application/json</td>
  </tr>
  <tr>
    <td>Authorization</td>
    <td>Bearer \${token}</td>
  </tr>
</table>

#### Request Body

<table border="1" cellspacing="0" bordercolor="#000000">
  <tr>
    <th>Parameter</th>
    <th>Description</th>
  </tr>
  <tr>
    <td>notification_no_disturbing</td>
    <td>Whether to be free from disturbance, \"0\" means free from disturbance off, "1" free from disturbance on</td>
  </tr>
  <tr>
    <td>notification_no_disturbing_start</td>
    <td>No-disturb start time in hours</td>
  </tr>
  <tr>
    <td>notification_no_disturbing_end</td>
    <td>No-disturb end time in hours</td>
  </tr>
</table>

#### Response Body

View the information contained in the entities field in the return value

<table border="1" cellspacing="0" bordercolor="#000000">
  <tr>
    <th>Parameter</th>
    <th>Description</th>
  </tr>
  <tr>
    <td>uuid</td>
    <td>User's UUID, identification field</td>
  </tr>
  <tr>
    <td>type</td>
    <td>"user" User Type</td>
  </tr>
  <tr>
    <td>username</td>
    <td>Username, also known as Easemob ID</td>
  </tr>
  <tr>
    <td>activated</td>
    <td>Whether the user is activated, "true" is activated, "false" is blocked, the block needs to be unblocked through the unblock interface in order to log in normally
  notification_display_style   Message alert method, "0" notification only, "1" notification and message details</td>
  </tr>
  <tr>
    <th>notification_display_style</th>
    <th>Do not disturb setting. \"0\" means do not disturb off, "1" do not disturb on</th>
  </tr>
  <tr>
    <th>notification_no_disturbing_start</th>
    <th>The start time of Do Not Disturb. The number represents the start time, e.g. "8" means no disturbance starts at 8:00 every day</th>
  </tr>
  <tr>
    <th>notification_no_disturbing_end</th>
    <th>The end time of do-not-disturb. The number represents the end time, e.g. "18" means that no-disturb is turned off at 18:00 daily</th>
  </tr>
</table>

#### Request Example

**Setting no-disturb time**

``` php
curl -X PUT -H "Authorization: Bearer YWMtSozP9jHNEeSQegV9EKeAQAAAUlmBR2bTGr-GP2xNh8GhUCdKViBFDSEF2E" -i  "https://a1.easemob.com/easemob-demo/testapp/users/a " -d '{"notification_no_disturbing": true,"notification_no_disturbing_start": "1","notification_no_disturbing_end": "3"}'
```

**Cancel No Disturb**

``` php
curl -X PUT -H "Authorization: Bearer YWMtSozP9jHNEeSQegV9EKeAQAAAUlmBR2bTGr-GP2xNh8GhUCdKViBFDSEF2E" -i  "https://a1.easemob.com/easemob-demo/testapp/users/a " -d '{"notification_no_disturbing": false}'
```

#### Examples of possible returned results

**Returns 200, indicating successful setup**

``` json
{
  "action" : "put",
  "application" : "17d59e50-0aee-11e8-8092-0dc80c0f5e99",
  "path" : "/users",
  "uri" : "https://a1.easemob.com/easemob-demo/testapp/users",
  "entities" : [ {
    "uuid" : "3b8c9890-7b9a-11e8-9d88-f50bf55cafad",
    "type" : "user",
    "created" : 1530276298905,
    "modified" : 1534405429835,
    "username" : "User1",
    "activated" : true,
    "notification_no_disturbing" : true,
    "notification_no_disturbing_start" : 1,
    "notification_no_disturbing_end" : 3,
    "notification_display_style" : 0,
    "nickname" : "testuser",
    "notifier_name" : "2882303761517426801"
  } ],
  "timestamp" : 1534405429833,
  "duration" : 4,
  "organization" : "1112171214115068",
  "applicationName" : "testapp"
}
```

If the return result is <font color='red'> 429, 503 </font> or other <font color='red'> 5xx </font>, it may mean that the interface is flow-limited, please pause slightly and retry. See [interface flow restriction description](/server_rest_interface_flow_limiting_instructions.html) for details

[Online testing using Easemob REST API](http://api-docs.easemob.com/)

------------------------------------------------------------------------

## Friends and Blacklist

Easemob provides several interfaces for adding and removing friends and blacklists of IM users.

<table border="1" cellspacing="0" bordercolor="#000000">
  <tr>
    <th>Name</th>
    <th>Request</th>
    <th>Description</th>
  </tr>
  <tr>
    <td>Add Friends</td>
    <td>/{org_name}/{app_name}/users/{owner_username}/contacts/users/{friend_username}</td>
    <th>Add as friend</th>
  </tr>
  <tr>
    <td>Remove Friends</td>
    <td>/{org_name}/{app_name}/users/{owner_username}/contacts/users/{friend_username}   Remove users from the friends list</td>
    <th>Remove users from the friends list</th>
  </tr>
  <tr>
    <td>Get a list of friends</td>
    <td>/{org_name}/{app_name}/users/{owner_username}/contacts/users</td>
    <th>Get a list of friends and information</th>
  </tr>
  <tr>
    <th>Get Blacklist</th>
    <th>/{org_name}/{app_name}/users/{owner_username}/blocks/users</th>
    <th>Get blacklist and information</th>
  </tr>
  <tr>
    <th>Add blacklist</th>
    <th>/{org_name}/{app_name}/users/{owner_username}/blocks/users</th>
    <th>Hacking users, adding to blacklist</th>
  </tr>
  <tr>
    <th>Remove Blacklist</th>
    <th>/{org_name}/{app_name}/users/{owner_username}/blocks/users/{blocked_username}</th>
    <th>Excluding blacklisted users</th>
  </tr>
</table>

### Add Friends

To add a friend, the friend must be an IM user who is under the same APPkey as you.

The maximum number of friends per user under the community version appkey is 1000, and the maximum number of friends varies from version to version appkey, please refer to: [Function Introduction](https://www.easemob.com/pricing/im)

#### HTTP Request

<table border="1" cellspacing="0" bordercolor="#000000">
  <tr>
    <th>POST</th>
    <th>/{org_name}/{app_name}/users/{owner_username}/contacts/users/{friend_username}</th>
  </tr>
</table>

You need to fill in the request with {owner_username}, the username of the friend you want to add, and {friend_username}, the friend's username.

#### Request Headers

<table border="1" cellspacing="0" bordercolor="#000000">
  <tr>
    <th>Parameter</th>
    <th>Description</th>
  </tr>
  <tr>
    <td>Content-Type</td>
    <td>application/json</td>
  </tr>
  <tr>
    <td>Authorization</td>
    <td>Bearer \${token}</td>
  </tr>
</table>

#### Request Example

``` php
curl -X POST -H 'Content-Type: application/json' -H 'Accept: application/json' -H 'Authorization: Bearer YWMte3bGuOukEeiTkNP4grL7iwAAAAAAAAAAAAAAAAAAAAGL4CTw6XgR6LaXXVmNX4QCAgMAAAFnKdc-ZgBPGgBFTrLhhyK8woMEI005emtrLJFJV6aoxsZSioSIZkr5kw' 'http://a1.easemob.com/easemob-demo/testapp/users/user1/contacts/users/user2'
```

#### Examples of possible returned results

**return value 200, means add friend successfully**

``` json
{
  "action": "post",
  "application": "8be024f0-e978-11e8-b697-5d598d5f8402",
  "path": "/users/4759aa70-eba5-11e8-925f-6fa0510823ba/contacts",
  "uri": "https://a1.easemob.com/easemob-demo/testapp/users/4759aa70-eba5-11e8-925f-6fa0510823ba/contacts",
  "entities": [
    {
      "uuid": "b2aade90-e978-11e8-a974-f3368f82e4f1",
      "type": "user",
      "created": 1542356523769,
      "modified": 1542597334500,
      "username": "user2",
      "activated": true,
      "nickname": "testuser"
    }
  ],
  "timestamp": 1542598913819,
  "duration": 63,
  "organization": "easemob-demo",
  "applicationName": "testapp"
}
```

**returns 404, indicating that the IM user or the friend being added does not exist**

``` json
{
  "error": "service_resource_not_found",
  "timestamp": 1542598965722,
  "duration": 0,
  "exception": "org.apache.usergrid.services.exceptions.ServiceResourceNotFoundException",
  "error_description": "Service resource not found"
}
```

**return value 401, unauthorized\[no token, token error, token expired\]**

``` json
{
  "error": "auth_bad_access_token",
  "timestamp": 1542599042628,
  "duration": 0,
  "exception": "org.apache.usergrid.rest.exceptions.SecurityException",
  "error_description": "Unable to authenticate due to corrupt access token"
}
```

If the return result is <font color='red'> 429, 503 </font> or other <font color='red'> 5xx </font>, it may mean that the interface is flow-limited, please pause slightly and retry. See [interface flow restriction description](/server_rest_interface_flow_limiting_instructions.html) for details

[Online testing using the Easemob REST API](http://api-docs.easemob.com/)

------------------------------------------------------------------------

### Remove Friends

Removes an IM user from the IM user's buddy list.

#### HTTP Request

<table border="1" cellspacing="0" bordercolor="#000000">
  <tr>
    <th>DELETE</th>
    <th>/{org_name}/{app_name}/users/{owner_username}/contacts/users/{friend_username}</th>
  </tr>
</table>

You need to fill in the request with {owner_username}, the username of the friend to be deleted, and {friend_username}, the username of the friend to be deleted.

#### Request Headers

<table border="1" cellspacing="0" bordercolor="#000000">
  <tr>
    <th>Parameter</th>
    <th>Description</th>
  </tr>
  <tr>
    <td>Content-Type</td>
    <td>application/json</td>
  </tr>
  <tr>
    <td>Authorization</td>
    <td>Bearer \${token}</td>
  </tr>
</table>

#### Request Example

``` php
curl -X DELETE -H 'Accept: application/json' -H 'Authorization: Bearer YWMte3bGuOukEeiTkNP4grL7iwAAAAAAAAAAAAAAAAAAAAGL4CTw6XgR6LaXXVmNX4QCAgMAAAFnKdc-ZgBPGgBFTrLhhyK8woMEI005emtrLJFJV6aoxsZSioSIZkr5kw' 'http://a1.easemob.com/easemob-demo/testapp/users/user1/contacts/users/user2'
```

#### Examples of possible returned results

**Return value 200, means the friend deletion is successful**

``` json
{
  "action": "delete",
  "application": "8be024f0-e978-11e8-b697-5d598d5f8402",
  "path": "/users/4759aa70-eba5-11e8-925f-6fa0510823ba/contacts",
  "uri": "https://a1.easemob.com/easemob-demo/testapp/users/4759aa70-eba5-11e8-925f-6fa0510823ba/contacts",
  "entities": [
    {
      "uuid": "b2aade90-e978-11e8-a974-f3368f82e4f1",
      "type": "user",
      "created": 1542356523769,
      "modified": 1542597334500,
      "username": "user2",
      "activated": true,
      "nickname": "testuser"
    }
  ],
  "timestamp": 1542599266616,
  "duration": 350,
  "organization": "easemob-demo",
  "applicationName": "testapp"
}
```

**returns a value of 404, indicating that this IM user or deleted friend does not exist**

``` json
{
  "error": "service_resource_not_found",
  "timestamp": 1542599413796,
  "duration": 0,
  "exception": "org.apache.usergrid.services.exceptions.ServiceResourceNotFoundException",
  "error_description": "Service resource not found"
}
```

**return value 401, means unauthorized\[no token, token error, token expired\]**

``` json
{
  "error": "unauthorized",
  "timestamp": 1542599447198,
  "duration": 0,
  "exception": "org.apache.shiro.authz.UnauthorizedException",
  "error_description": "Subject does not have permission [applications:delete:8be024f0-e978-11e8-b697-5d598d5f8402:/users/4759aa70-eba5-11e8-925f-6fa0510823ba]"
}
```

If the return result is <font color='red'> 429, 503 </font> or other <font color='red'> 5xx </font>, it may mean that the interface is flow-limited, please pause slightly and retry. See [interface flow restriction description](/server_rest_interface_flow_limiting_instructions.html) for details

[Online testing using the Easemob REST API](http://api-docs.easemob.com/)

------------------------------------------------------------------------

### Get a list of friends

Get the list of IM user's friends.

#### HTTP Request

<table border="1" cellspacing="0" bordercolor="#000000">
  <tr>
    <th>GET</th>
    <th>/{org_name}/{app_name}/users/{owner_username}/contacts/users</th>
  </tr>
</table>

You need to fill in {owner_username} in the request to get the user name of the friends list

#### Request Headers

<table border="1" cellspacing="0" bordercolor="#000000">
  <tr>
    <th>Parameter</th>
    <th>Description</th>
  </tr>
  <tr>
    <td>Content-Type</td>
    <td>application/json</td>
  </tr>
  <tr>
    <td>Authorization</td>
    <td>Bearer \${token}</td>
  </tr>
</table>

#### Response Body

<table border="1" cellspacing="0" bordercolor="#000000">
  <tr>
    <th>Parameter</th>
    <th>Description</th>
  </tr>
  <tr>
    <td>username</td>
    <td>"user1", "user2"，get the list of friends</td>
  </tr>
</table>

#### Request Example

``` php
curl -X GET -H 'Accept: application/json' -H 'Authorization: Bearer YWMte3bGuOukEeiTkNP4grL7iwAAAAAAAAAAAAAAAAAAAAGL4CTw6XgR6LaXXVmNX4QCAgMAAAFnKdc-ZgBPGgBFTrLhhyK8woMEI005emtrLJFJV6aoxsZSioSIZkr5kw' 'http://a1.easemob.com/easemob-demo/testapp/users/user1/contacts/users'
```

#### Examples of possible returned results

**Returns 200, indicating the success of viewing friends**

``` json
{
  "action": "get",
  "uri": "http://a1.easemob.com/easemob-demo/testapp/users/user1/contacts/users",
  "entities": [],
  "data": [
    "user3",
    "user2"
  ],
  "timestamp": 1543819826513,
  "duration": 12,
  "count": 2
}
```

**returns a value of 404, indicating that this IM user does not exist**

``` json
{
  "error": "service_resource_not_found",
  "timestamp": 1542599741965,
  "duration": 0,
  "exception": "org.apache.usergrid.services.exceptions.ServiceResourceNotFoundException",
  "error_description": "Service resource not found"
}
```

**return value 401, means unauthorized\[no token, token error, token expired\]**

``` json
{
  "error": "unauthorized",
  "timestamp": 1542599778401,
  "duration": 0,
  "exception": "org.apache.shiro.authz.UnauthorizedException",
  "error_description": "Subject does not have permission [applications:get:8be024f0-e978-11e8-b697-5d598d5f8402:/users/4759aa70-eba5-11e8-925f-6fa0510823ba]"
}
```

If the return result is <font color='red'> 429, 503 </font> or other <font color='red'> 5xx </font>, it may mean that the interface is flow-limited, please pause slightly and retry. See [interface flow restriction description](/server_rest_interface_flow_limiting_instructions.html) for details

[Online testing using Easemob REST API](http://api-docs.easemob.com/)

------------------------------------------------------------------------

### Get Blacklist

Get the blacklist of IM users.

#### HTTP Request

<table border="1" cellspacing="0" bordercolor="#000000">
  <tr>
    <th>GET</th>
    <th>/{org_name}/{app_name}/users/{owner_username}/blocks/users</th>
  </tr>
</table>

You need to fill in the corresponding {owner_username} when requesting to get the blacklisted username

#### Request Headers

<table border="1" cellspacing="0" bordercolor="#000000">
  <tr>
    <th>Parameter</th>
    <th>Description</th>
  </tr>
  <tr>
    <td>Content-Type</td>
    <td>application/json</td>
  </tr>
  <tr>
    <td>Authorization</td>
    <td>Bearer \${token}</td>
  </tr>
</table>

#### Response Body

<table border="1" cellspacing="0" bordercolor="#000000">
  <tr>
    <th>Parameter</th>
    <th>Description</th>
  </tr>
  <tr>
    <td>username</td>
    <td>"user1", "user2"，get the blacklist of friends</td>
  </tr>
</table>

#### Request Example

``` php
curl -X GET -H 'Accept: application/json' -H 'Authorization: Bearer YWMte3bGuOukEeiTkNP4grL7iwAAAAAAAAAAAAAAAAAAAAGL4CTw6XgR6LaXXVmNX4QCAgMAAAFnKdc-ZgBPGgBFTrLhhyK8woMEI005emtrLJFJV6aoxsZSioSIZkr5kw' 'http://a1.easemob.com/easemob-demo/testapp/users/user1/blocks/users'
```

#### Examples of possible returned results

**Return value 200, means view user blacklist successfully**

``` json
{
  "action": "get",
  "uri": "http://a1.easemob.com/easemob-demo/testapp/users/user1/blocks/users",
  "entities": [],
  "data": [
    "user2"
  ],
  "timestamp": 1542599978751,
  "duration": 4,
  "count": 1
}
```

**returns a value of 404, indicating that this IM user does not exist**

``` json
{
  "error": "service_resource_not_found",
  "timestamp": 1542600037889,
  "duration": 0,
  "exception": "org.apache.usergrid.services.exceptions.ServiceResourceNotFoundException",
  "error_description": "Service resource not found"
}
```

**return value 401, means unauthorized\[no token, token error, token expired\]**

``` json
{
  "error": "unauthorized",
  "timestamp": 1542600068826,
  "duration": 0,
  "exception": "org.apache.shiro.authz.UnauthorizedException",
  "error_description": "Subject does not have permission [applications:get:8be024f0-e978-11e8-b697-5d598d5f8402:/users/4759aa70-eba5-11e8-925f-6fa0510823ba]"
}
```

If the return result is <font color='red'> 429, 503 </font> or other <font color='red'> 5xx </font>, it may mean that the interface is flow-limited, please pause slightly and retry. See [interface flow restriction description](/server_rest_interface_flow_limiting_instructions.html) for details

[Online testing using Easemob REST API](http://api-docs.easemob.com/)

------------------------------------------------------------------------

### Add blacklist

Add one or more users to the blacklist list of IM users, and the users in the blacklist cannot send messages to this IM user. The maximum number of blacklisted users is 500 per user.

#### HTTP Request

<table border="1" cellspacing="0" bordercolor="#000000">
  <tr>
    <th>POST</th>
    <th>/{org_name}/{app_name}/users/{owner_username}/blocks/users</th>
  </tr>
</table>

You need to fill in the request with {owner_username}, the username to be added to the blacklist

#### Request Headers

<table border="1" cellspacing="0" bordercolor="#000000">
  <tr>
    <th>Parameter</th>
    <th>Description</th>
  </tr>
  <tr>
    <td>Content-Type</td>
    <td>application/json</td>
  </tr>
  <tr>
    <td>Authorization</td>
    <td>Bearer \${token}</td>
  </tr>
</table>

#### Request Body

<table border="1" cellspacing="0" bordercolor="#000000">
  <tr>
    <th>Parameter</th>
    <th>Description</th>
  </tr>
  <tr>
    <td>usernames</td>
    <td>"user1", "user2"，The user names to be added to the blacklist are submitted as an array</td>
  </tr>
</table>

#### Response Body

View the information contained in the data field in the return value

<table border="1" cellspacing="0" bordercolor="#000000">
  <tr>
    <th>Parameter</th>
    <th>Description</th>
  </tr>
  <tr>
    <td>usernames</td>
    <td>The user names that have been added to the inner list are displayed here</td>
  </tr>
</table>

#### Request Example

``` php
curl -X POST -H 'Content-Type: application/json' -H 'Accept: application/json' -H 'Authorization: Bearer YWMte3bGuOukEeiTkNP4grL7iwAAAAAAAAAAAAAAAAAAAAGL4CTw6XgR6LaXXVmNX4QCAgMAAAFnKdc-ZgBPGgBFTrLhhyK8woMEI005emtrLJFJV6aoxsZSioSIZkr5kw' -d '{  
   "usernames": [  
     "user2"  
   ]  
 }' 'http://a1.easemob.com/easemob-demo/testapp/users/user1/blocks/users'
```

#### Examples of possible returned results

**Return value 200, means add blacklist successfully**

``` json
{
  "action": "post",
  "application": "8be024f0-e978-11e8-b697-5d598d5f8402",
  "uri": "https://a1.easemob.com/easemob-demo/testapp",
  "entities": [],
  "data": [
    "user2"
  ],
  "timestamp": 1542600372046,
  "duration": 11,
  "organization": "easemob-demo",
  "applicationName": "testapp"
}
```

**returns a value of 404, indicating that this IM user does not exist**

``` json
{
  "error": "service_resource_not_found",
  "timestamp": 1542600419231,
  "duration": 0,
  "exception": "org.apache.usergrid.services.exceptions.ServiceResourceNotFoundException",
  "error_description": "Service resource not found"
}
```

**returns 400, indicating that the user being added does not exist**

``` json
{
  "error": "illegal_argument",
  "timestamp": 1542600449246,
  "duration": 0,
  "exception": "java.lang.IllegalArgumentException",
  "error_description": "attempt to adding new block[user20] for user[user1],but user[user20] not found."
}
```

**return value 401, means unauthorized\[no token, token error, token expired\]**

``` json
{
  "error": "unauthorized",
  "timestamp": 1542600528822,
  "duration": 0,
  "exception": "org.apache.shiro.authz.UnauthorizedException",
  "error_description": "Subject does not have permission [applications:post:8be024f0-e978-11e8-b697-5d598d5f8402:/users/4759aa70-eba5-11e8-925f-6fa0510823ba]"
}
```

If the return result is <font color='red'> 429, 503 </font> or other <font color='red'> 5xx </font>, it may mean that the interface is flow-limited, please pause slightly and retry. See [interface flow restriction description](/server_rest_interface_flow_limiting_instructions.html) for details

[Online testing using Easemob REST API](http://api-docs.easemob.com/)

------------------------------------------------------------------------

### Remove Blacklist

Remove users from IM user's blacklist. After removing a user from the blacklist, the user is restored to the friend, or unfriend, relationship. You can send and receive messages normally.

#### HTTP Request

<table border="1" cellspacing="0" bordercolor="#000000">
  <tr>
    <th>DELETE</th>
    <th>/{org_name}/{app_name}/users/{owner_username}/blocks/users/{blocked_username}</th>
  </tr>
</table>

You need to fill in the request with {owner_username}, the username you want to remove from the blacklist

#### Request Headers

<table border="1" cellspacing="0" bordercolor="#000000">
  <tr>
    <th>Parameter</th>
    <th>Description</th>
  </tr>
  <tr>
    <td>Content-Type</td>
    <td>application/json</td>
  </tr>
  <tr>
    <td>Authorization</td>
    <td>Bearer \${token}</td>
  </tr>
</table>

#### Response Body

<table border="1" cellspacing="0" bordercolor="#000000">
  <tr>
    <th>Parameter</th>
    <th>Description</th>
  </tr>
  <tr>
    <td>entities</td>
    <td>Details of the IM users removed from the blacklist</td>
  </tr>
</table>

#### Request Example

``` php
curl -X DELETE -H 'Accept: application/json' -H 'Authorization: Bearer YWMte3bGuOukEeiTkNP4grL7iwAAAAAAAAAAAAAAAAAAAAGL4CTw6XgR6LaXXVmNX4QCAgMAAAFnKdc-ZgBPGgBFTrLhhyK8woMEI005emtrLJFJV6aoxsZSioSIZkr5kw' 'http://a1.easemob.com/easemob-demo/testapp/users/user1/blocks/users/user2'
```

#### Examples of possible returned results

**Returns 200, indicating successful blacklisting**

``` json
{
  "action": "delete",
  "application": "8be024f0-e978-11e8-b697-5d598d5f8402",
  "path": "/users/4759aa70-eba5-11e8-925f-6fa0510823ba/blocks",
  "uri": "https://a1.easemob.com/easemob-demo/testapp/users/4759aa70-eba5-11e8-925f-6fa0510823ba/blocks",
  "entities": [
    {
      "uuid": "b2aade90-e978-11e8-a974-f3368f82e4f1",
      "type": "user",
      "created": 1542356523769,
      "modified": 1542597334500,
      "username": "user2",
      "activated": true,
      "nickname": "testuser"
    }
  ],
  "timestamp": 1542600712985,
  "duration": 20,
  "organization": "easemob-demo",
  "applicationName": "testapp"
}
```

**returns a value of 404, indicating that this IM user or the subtracted user does not exist**

``` json
{
  "error": "service_resource_not_found",
  "timestamp": 1542600858000,
  "duration": 0,
  "exception": "org.apache.usergrid.services.exceptions.ServiceResourceNotFoundException",
  "error_description": "Service resource not found"
}
```

**return value 401, means unauthorized\[no token, token error, token expired\]**

``` json
{
  "error": "unauthorized",
  "timestamp": 1542600910575,
  "duration": 0,
  "exception": "org.apache.shiro.authz.UnauthorizedException",
  "error_description": "Subject does not have permission [applications:delete:8be024f0-e978-11e8-b697-5d598d5f8402:/users/4759aa70-eba5-11e8-925f-6fa0510823ba]"
}
```

If the return result is <font color='red'> 429, 503 </font> or other <font color='red'> 5xx </font>, it may mean that the interface is flow-limited, please pause slightly and retry. See [interface flow restriction description](/server_rest_interface_flow_limiting_instructions.html) for details

[Online testing using the Easemob REST API](http://api-docs.easemob.com/)

------------------------------------------------------------------------

## Online and Offline

Easemob provides several interfaces to view the online/offline status of users, the online/offline status and number of messages from users.

<table border="1" cellspacing="0" bordercolor="#000000">
  <tr>
    <th>Name</th>
    <th>Request</th>
    <th>Description</th>
  </tr>
  <tr>
    <td>Get user online status</td>
    <td>/{org_name}/{app_name}/users/{username}/status</td>
    <td>View the online status of a user</td>
  </tr>
  <tr>
    <th>Get user online status in bulk</th>
    <th>/{org_name}/{app_name}/users/batch/status</th>
    <th>Bulk view of users' online status</th>
  </tr>
  <tr>
    <th>Get the number of offline messages</th>
    <th>/{org_name}/{app_name}/users/{owner_username}/offline_msg_count</th>
    <th>Get the number of offline messages for an IM user</th>
  </tr>
  <tr>
    <th>Get the status of offline messages</th>
    <th>/{org_name}/{app_name}/users/{username}/offline_msg_status/{msg_id}</th>
    <th>View the user's offline message status by the ID of the offline message</th>
  </tr>
</table>

### Get user online status

View the online status of a user.

#### HTTP Request

<table border="1" cellspacing="0" bordercolor="#000000">
  <tr>
    <th>GET</th>
    <th>/{org_name}/{app_name}/users/{username}/status</th>
  </tr>
</table>

You need to fill in {username} corresponding to the request, to get the online status of the user name.

#### Request Headers

<table border="1" cellspacing="0" bordercolor="#000000">
  <tr>
    <th>Parameter</th>
    <th>Description</th>
  </tr>
  <tr>
    <td>Content-Type</td>
    <td>application/json</td>
  </tr>
  <tr>
    <td>Authorization</td>
    <td>Bearer \${token}</td>
  </tr>
</table>

#### Response Body

View the information contained in the data field in the return value

<table border="1" cellspacing="0" bordercolor="#000000">
  <tr>
    <th>Parameter</th>
    <th>Description</th>
  </tr>
  <tr>
    <td>username</td>
    <td>will be displayed as "user1" which corresponds to the username of the query, "offline" means offline, "online" is the current online user in the table</td>
  </tr>
</table>

#### Request Example

``` php
curl -X GET -H 'Accept: application/json' -H 'Authorization: Bearer YWMte3bGuOukEeiTkNP4grL7iwAAAAAAAAAAAAAAAAAAAAGL4CTw6XgR6LaXXVmNX4QCAgMAAAFnKdc-ZgBPGgBFTrLhhyK8woMEI005emtrLJFJV6aoxsZSioSIZkr5kw' 'http://a1.easemob.com/easemob-demo/testapp/users/user1/status'
```

#### Examples of possible returned results

**returns 200, indicating the success of the user status query**

``` json
{
  "action": "get",
  "uri": "http://a1.easemob.com/easemob-demo/testapp/users/user1/status",
  "entities": [],
  "data": {
    "user1": "offline"
  },
  "timestamp": 1542601284531,
  "duration": 4,
  "count": 0
}
```

**returns a value of 404, indicating that this IM user does not exist**

``` json
{
  "error": "service_resource_not_found",
  "timestamp": 1542601345042,
  "duration": 0,
  "exception": "org.apache.usergrid.services.exceptions.ServiceResourceNotFoundException",
  "error_description": "Service resource not found"
}
```

**return value 401, means unauthorized\[no token, token error, token expired\]**

``` json
{
  "error": "unauthorized",
  "timestamp": 1542601375113,
  "duration": 0,
  "exception": "org.apache.shiro.authz.UnauthorizedException",
  "error_description": "Subject does not have permission [applications:get:8be024f0-e978-11e8-b697-5d598d5f8402:/users/4759aa70-eba5-11e8-925f-6fa0510823ba]"
}
```

If the return result is <font color='red'> 429, 503 </font> or other <font color='red'> 5xx </font>, it may mean that the interface is flow-limited, please pause slightly and retry. See [interface flow restriction description](/server_rest_interface_flow_limiting_instructions.html) for details

[Online testing using the Easemob REST API](http://api-docs.easemob.com/)

------------------------------------------------------------------------

### Get user online status in bulk

Batch view the online status of users, up to 100 users at the same time.

#### HTTP Request

<table border="1" cellspacing="0" bordercolor="#000000">
  <tr>
    <th>POST</th>
    <th>/{org_name}/{app_name}/users/batch/status</th>
  </tr>
</table>

#### Request Headers

<table border="1" cellspacing="0" bordercolor="#000000">
  <tr>
    <th>Parameter</th>
    <th>Description</th>
  </tr>
  <tr>
    <td>Content-Type</td>
    <td>application/json</td>
  </tr>
  <tr>
    <td>Authorization</td>
    <td>Bearer \${token}</td>
  </tr>
</table>

#### Request Body

<table border="1" cellspacing="0" bordercolor="#000000">
  <tr>
    <th>Parameter</th>
    <th>Description</th>
  </tr>
  <tr>
    <td>usernames</td>
    <td>"user1", "user2"，the user name to be queried for status is submitted as an array，\<wrap em>no more than 100 at most\</wrap></td>
  </tr>
</table>

#### Response Body

View the information contained in the data field in the return value

<table border="1" cellspacing="0" bordercolor="#000000">
  <tr>
    <th>Parameter</th>
    <th>Description</th>
  </tr>
  <tr>
    <td>usernames</td>
    <td>It will be displayed as "user1" which corresponds to the username of the query, "offline" represents offline, "online" is the current online user in the table</td>
  </tr>
</table>

#### Request Example

``` php
curl -X POST http://a1.easemob.com/easemob-demo/chatdemoui/users/batch/status -H 'Authorization: Bearer YWMte3bGuOukEeiTkNP4grL7iwAAAAAAAAAAAAAAAAAAAAGL4CTw6XgR6LaXXVmNX4QCAgMAAAFnKdc-ZgBPGgBFTrLhhyK8woMEI005emtrLJFJV6aoxsZSioSIZkr5kw' -H 'Content-Type: application/json' -d '{"usernames":["user1","user2"]}'
```

#### Examples of possible returned results

**returns 200, indicating the success of the user status query**

If an incorrect user name is requested, i.e., a user state that does not exist is requested, the returned Parameter must be offline and this interface does not verify the user name.

``` json
{
    "action": "get batch user status",
    "data": [
        {
            "user1": "offline"
        },
        {
            "user2": "offline"
        }
    ],
    "timestamp": 1552280231926,
    "duration": 4
}
```

**return value 401, means unauthorized\[no token, token error, token expired\]**

``` json
{
  "error": "unauthorized",
  "timestamp": 1542601375113,
  "duration": 0,
  "exception": "org.apache.shiro.authz.UnauthorizedException",
  "error_description": "Subject does not have permission [applications:get:8be024f0-e978-11e8-b697-5d598d5f8402:/users/4759aa70-eba5-11e8-925f-6fa0510823ba]"
}
```

If the return result is <font color='red'> 429, 503 </font> or other <font color='red'> 5xx </font>, it may mean that the interface is flow-limited, please pause a little and retry; or or the number of requested users is greater than 100, please resend the correct number of requests. See [interface flow limiting instructions](/server_rest_interface_flow_limiting_instructions.html) for details

------------------------------------------------------------------------

### Get the number of user offline messages

Gets the number of offline messages for IM users.

#### HTTP Request

<table border="1" cellspacing="0" bordercolor="#000000">
  <tr>
    <th>GET</th>
    <th>/{org_name}/{app_name}/users/{owner_username}/offline_msg_count</th>
  </tr>
</table>

You need to fill in {owner_username} corresponding to the request, the username to get the number of offline messages.

#### Request Headers

<table border="1" cellspacing="0" bordercolor="#000000">
  <tr>
    <th>Parameter</th>
    <th>Description</th>
  </tr>
  <tr>
    <td>Content-Type</td>
    <td>application/json</td>
  </tr>
  <tr>
    <td>Authorization</td>
    <td>Bearer \${token}</td>
  </tr>
</table>

#### Response Body

View the information contained in the data field in the return value

<table border="1" cellspacing="0" bordercolor="#000000">
  <tr>
    <th>Parameter</th>
    <th>Description</th>
  </tr>
  <tr>
    <td>username</td>
    <td>It will be displayed as "user1" which corresponds to the username of the query, and "number" which is the number of current offline messages</td>
  </tr>
</table>

#### Request Example

``` php
curl -X GET -H 'Accept: application/json' -H 'Authorization: Bearer YWMte3bGuOukEeiTkNP4grL7iwAAAAAAAAAAAAAAAAAAAAGL4CTw6XgR6LaXXVmNX4QCAgMAAAFnKdc-ZgBPGgBFTrLhhyK8woMEI005emtrLJFJV6aoxsZSioSIZkr5kw' 'http://a1.easemob.com/easemob-demo/testapp/users/user1/offline_msg_count'
```

#### Examples of possible returned results

**Return value 200, means the query for the number of offline messages is successful**

``` json
{
  "action": "get",
  "uri": "http://tomcatcluster_users/easemob-demo/testapp/users/user1/offline_msg_count",
  "entities": [],
  "data": {
    "user1": 0
  },
  "timestamp": 1542601518137,
  "duration": 3,
  "count": 0
}
```

**returns a value of 404, indicating that this IM user does not exist**

``` json
{
  "error": "service_resource_not_found",
  "timestamp": 1542601576537,
  "duration": 0,
  "exception": "org.apache.usergrid.services.exceptions.ServiceResourceNotFoundException",
  "error_description": "Service resource not found"
}
```

**return value 401, means unauthorized\[no token, token error, token expired\]**

``` json
{
  "error": "unauthorized",
  "timestamp": 1542601604510,
  "duration": 0,
  "exception": "org.apache.shiro.authz.UnauthorizedException",
  "error_description": "Subject does not have permission [applications:get:8be024f0-e978-11e8-b697-5d598d5f8402:/users/4759aa70-eba5-11e8-925f-6fa0510823ba]"
}
```

If the return result is <font color='red'> 429, 503 </font> or other <font color='red'> 5xx </font>, it may mean that the interface is flow-limited, please pause slightly and retry. See [interface flow restriction description](/server_rest_interface_flow_limiting_instructions.html) for details

[Online testing using the Easemob REST API](http://api-docs.easemob.com/)

------------------------------------------------------------------------

### Get the status of an offline message

Get the offline message status of IM users, view the status of offline messages of users offline messages

#### HTTP Request

<table border="1" cellspacing="0" bordercolor="#000000">
  <tr>
    <th>GET</th>
    <th>/{org_name}/{app_name}/users/{username}/offline_msg_status/{msg_id}</th>
  </tr>
</table>

Need to fill in {username} corresponding to the request, the username to get the status of the offline message. {msg_id}, the ID of the offline message status to be viewed.

#### Request Headers

<table border="1" cellspacing="0" bordercolor="#000000">
  <tr>
    <th>Parameter</th>
    <th>Description</th>
  </tr>
  <tr>
    <td>Content-Type</td>
    <td>application/json</td>
  </tr>
  <tr>
    <td>Authorization</td>
    <td>Bearer \${token}</td>
  </tr>
</table>

#### Response Body

View the information contained in the data field in the return value

<table border="1" cellspacing="0" bordercolor="#000000">
  <tr>
    <th>Parameter</th>
    <th>Description</th>
  </tr>
  <tr>
    <td>msg_id</td>
    <td>"corresponding ID number \" means the corresponding message id,  "delivered\" means the status of the message has been delivered,  "undelivered\" means the message has not been delivered</td>
  </tr>
</table>

#### Request Example

``` php
curl -X GET -H 'Accept: application/json' -H 'Authorization: Bearer YWMte3bGuOukEeiTkNP4grL7iwAAAAAAAAAAAAAAAAAAAAGL4CTw6XgR6LaXXVmNX4QCAgMAAAFnKdc-ZgBPGgBFTrLhhyK8woMEI005emtrLJFJV6aoxsZSioSIZkr5kw' 'http://a1.easemob.com/easemob-demo/testapp/users/user1/offline_msg_status/123'
```

#### Examples of possible returned results

**return value 200, means the query offline message status is successful**

``` json
{
  "action": "get",
  "uri": "http://a1.easemob.com/easemob-demo/testapp/users/user1/offline_msg_status/123",
  "entities": [],
  "data": {
    "123": "delivered"
  },
  "timestamp": 1542601830084,
  "duration": 5,
  "count": 0
}
```

**returns a value of 404, indicating that this IM user does not exist**

``` json
{
  "error": "service_resource_not_found",
  "timestamp": 1542601878917,
  "duration": 0,
  "exception": "org.apache.usergrid.services.exceptions.ServiceResourceNotFoundException",
  "error_description": "Service resource not found"
}
```

**return value 401, means unauthorized\[no token, token error, token expired\]**

``` json
{
  "error": "unauthorized",
  "timestamp": 1542601903781,
  "duration": 0,
  "exception": "org.apache.shiro.authz.UnauthorizedException",
  "error_description": "Subject does not have permission [applications:get:8be024f0-e978-11e8-b697-5d598d5f8402:/users/4759aa70-eba5-11e8-925f-6fa0510823ba]"
}
```

If the return result is <font color='red'> 429, 503 </font> or other <font color='red'> 5xx </font>, it may mean that the interface is flow-limited, please pause slightly and retry. See [interface flow restriction description](/server_rest_interface_flow_limiting_instructions.html) for details

[Online testing using Easemob REST API](http://api-docs.easemob.com/)

------------------------------------------------------------------------

## Account Disabling and Unblocking

Easemob provides an interface for disabling and unblocking IM users. Disabling a user will immediately take them offline and prevent them from logging into Easemob until they are unblocked. This is commonly used for immediate handling of abnormal users.

<table border="1" cellspacing="0" bordercolor="#000000">
  <tr>
    <th>Name</th>
    <th>Request</th>
    <th>Description</th>
  </tr>
  <tr>
    <th>User account disable</th>
    <th>/{org_name}/{app_name}/users/{username}/deactivate</th>
    <th>Login accounts of disabled users must be restored by unbanning them</th>
  </tr>
  <tr>
    <th>User account unblock</th>
    <th>/{org_name}/{app_name}/users/{username}/activate</th>
    <th>Users resume normal use after the ban is lifted</th>
  </tr>
</table>

### User account disable

Disable an IM user's account. After disabling, the user cannot log in, and the account will resume normal use after the next unban.

#### HTTP Request

<table border="1" cellspacing="0" bordercolor="#000000">
  <tr>
    <th>POST</th>
    <th>/{org_name}/{app_name}/users/{username}/deactivate</th>
  </tr>
</table>

You need to fill in {username} corresponding to the username to be disabled in the request.

#### Request Headers

<table border="1" cellspacing="0" bordercolor="#000000">
  <tr>
    <th>Parameter</th>
    <th>Description</th>
  </tr>
  <tr>
    <td>Content-Type</td>
    <td>application/json</td>
  </tr>
  <tr>
    <td>Authorization</td>
    <td>Bearer \${token}</td>
  </tr>
</table>

#### Response Body

View the information contained in the entities field in the return value

<table border="1" cellspacing="0" bordercolor="#000000">
  <tr>
    <th>Parameter</th>
    <th>Description</th>
  </tr>
  <tr>
    <td>uuid</td>
    <td>User's UUID, identification field</td>
  </tr>
  <tr>
    <td>type</td>
    <td>"user"User Type</td>
  </tr>
  <tr>
    <td>username</td>
    <td>Username, also known as Easemob ID</td>
  </tr>
  <tr>
    <td>activated</td>
    <td>Whether the user is activated, "true" is activated, "false" is blocked, the block needs to be unblocked through the unblock interface in order to log in normally</td>
  </tr>
</table>

#### Request Example

``` php
curl -X POST -H 'Content-Type: application/json' -H 'Accept: application/json' -H 'Authorization: Bearer YWMte3bGuOukEeiTkNP4grL7iwAAAAAAAAAAAAAAAAAAAAGL4CTw6XgR6LaXXVmNX4QCAgMAAAFnKdc-ZgBPGgBFTrLhhyK8woMEI005emtrLJFJV6aoxsZSioSIZkr5kw' 'http://a1.easemob.com/easemob-demo/testapp/users/user1/deactivate'
```

#### Examples of possible returned results

**returns a value of 200, indicating the success of disabling the user account**

``` json
{
  "action": "Deactivate user",
  "entities": [
    {
      "uuid": "4759aa70-eba5-11e8-925f-6fa0510823ba",
      "type": "user",
      "created": 1542595573399,
      "modified": 1542597578147,
      "username": "user1",
      "activated": false,
      "nickname": "user"
    }
  ],
  "timestamp": 1542602157258,
  "duration": 12
}
```

**returns a value of 400, indicating that this IM user does not exist**

``` json
{
  "error": "management",
  "timestamp": 1542602213887,
  "duration": 0,
  "exception": "org.apache.usergrid.management.exceptions.ManagementException",
  "error_description": "User with id null does not exist in app 8be024f0-e978-11e8-b697-5d598d5f8402"
}
```

**return value 401, means unauthorized\[no token, token error, token expired\]**

``` json
{
  "error": "unauthorized",
  "timestamp": 1542602239851,
  "duration": 0,
  "exception": "org.apache.usergrid.rest.exceptions.SecurityException",
  "error_description": "No admin user access authorized"
}
```

If the return result is <font color='red'> 429, 503 </font> or other <font color='red'> 5xx </font>, it may mean that the interface is flow-limited, please pause slightly and retry. See [interface flow restriction description](/server_rest_interface_flow_limiting_instructions.html) for details

[Online testing using Easemob REST API](http://api-docs.easemob.com/)

------------------------------------------------------------------------

### User account unblock

Unblock the IM user account from the disable to unblock operation, which requires the user to log in again.

#### HTTP Request

<table border="1" cellspacing="0" bordercolor="#000000">
  <tr>
    <th>POST</th>
    <th>/{org_name}/{app_name}/users/{username}/activate</th>
  </tr>
</table>

You need to fill in {username} corresponding to the username to be unblocked in the request.

#### Request Headers

<table border="1" cellspacing="0" bordercolor="#000000">
  <tr>
    <th>Parameter</th>
    <th>Description</th>
  </tr>
  <tr>
    <td>Content-Type</td>
    <td>application/json</td>
  </tr>
  <tr>
    <td>Authorization</td>
    <td>Bearer \${token}</td>
  </tr>
</table>

#### Response Body

<table border="1" cellspacing="0" bordercolor="#000000">
  <tr>
    <th>Parameter</th>
    <th>Description</th>
  </tr>
  <tr>
    <td>action</td>
    <td>operation, "activate user\" means unblock/activate IM user</td>
  </tr>
</table>

#### Request Example

``` php
curl -X POST -H 'Content-Type: application/json' -H 'Accept: application/json' -H 'Authorization: Bearer YWMte3bGuOukEeiTkNP4grL7iwAAAAAAAAAAAAAAAAAAAAGL4CTw6XgR6LaXXVmNX4QCAgMAAAFnKdc-ZgBPGgBFTrLhhyK8woMEI005emtrLJFJV6aoxsZSioSIZkr5kw' 'http://a1.easemob.com/easemob-demo/testapp/users/user1/activate'
```

#### Examples of possible returned results

**Returns 200, indicating that the user account was unblocked successfully**

``` json
{
  "action": "activate user",
  "timestamp": 1542602404132,
  "duration": 9
}
```

**returns a value of 404, indicating that this IM user does not exist**

``` json
{
  "error": "service_resource_not_found",
  "timestamp": 1542602447129,
  "duration": 0,
  "exception": "org.apache.usergrid.services.exceptions.ServiceResourceNotFoundException",
  "error_description": "Service resource not found"
}
```

**Return value 401, means unauthorized \ [no token, token error, token expired \]**

``` json
{
  "error": "unauthorized",
  "timestamp": 1542602487688,
  "duration": 0,
  "exception": "org.apache.usergrid.rest.exceptions.SecurityException",
  "error_description": "No admin user access authorized"
}
```

If the return result is <font color='red'> 429, 503 </font> or other <font color='red'> 5xx </font>, it may mean that the interface is flow-limited, please pause slightly and retry. See [interface flow restriction description](/server_rest_interface_flow_limiting_instructions.html) for details

[Online testing using the Easemob REST API](http://api-docs.easemob.com/)

------------------------------------------------------------------------

## Forced downline

Force the IM user status to offline and the user will need to log in again to use it properly.

#### HTTP Request

<table border="1" cellspacing="0" bordercolor="#000000">
  <tr>
    <th>GET</th>
    <th>/{org_name}/{app_name}/users/{username}/disconnect</th>
  </tr>
</table>

You need to fill in {username} corresponding to the request, the username to be forced offline.

#### Request Headers

<table border="1" cellspacing="0" bordercolor="#000000">
  <tr>
    <th>Parameter</th>
    <th>Description</th>
  </tr>
  <tr>
    <td>Content-Type</td>
    <td>application/json</td>
  </tr>
  <tr>
    <td>Authorization</td>
    <td>Bearer \${token}</td>
  </tr>
</table>

#### Response Body

View the information contained in the data field in the return value

<table border="1" cellspacing="0" bordercolor="#000000">
  <tr>
    <th>Parameter</th>
    <th>Description</th>
  </tr>
  <tr>
    <td>result</td>
    <td>The result of the operation, "true\" means that the user has been forced offline</td>
  </tr>
</table>

#### Request Example

``` php
curl -X GET -H 'Accept: application/json' -H 'Authorization: Bearer YWMte3bGuOukEeiTkNP4grL7iwAAAAAAAAAAAAAAAAAAAAGL4CTw6XgR6LaXXVmNX4QCAgMAAAFnKdc-ZgBPGgBFTrLhhyK8woMEI005emtrLJFJV6aoxsZSioSIZkr5kw' 'http://a1.easemob.com/easemob-demo/testapp/users/user1/disconnect'
```

#### Examples of possible returned results

**return value 200, means force user offline successfully**

``` json
{
  "action": "get",
  "uri": "http://a1.easemob.com/easemob-demo/testapp/users/user1/disconnect",
  "entities": [],
  "data": {
    "result": true
  },
  "timestamp": 1542602601332,
  "duration": 6,
  "count": 0
}
```

**returns a value of 404, indicating that this IM user does not exist**

``` json
{
  "error": "service_resource_not_found",
  "timestamp": 1542602640827,
  "duration": 0,
  "exception": "org.apache.usergrid.services.exceptions.ServiceResourceNotFoundException",
  "error_description": "Service resource not found"
}
```

**return value 401, means unauthorized\[no token, token error, token expired\]**

``` json
{
  "error": "unauthorized",
  "timestamp": 1542602666575,
  "duration": 0,
  "exception": "org.apache.shiro.authz.UnauthorizedException",
  "error_description": "Subject does not have permission [applications:get:8be024f0-e978-11e8-b697-5d598d5f8402:/users/4759aa70-eba5-11e8-925f-6fa0510823ba]"
}
```

If the return result is <font color='red'> 429, 503 </font> or other <font color='red'> 5xx </font>, it may mean that the interface is flow-limited, please pause slightly and retry. See [interface flow restriction description](/server_rest_interface_flow_limiting_instructions.html) for details

[Online testing using Easemob REST API](http://api-docs.easemob.com/)
