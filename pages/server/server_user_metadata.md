---
title: User Metadata
keywords: server
sidebar: server_sidebar
toc: true
permalink: server_user_metadata.html
folder: server
---

### Setting user metadata

Set the user metadata for the specified user. Only the user himself or the app administrator has permission to do the settings.

#### HTTP Request

<table border="1" cellspacing="0" bordercolor="#000000">
  <tr>
    <th>PUT</th>
    <th>/{org_name}/{app_name}/metadata/user/{username}</th>
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
    <td>application/x-www-form-urlencoded</td>
  </tr>
  <tr>
    <td>Authorization</td>
    <td>Bearer ${token}</td>
  </tr>
</table>

#### Request Body

The content of a user attribute is one or more plain text key-value pairs.
By default the total length of properties for a single user must not exceed 2KB.
By default the total length of all properties for all users under one app must not exceed 10GB.

The keys used in the request example are avater, ext, nickname. However, of course, this is not fixed just an example, it is entirely up to you to decide what the key values are.

The following fields are used for user metadata on the SDK side

<table border="1" cellspacing="0" bordercolor="#000000">
  <tr>
    <th>Field</th>
    <th>Type</th>
    <th>Remark</th>
  </tr>
  <tr>
    <td>nickname</td>
    <td>String</td>
    <td>Nickname</td>
  </tr>
  <tr>
    <td>avatarurl</td>
    <td>String</td>
    <td>Avatar</td>
  </tr>
  <tr>
    <td>phone</td>
    <td>String</td>
    <td>Contact details</td>
  </tr>
  <tr>
    <td>mail</td>
    <td>String</td>
    <td>Email</td>
  </tr>
  <tr>
    <td>gender</td>
    <td>Number</td>
    <td>Gender</td>
  </tr>
  <tr>
    <td>sign</td>
    <td>String</td>
    <td>Signature</td>
  </tr>
  <tr>
    <td>birth</td>
    <td>String</td>
    <td>Birthday</td>
  </tr>
  <tr>
    <td>ext</td>
    <td>String</td>
    <td>Custom Fields</td>
  </tr>
</table>

#### Response Body

View the information contained in the data field in the return value

#### Example request

``` php
curl -X PUT -H 'Content-Type: application/x-www-form-urlencoded' -H 'Authorization: Bearer WMte3bGuOukEeiTkNP4grL7iwAAAAAAAAAAAAAAAAAAAAGL4CTw6XgR6LaXXVmNX4QCAgMAAAFnKdc-ZgBPGgBFTrLhhyK8woMEI005emtrLJFJV6aoxsZSioSIZkr5kw12' -d 'avatar=http://www.chat.com/avatar.png&ext=ext&nickname=nickname' 'http://a1.easecdn.com/chat-demo/testapp/metadata/user/user1'
```

#### Examples of possible results returned

**Returns a value of 200, indicating success in setting user metadata**

``` json
{
    "timestamp":1620445147011,
    "data":{
        "ext":"ext",
        "nickname":"nickname",
        "avatar":"http://www.chat.com/avatar.png"
    },
    "duration":166
}
```

**Return a value of 401, only the user himself or the app administrator has the right to modify his metadata.**
**A 401 is also returned when an ordinary user tries to modify the metadata of a non-existent user, because the ordinary user has no right to know whether a username exists or not.**

``` json
{
    "desc": "unauthorized",
    "duration": 10,
    "timestamp": 1616573382270
}
```

**Returns value 403, the user attribute value you are trying to set exceeds the length limit.**
**The limit may be a limit on the total length of metadata under a single user, or it may be a limit on the total length of all user metadata under an app.**

``` json
{
    "desc": "user metadata length exceed the limit",
    "duration": 10,
    "timestamp": 1616573382270
}
```

**Returns a value of 404, indicating that the user does not exist.**

``` json
{
    "timestamp":1620445340631,
    "desc":"Not Found",
    "duration":0
}
```

**Returns 409, the user you are trying to modify has already been modified by "someone else".**
**Since we use optimistic locks on user metadata to handle concurrent modifications. When multiple threads are making changes to the same user's metadata at the same time，**
**only one of the threads will succeed and return 200, the others will fail and return 409.**

``` json
{
   "desc": "update userMetadata failed, concurrent updates not allowed",
   "duration": 10,
   "timestamp": 1616573382270
}
```

**Returns a value of 500, which is usually a mysql error.**
**If the problem keep existing, please contact our technical support team.**

``` json
{
   "desc": "org.hibernate.exception.JDBCConnectionException: Unable to acquire JDBC Connection",
   "duration": 10,
   "timestamp": 1616573382270
}
```

If the return result is <font color='red'> 429, 503 </font> or other <font color='red'> 5xx </font>,it may mean that the interface has been restricted, please pause slightly and retry.For more information, see [Interface current limit description](/server_rest_interface_flow_limiting_instructions.html)

[[Online testing using the Platform API].](http://api-docs.easemob.com/)

------------------------------------------------------------------------

### Get user metadata

Gets all user attribute key-value pairs for the specified user.
Returns null data if the specified user does not exist, or if the specified user's metadata do not exist {}.

#### HTTP Request

<table border="1" cellspacing="0" bordercolor="#000000">
  <tr>
    <th>GET</th>
    <th>/{org_name}/{app_name}/metadata/user/{username}</th>
  </tr>
</table>

You need to fill in {username} corresponding to the request, and you need to get the Chat username of the user property.

#### Request         Headers

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
    <td>Bearer ${token}</td>
  </tr>
</table>

#### Response Body

View the information contained in the data field in the return value

#### Example request

``` php
curl -X GET -H 'Content-Type: application/json' -H 'Authorization: Bearer YWMte3bGuOukEeiTkNP4grL7iwAAAAAAAAAAAAAAAAAAAAGL4CTw6XgR6LaXXVmNX4QCAgMAAAFnKdc-ZgBPGgBFTrLhhyK8woMEI005emtrLJFJV6aoxsZSioSIZkr5kw' 'http://a1.easecdn.com/chat-demo/testapp/metadata/user/user1'
```

#### Examples of possible results returned

**Returns a value of 200, indicating success in obtaining user metadata**

``` json
{
    "timestamp":1620446048161,
    "data":{
        "ext":"ext",
        "nickname":"nickname",
        "avatar":"http://www.chat.com/avatar.png"
    },
    "duration":2
}
```

**Returns a value of 404, indicating that the user does not exist**

``` json
{
    "timestamp":1620446033278,
    "data":{

    },
    "duration":9
}
```

**Returns a value of 500, which is usually a mysql error.**
**If the problem keep existing, please contact our technical support team.**

``` json
{
    "desc": "org.hibernate.exception.JDBCConnectionException: Unable to acquire JDBC Connection",
    "duration": 10,
    "timestamp": 1616573382270
}
```

If the return result is <font color='red'> 429, 503 </font> or other <font color='red'> 5xx </font>,It may mean that the interface has been restricted, please pause slightly and retry.For more information, see [Interface current limit description](/server_rest_interface_flow_limiting_instructions.html)

[Online testing using the Platform API](http://api-docs.easemob.com/)

------------------------------------------------------------------------

### Get Users' Metadata

Query user metadata based on the specified list of usernames and list of metadata.
Returns empty data if the specified user/attribute does not exist {}. Specify up to 100 users at a time.

#### HTTP Request

<table border="1" cellspacing="0" bordercolor="#000000">
  <tr>
    <th>POST</th>
    <th>/{org_name}/{app_name}/metadata/user/get</th>
  </tr>
</table>

#### Request          Headers

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
    <td>Bearer ${token}</td>
  </tr>
</table>

#### Request          Body

<table border="1" cellspacing="0" bordercolor="#000000">
  <tr>
    <th>Parameter</th>
    <th>Description</th>
  </tr>
  <tr>
    <td>targets</td>
    <td>List of usernames, up to 100 usernames</td>
  </tr>
  <tr>
    <td>properties</td>
    <td>A list of property names, the query will only return the properties contained in this list, properties that are not in this list will be ignored</td>
  </tr>
</table>

#### Response Body

View the information contained in the data field in the return value

#### Example request

``` php
curl -X POST -H 'Content-Type:  application/json' -H 'Authorization: Bearer YWMte3bGuOukEeiTkNP4grL7iwAAAAAAAAAAAAAAAAAAAAGL4CTw6XgR6LaXXVmNX4QCAgMAAAFnKdc-ZgBPGgBFTrLhhyK8woMEI005emtrLJFJV6aoxsZSioSIZkr5kw' -d '{
  "properties": [
    "avatar",
    "ext",
    "nickname"
  ],
  "targets": [
    "user1",
    "user2",
    "user3"
  ]
}' 'http://a1.easecdn.com/chat-demo/testapp/metadata/user/get'
```

#### Examples of possible results returned

**Returns a value of 200, which means that getting the user's metadata was successful**

``` json
{
    "timestamp":1620448826647,
    "data":{
        "user1":{
            "ext":"ext",
            "nickname":"nickname",
            "avatar":"http://www.chat.com/avatar.png"
        },
        "user2":{
            "ext":"ext",
            "nickname":"nickname",
            "avatar":"http://www.chat.com/avatar.png"
        },
        "user3":{
            "ext":"ext",
            "nickname":"nickname",
            "avatar":"http://www.chat.com/avatar.png"
        }
    },
    "duration":3
}
```

**Returns value 403, the number of specified usernames exceeds 100**

``` json
{
   "desc": "exceed allowed batch size 100",
   "duration": 10,
   "timestamp": 1616573382270
}
```

**Returns a value of 500, which is usually a mysql error.**
**If the problem keep existing, please contact our technical support team.**

``` json
{
    "desc": "org.hibernate.exception.JDBCConnectionException: Unable to acquire JDBC Connection",
    "duration": 10,
    "timestamp": 1616573382270
}
```

If the return result is <font color='red'> 429, 503 </font> or other <font color='red'> 5xx </font>,It may mean that the interface has been restricted, please pause slightly and retry.For more information, see [Interface current limit description](/server_rest_interface_flow_limiting_instructions.html)

[Online testing using the Platform API](http://api-docs.easemob.com/)

------------------------------------------------------------------------

### Get the total size of user metadata

Gets the total user attribute size, in Bytes, for all users under this app.

#### HTTP Request

<table border="1" cellspacing="0" bordercolor="#000000">
  <tr>
    <th>GET</th>
    <th>/{org_name}/{app_name}/metadata/user/capacity</th>
  </tr>
</table>

#### Request         Headers

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
    <td>Bearer ${token}</td>
  </tr>
</table>

#### Response Body

View the information contained in the data field in the return value

#### Example request

``` php
curl -X GET -H 'Content-Type: application/json' -H 'Authorization: Bearer YWMte3bGuOukEeiTkNP4grL7iwAAAAAAAAAAAAAAAAAAAAGL4CTw6XgR6LaXXVmNX4QCAgMAAAFnKdc-ZgBPGgBFTrLhhyK8woMEI005emtrLJFJV6aoxsZSioSIZkr5kw' 'http://a1.easecdn.com/chat-demo/testapp/metadata/user/capacity'
```

#### Examples of possible results returned

**Returns a value of 200, indicating success in obtaining the total size of the user's metadata**

``` json
{
    "timestamp": 1620447051368,
    "data": 1673,
    "duration": 55
}
```

**Return value 401, only app admin has permission.**

``` json
{
   "desc": "unauthorized",
   "duration": 10,
   "timestamp": 1616573382270
}
```

**Returns a value of 500, which is usually a mysql error.**
**If the problem keep existing, please contact our technical support team.**

``` json
{
    "desc": "org.hibernate.exception.JDBCConnectionException: Unable to acquire JDBC Connection",
    "duration": 10,
    "timestamp": 1616573382270
}
```

If the return result is <font color='red'> 429, 503 </font> or other <font color='red'> 5xx </font>,It may mean that the interface has been restricted, please pause slightly and retry.For more information, see [Interface current limit description](/server_rest_interface_flow_limiting_instructions.html)

[Online testing using the Platform API](http://api-docs.easemob.com/)

------------------------------------------------------------------------

### Delete User Metadata

Deletes all user metadata for the specified user.
If the specified user does not exist, or if its user metadata do not exist (perhaps they have already been deleted), the deletion is also considered successful.

#### HTTP Request

<table border="1" cellspacing="0" bordercolor="#000000">
  <tr>
    <th>DELETE</th>
    <th>/{org_name}/{app_name}/metadata/user/{username}</th>
  </tr>
</table>

#### Request        Headers

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
    <td>Bearer ${token}</td>
  </tr>
</table>

#### Response Body

See the information contained in the data field in the return value, true means the deletion was successful

#### Example request

``` php
curl -X DELETE -H 'Content-Type: application/json' -H 'Authorization: Bearer YWMte3bGuOukEeiTkNP4grL7iwAAAAAAAAAAAAAAAAAAAAGL4CTw6XgR6LaXXVmNX4QCAgMAAAFnKdc-ZgBPGgBFTrLhhyK8woMEI005emtrLJFJV6aoxsZSioSIZkr5kw' 'http://a1.easecdn.com/chat-demo/testapp/metadata/user/user1'
```

#### Examples of possible results returned

**A return value of 200 and a data of true means that the deletion of the user attribute was successful, note that the deletion of a non-existent user is also considered successful.**

``` json
{
    "timestamp": 1620447526237,
    "data": true,
    "duration": 160
}
```

**Returns value 401, only the user himself or the app administrator has the right to delete his user metadata.**

``` json
{
   "desc": "unauthorized",
   "duration": 10,
   "timestamp": 1616573382270
}
```

**Returns a value of 500, which is usually a mysql error.**
**If the problem keep existing, please contact our technical support team.**

``` json
{
    "desc": "org.hibernate.exception.JDBCConnectionException: Unable to acquire JDBC Connection",
    "duration": 10,
    "timestamp": 1616573382270
}
```

If the return result is <font color='red'> 429, 503 </font> or other <font color='red'> 5xx </font>,It may mean that the interface has been restricted, please pause slightly and retry.For more information, see [Interface current limit description](/server_rest_interface_flow_limiting_instructions.html)

[Online testing using the Platform API](http://api-docs.easemob.com/)

