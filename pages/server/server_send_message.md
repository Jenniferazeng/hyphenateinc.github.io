---
title: Send a message
keywords: server
sidebar: server_sidebar
toc: true
permalink: server_send_message.html
folder: server
---

Chat Related information
The API can send text messages, send image messages, send audio messages, send video messages, send signal messages and send extended messages.

To send a file type message, you need to upload the file to the Chat server first, refer to the documentation: [file upload and download](/server/server_upload_and_download_files.html)

Platform API interface to send messages, will not determine whether the Chat id exists under appkey.

## Process description

 <table border="1" cellspacing="0" bordercolor="#000000">
  <tr>
    <th>Message type</th>
    <th>Description</th>
  </tr>
  <tr>
    <td>Send text/transparent</td>
    <td>directly edit message content  to send</td>
  </tr>
  <tr>
    <td>Send image/audio/video message</td>
    <td>You need to upload these three types of files first, get the corresponding parameters from the return value of the interface, edit them in the message body according to the API requirements and then send</td>
  </tr>
</table>

## Sending text messages

Sends a message to one or more users, or one or more groups. With the optional "from" field, the receiver can see that who the sender is. Also, extension fields are supported, and with the ext attribute, the APP can send its own message structure.

**Note**: In the calling procedure, a request body that exceeds 5kb will result in a 413 error, and needs to be split into several smaller requests for retries, while the length of the user message + extension field is within 4k bytes. See [interface stream limit description](/server_rest_interface_flow_limiting_instructions.html) for details.

#### HTTP Request

<table border="1" cellspacing="0" bordercolor="#000000">
  <tr>
    <th>POST</th>
    <th>/{org_name}/{app_name}/messages</th>
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
    <td>Bearer ${token}</td>
  </tr>
</table>

#### Request Body

<table border="1" cellspacing="0" bordercolor="#000000">
  <tr>
    <th>Parameter</th>
    <th>Description</th>
  </tr>
  <tr>
    <td>target_type</td>
    <td>The type of target to send; users: to send messages to users, chatgroups: to send messages to groups, chatrooms: to send messages to chat rooms</td>
  </tr>
  <tr>
    <td>target</td>
    <td>The target to send; note that you need to use an array, the maximum number of users added to the array is 600 by default, even if there is only one user, use the array ['u1']; when sending to users, the array element is the user name, when sending to groups, the array element is grouppid</td>
  </tr>
  <tr>
    <td>msg</td>
    <td>Message content</td>
  </tr>
  <tr>
    <td>type</td>
    <td>Message type; txt: text message, img: picture message, loc: location message, audio: audio message, video: video message, file: file message</td>
  </tr>
  <tr>
    <td>from</td>
    <td>indicates the message sender; without this field Server will default to "from":"admin". There is a from field but the value of the null string (""), the request will fail</td>
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
    <td>The username of the recipient of the message</td>
  </tr>
  <tr>
    <td>success</td>
    <td>means the message was sent successfully</td>
  </tr>
</table>

#### Example request

``` php
curl -X POST -H 'Content-Type: application/json' -H 'Accept: application/json' -H 'Authorization: Bearer YWMtP5n9zvOQEei7KclxPqJTkgAAAAAAAAAAAAAAAAAAGL4CTw6XgR6LaXXVmNX4QCAgMAAAFnXcBpfQBPGgDC09w5IdrfqG_H8_F53VLVTG0_82GXyEF8ZdMCt9-UpQ ' -d '{"target_type": "users", "target": ["user2", "user3"], "msg": {"type": "txt", "msg": "testmessage"}, "from": "user1"}' 'http://a1.easecdn.com/chat-demo/testapp/messages'
```

**Examples of possible returned results**

The return value of 200 indicates that the message was sent successfully.

``` json
{
  "action": "post",
  "application": "8be024f0-e978-11e8-b697-5d598d5f8402",
  "path": "/messages",
  "uri": "https://a1.easecdn.com/chat-demo/testapp/messages",
  "data": {
    "user2": "success",
    "user3": "success"
  },
  "timestamp": 1543922150902,
  "duration": 1,
  "organization": "chat-demo",
  "applicationName": "testapp"
}
```

Return value 400, means massage structure error

``` json
{
  "error": "json_parse",
  "timestamp": 1543922465246,
  "duration": 0,
  "exception": "org.codehaus.jackson.JsonParseException",
  "error_description": "Unexpected character ('}' (code 125)): was expecting double-quote to start field name\n at [Source: java.io.BufferedInputStream@7ba7eef2; line: 11, column: 2]"
}
```

**Return value 401, means unauthorized [no token, token error, token expired]**

``` json
{
  "error": "auth_bad_access_token",
  "timestamp": 1543922590032,
  "duration": 0,
  "exception": "org.apache.usergrid.rest.exceptions.SecurityException",
  "error_description": "Unable to authenticate due to corrupt access token"
}
```

If the return result is <font color='red'> 429, 503 </font> or other <font color='red'> 5xx </font>, it may mean that the interface is flow-limited, please pause for a while and retry. See [interface flow restriction instructions](/server_rest_interface_flow_limiting_instructions.html) for details 

[Testing online with Platform API](http://api-docs.easemob.com/)

------------------------------------------------------------------------

## Sending image messages

Sends a message to one or more users, or one or more groups. With the optional "from" field, the receiver can see that who the sender is. Also, extension fields are supported, and with the ext attribute, the APP can send its own message structure.

**Note**: In the calling procedure, a request body that exceeds 5kb will result in a 413 error, and needs to be split into several smaller requests for retries. See [interface stream limit description](/server_rest_interface_flow_limiting_instructions.html) for details.

#### HTTP Request

<table border="1" cellspacing="0" bordercolor="#000000">
  <tr>
    <th>POST</th>
    <th>/{org_name}/{app_name}/messages</th>
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
    <td>Bearer ${token}</td>
  </tr>
</table>

#### Request Body

<table border="1" cellspacing="0" bordercolor="#000000">
  <tr>
    <th>Parameter</th>
    <th>Description</th>
  </tr>
  <tr>
    <td>target_type</td>
    <td>The type of target to send; users: to send messages to users, chatgroups: to send messages to groups, chatrooms: to send messages to chat rooms</td>
  </tr>
  <tr>
    <td>target</td>
    <td>The target to send; note that you need to use an array, the maximum number of users added to the array is 600 by default, even if there is only one user, use the array ['u1']; when sending to users, the array element is the user name, when sending to groups, the array element is grouppid</td>
  </tr>
  <tr>
    <td>msg</td>
    <td>Message content</td>
  </tr>
  <tr>
    <td>type</td>
    <td>Message type; txt: text message, img: picture message, loc: location message, audio: audio message, video: video message, file: file message</td>
  </tr>
  <tr>
    <td>url</td>
    <td>domain/orgname/appname/chatfiles/UUUID returned from a successful file upload. reference request example</td>
  </tr>
  <tr>
    <td>filename</td>
    <td>Image name</td>
  </tr>
  <tr>
    <td>secret</td>
    <td>The secret returned after a successful file upload</td>
  </tr>
  <tr>
    <td>size</td>
    <td>the size of the image; height: height, width: width</td>
  </tr>
  <tr>
    <td>from</td>
    <td>indicates the message sender; without this field Server will default to "from":"admin". There is a from field but the value of the null string (""), the request will fail</td>
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
    <td>The username of the recipient of the message</td>
  </tr>
  <tr>
    <td>success</td>
    <td>means the message was sent successfully</td>
  </tr>
</table>

#### Request example

``` php
curl -X POST -i 'https://a1.easecdn.com/chat-demo/testapp/messages'   -H 'Authorization: Bearer YWMtsFVigGSuEeSTc7k5183Z5QAAAUqzeFx_9IjRch-ZxNbIlBIvx_4GWvzheSU'  -d '{"target_type":"users","target":["user2"],"from":"user1","msg":{"type":"img","filename":"testimg.jpg","secret":"VfEpSmSvEeS7yU8dwa9rAQc-DIL2HhmpujTNfSTsrDt6eNb_","url":"https://a1.easecdn.com/chat-demo/testapp/chatfiles/55f12940-64af-11e4-8a5b-ff2336f03252","size":{"width":480,"height":720}}}'
```

#### **Examples of possible returned results**

**returns a value of 200, indicating that the message was sent successfully**

``` json
{
  "action" : "post",
  "application" : "4d7e4ba0-dc4a-11e3-90d5-e1ffbaacdaf5",
  "uri" : "https://a1.easecdn.com/chat-demo/testapp/messages",
  "entities" : [ ],
  "data" : {
     "user2" : "success"
  },
  "timestamp" : 1415166497129,
  "duration" : 12,
  "organization" : "chat-demo",
  "applicationName" : "testapp"
}
```

**returns 400, indicating a massage structure error**

``` json
{
  "error": "json_parse",
  "timestamp": 1543922465246,
  "duration": 0,
  "exception": "org.codehaus.jackson.JsonParseException",
  "error_description": "Unexpected character ('}' (code 125)): was expecting double-quote to start field name\n at [Source: java.io.BufferedInputStream@7ba7eef2; line: 11, column: 2]"
}
```

**Return value 401, means unauthorized [no token, token error, token expired]**

``` json
{
  "error": "auth_bad_access_token",
  "timestamp": 1543922590032,
  "duration": 0,
  "exception": "org.apache.usergrid.rest.exceptions.SecurityException",
  "error_description": "Unable to authenticate due to corrupt access token"
}
```

If the return result is <font color='red'> 429, 503 </font> or other <font color='red'> 5xx </font>, it may mean that the interface is flow-limited, please pause for a while and retry. See [interface flow restriction instructions](/server_rest_interface_flow_limiting_instructions.html) for details

[Test online using Platform API](http://api-docs.easemob.com/)

------------------------------------------------------------------------

## Sending a audio message

To send a audio file, you need to upload the audio file first, and then send this message. (The UUID in the URL and the secret can be get from the response after uploading)

**Note**: In the calling procedure, if the request body exceeds 5kb, it will result in a 413 error and request needs to be split into several smaller requests to retry. See [interface flow limit description](/server_rest_interface_flow_limiting_instructions.html) for details.

#### HTTP Request

<table border="1" cellspacing="0" bordercolor="#000000">
  <tr>
    <th>POST</th>
    <th>/{org_name}/{app_name}/messages</th>
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
    <td>Bearer ${token}</td>
  </tr>
</table>

#### Request Body

<table border="1" cellspacing="0" bordercolor="#000000">
  <tr>
    <th>Parameter</th>
    <th>Description</th>
  </tr>
  <tr>
    <td>target_type</td>
    <td>The type of target to send; users: to send messages to users, chatgroups: to send messages to groups, chatrooms: to send messages to chat rooms</td>
  </tr>
  <tr>
    <td>target</td>
    <td>The target to send; note that you need to use an array, the maximum number of users added to the array is 600 by default, even if there is only one user, use the array ['u1']; when sending to users, the array element is the user name, when sending to groups, the array element is grouppid</td>
  </tr>
  <tr>
    <td>msg</td>
    <td>Message content</td>
  </tr>
  <tr>
    <td>type</td>
    <td>Message type; txt: text message, img: picture message, loc: location message, audio: audio message, video: video message, file: file message</td>
  </tr>
  <tr>
    <td>url</td>
    <td>The UUID returned by the successful file upload</td>
  </tr>
  <tr>
    <td>filename</td>
    <td>The name of the audio</td>
  </tr>
  <tr>
    <td>secret</td>
    <td>The secret returned after a successful file upload</td>
  </tr>
  <tr>
    <td>length</td>
    <td>audio time (in seconds)</td>
  </tr>
  <tr>
    <td>from</td>
    <td>indicates the message sender; without this field Server will default to "from":"admin". There is a from field but the value of the null string (""), the request will fail</td>
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
    <td>The username of the recipient of the message</td>
  </tr>
  <tr>
    <td>success</td>
    <td>means the message was sent successfully</td>
  </tr>
</table>

#### Request Example

``` php
curl -X POST -H "Authorization: Bearer YWMtxc6K0L1aEeKf9LWFzT9xEAAAAT7MNR_9OcNq-GwPsKwj_TruuxZfFSC2eIQ" "https://a1.easecdn.com/chat-demo/testapp/messages" -d '{"target_type" : "users","target" : ["user2", "user3"],"msg" : {"type": "audio","url": "https://a1.easecdn.com/chat-demo/testapp/chatfiles/1dfc7f50-55c6-11e4-8a07-7d75b8fb3d42","filename": "testaudio.amr","length": 10,"secret": "Hfx_WlXGEeSdDW-SuX2EaZcXDC7ZEig3OgKZye9IzKOwoCjM"},"from" : "user1" }'
```

#### Examples of possible returned results

**returns a value of 200, indicating that the message was sent successfully**

``` json
{
  "action": "post",
  "application": "8be024f0-e978-11e8-b697-5d598d5f8402",
  "path": "/messages",
  "uri": "https://a1.easecdn.com/chat-demo/testapp/messages",
  "data": {
    "user2": "success",
    "user3": "success"
  },
  "timestamp": 1543922150902,
  "duration": 1,
  "organization": "chat-demo",
  "applicationName": "testapp"
}
```

**returns 400, indicating a massage structure error**

``` json
{
  "error": "json_parse",
  "timestamp": 1543922465246,
  "duration": 0,
  "exception": "org.codehaus.jackson.JsonParseException",
  "error_description": "Unexpected character ('}' (code 125)): was expecting double-quote to start field name\n at [Source: java.io.BufferedInputStream@7ba7eef2; line: 11, column: 2]"
}
```

##### Return value 401, means unauthorized [no token, token error, token expired]

``` json
{
  "error": "auth_bad_access_token",
  "timestamp": 1543922590032,
  "duration": 0,
  "exception": "org.apache.usergrid.rest.exceptions.SecurityException",
  "error_description": "Unable to authenticate due to corrupt access token"
}
```

If the return result is <font color='red'> 429, 503 </font> or other <font color='red'> 5xx </font>, it may mean that the interface is flow-limited, please pause for a while and retry. See [interface flow restriction instructions](/server_rest_interface_flow_limiting_instructions.html) 

[Testing online with Platform API](http://api-docs.easemob.com/)

------------------------------------------------------------------------

## Sending a video message

To send a video message, you need to upload a video file and a video thumbnail file first, then send this message. (The UUID and secret in the URL can be get from the response after uploading)

**Note**: In the calling procedure, a request body that exceeds 5kb will result in a 413 error which will need to be split into several smaller requests to retry. See [interface flow limit description](/server_rest_interface_flow_limiting_instructions.html) for details.

#### HTTP Request

<table border="1" cellspacing="0" bordercolor="#000000">
  <tr>
    <th>POST</th>
    <th>/{org_name}/{app_name}/messages</th>
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
    <td>Bearer ${token}</td>
  </tr>
</table>

#### Request Body

<table border="1" cellspacing="0" bordercolor="#000000">
  <tr>
    <th>Parameter</th>
    <th>Description</th>
  </tr>
  <tr>
    <td>target_type</td>
    <td>The type of target to send; users: to send messages to users, chatgroups: to send messages to groups, chatrooms: to send messages to chat rooms</td>
  </tr>
  <tr>
    <td>target</td>
    <td>The target to send; note that you need to use an array, the maximum number of users added to the array is 600 by default, even if there is only one user, use the array ['u1']; when sending to users, the array element is the user name, when sending to groups, the array element is grouppid</td>
  </tr>
  <tr>
    <td>msg</td>
    <td>Message content</td>
  </tr>
  <tr>
    <td>type</td>
    <td>Message type; txt: text message, img: picture message, loc: location message, audio: audio message, video: video message, file: file message</td>
  </tr>
  <tr>
    <td>filename</td>
    <td>the name of the video file</td>
  </tr>
  <tr>
    <td>secret</td>
    <td>The secret returned after successfully uploading the video file</td>
  </tr>
  <tr>
    <td>length</td>
    <td>The length of the video playback</td>
  </tr>
  <tr>
    <td>thumb_secret</td>
    <td>The secret returned after successfully uploading a video thumbnail</td>
  </tr>
    <tr>
    <td>url</td>
    <td>UUID returned after successful upload of video file</td>
  </tr>
  <tr>
    <td>from</td>
    <td>indicates the sender of the message; without this field Server will default to "from":"admin". The request fails when there is a from field but the value is an empty string ("")</td>
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
    <td>The username of the recipient of the message</td>
  </tr>
  <tr>
    <td>success</td>
    <td>means the message was sent successfully</td>
  </tr>
</table>

#### Request example

``` php
curl -X POST -i 'https://a1.easecdn.com/chat-demo/testapp/messages' -H 'Authorization: Bearer YWMtxc6K0L1aEeKf9LWFzT9xEAAAAT7MNR_9OcNq-GwPsKwj_TruuxZfFSC2eIQ'  -d '{"target_type":"users","target":["user2","user3"],"from":"user1","msg":{"type":"video","filename" : "testvideo.mp4","thumb" : "https://a1.easecdn.com/chat-demo/testapp/chatfiles/67279b20-7f69-11e4-8eee-21d3334b3a97","length" : 0,"secret":"VfEpSmSvEeS7yU8dwa9rAQc-DIL2HhmpujTNfSTsrDt6eNb_","file_length" : 58103,"thumb_secret" : "ZyebKn9pEeSSfY03ROk7ND24zUf74s7HpPN1oMV-1JxN2O2I","url" : "https://a1.easecdn.com/chat-demo/testapp/chatfiles/671dfe30-7f69-11e4-ba67-8fef0d502f46"}}'
```

#### Examples of possible returned results

**returns a value of 200, indicating that the message was sent successfully**

``` json
{
  "action" : "post",
  "application" : "4d7e4ba0-dc4a-11e3-90d5-e1ffbaacdaf5",
  "uri" : "https://a1.easecdn.com/chat-demo/testapp/messages",
  "entities" : [ ],
  "data" : {
    "user2" : "success",
    "user3" : "success"
  },
  "timestamp" : 1415166234863,
  "duration" : 5,
  "organization" : "chat-demo",
  "applicationName" : "testapp"
}
```

**returns 400, indicating a massage structure error**

``` json
{
  "error": "json_parse",
  "timestamp": 1543922465246,
  "duration": 0,
  "exception": "org.codehaus.jackson.JsonParseException",
  "error_description": "Unexpected character ('}' (code 125)): was expecting double-quote to start field name\n at [Source: java.io.BufferedInputStream@7ba7eef2; line: 11, column: 2]"
}
```

**return value 401, means unauthorized [no token, token error, token expired]**

``` json
{
  "error": "auth_bad_access_token",
  "timestamp": 1543922590032,
  "duration": 0,
  "exception": "org.apache.usergrid.rest.exceptions.SecurityException",
  "error_description": "Unable to authenticate due to corrupt access token"
}
```

If the return result is <font color='red'> 429, 503 </font> or other <font color='red'> 5xx </font>, it may mean that the interface is flow-limited, please pause for a while and retry. See [interface flow restriction instructions](/server_rest_interface_flow_limiting_instructions.html) for details

[Test online using Platform API](http://api-docs.easemob.com/)

------------------------------------------------------------------------

## Send location message

Location message: get the latitude and longitude of the address, fill in the correct address to send.

**Note**: In the calling procedure, a request body that exceeds 5kb will lead to 413 error, need to split into several smaller requests to retry, while the length of user message + extended field is within 4k bytes. See [interface flow limit description](/server_rest_interface_flow_limiting_instructions.html) for details.

#### HTTP Request

<table border="1" cellspacing="0" bordercolor="#000000">
  <tr>
    <th>POST</th>
    <th>/{org_name}/{app_name}/messages</th>
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
    <td>Bearer ${token}</td>
  </tr>
</table>

#### Request Body

<table border="1" cellspacing="0" bordercolor="#000000">
  <tr>
    <th>Parameter</th>
    <th>Description</th>
  </tr>
  <tr>
    <td>target_type</td>
    <td>The type of target to send; users: to send messages to users, chatgroups: to send messages to groups, chatrooms: to send messages to chat rooms</td>
  </tr>
  <tr>
    <td>target</td>
    <td>The target to send; note that you need to use an array, the maximum number of users added to the array is 600 by default, even if there is only one user, use the array ['u1']; when sending to users, the array element is the user name, when sending to groups, the array element is grouppid</td>
  </tr>
  <tr>
    <td>msg</td>
    <td>Message content</td>
  </tr>
  <tr>
    <td>type</td>
    <td>Message type; txt: text message, img: picture message, loc: location message, audio: audio message, video: video message, file: file message</td>
  </tr>
  <tr>
    <td>from</td>
    <td>means the message sender; without this field Server will default to "from":"admin". The request fails when there is a from field but the value is an empty string ("")</td>
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
    <td>The username of the recipient of the message</td>
  </tr>
  <tr>
    <td>success</td>
    <td>means the message was sent successfully</td>
  </tr>
</table>

#### Request example

``` php
curl -X POST -H 'Content-Type: application/json' -H 'Accept: application/json' -H 'Authorization: Bearer YWMtP5n9zvOQEei7KclxPqJTkgAAAAAAAAAAAAAAAAAAAAGL4CTw6XgR6LaXXVmNX4QCAgMAAAFnXcBpfQBPGgDC09w5IdrfqG_H8_F53VLVTG0_82GXyEF8ZdMCt9-UpQ' -d '{"target_type": "users","target": ["user2"],"msg": {"type": "loc","lat": "39.966","lng":"116.322",."addr":"中国北京市海淀区中关村"},"from": "user1"}' 'http://a1.easecdn.com/chat-demo/testapp/messages'
```

#### Examples of possible returned results

**returns a value of 200, indicating that the message was sent successfully**

``` json
{
  "action": "post",
  "application": "8be024f0-e978-11e8-b697-5d598d5f8402",
  "path": "/messages",
  "uri": "https://a1.easecdn.com/chat-demo/testapp/messages",
  "data": {
    "user2": "success"
  },
  "timestamp": 1543922150902,
  "duration": 1,
  "organization": "chat-demo",
  "applicationName": "testapp"
}
```

**returns 400, indicating a massage structure error**

``` json
{
  "error": "json_parse",
  "timestamp": 1543922465246,
  "duration": 0,
  "exception": "org.codehaus.jackson.JsonParseException",
  "error_description": "Unexpected character ('}' (code 125)): was expecting double-quote to start field name\n at [Source: java.io.BufferedInputStream@7ba7eef2; line: 11, column: 2]"
}
```

**returns 400, indicating a massage structure error**

``` json
{
  "error": "auth_bad_access_token",
  "timestamp": 1543922590032,
  "duration": 0,
  "exception": "org.apache.usergrid.rest.exceptions.SecurityException",
  "error_description": "Unable to authenticate due to corrupt access token"
}
```

If the return result is <font color='red'> 429, 503 </font> or other <font color='red'> 5xx </font>, it may mean that the interface is flow-limited, please pause for a while and retry. See [interface flow restriction instructions](/server_rest_interface_flow_limiting_instructions.html) for details

[Testing online with Platform API](http://api-docs.easemob.com/)

------------------------------------------------------------------------

## Sending signal messages

Signal messages: no client-side alerts (ring, vibrate, notification bar, etc.) and no APNS push (Apple push), but can be listened to on the client side, the specific function can be customized according to your own.

**Note**: In the calling procedure, if the request body exceeds 5kb will result in a 413 error and needs to be split into several smaller requests to retry. See [interface flow limit description](/server_rest_interface_flow_limiting_instructions.html)。

#### HTTP Request

<table border="1" cellspacing="0" bordercolor="#000000">
  <tr>
    <th>POST</th>
    <th>/{org_name}/{app_name}/messages</th>
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
    <td>Bearer ${token}</td>
  </tr>
</table>

#### Request Body

<table border="1" cellspacing="0" bordercolor="#000000">
  <tr>
    <th>Parameter</th>
    <th>Description</th>
  </tr>
  <tr>
    <td>target_type</td>
    <td>The type of target to send; users: to send messages to users, chatgroups: to send messages to groups, chatrooms: to send messages to chat rooms</td>
  </tr>
  <tr>
    <td>target</td>
    <td>The target to send; note that you need to use an array, the maximum number of users added to the array is 600 by default, even if there is only one user, use the array ['u1']; when sending to users, the array element is the user name, when sending to groups, the array element is grouppid</td>
  </tr>
  <tr>
    <td>msg</td>
    <td>Message content</td>
  </tr>
  <tr>
    <td>type</td>
    <td>Message type; txt: text message, img: picture message, loc: location message, audio: audio message, video: video message, file: file message, cmd: signal message</td>
  </tr>
  <tr>
    <td>from</td>
    <td>indicates the message sender; without this field Server will default to "from":"admin", The request fails when there is a from field but the value is an empty string ("")</td>
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
    <td>The username of the recipient of the message</td>
  </tr>
  <tr>
    <td>success</td>
    <td>means the message was sent successfully</td>
  </tr>
</table>

#### Request example

``` php
curl -X POST -H "Authorization:Bearer YWMtxc6K0L1aEeKf9LWFzT9xEAAAAT7MNR_9OcNq-GwPsKwj_TruuxZfFSC2eIQ" -i "https://a1.easecdn.com/chat-demo/testapp/messages" -d '{"target_type":"users","target":["user2","user3"],"msg":{"type":"cmd","action":"action1"},"from":"user1"}'
```

#### Examples of possible returned results

**returns a value of 200, indicating that the message was sent successfully**

``` json
{
  "action" : "post",
  "application" : "4d7e4ba0-dc4a-11e3-90d5-e1ffbaacdaf5",
  "uri" : "https://a1.easecdn.com/chat-demo/testapp/messages",
  "entities" : [ ],
  "data" : {
    "user2" : "success",
    "user3" : "success"
  },
  "timestamp" : 1415167842297,
  "duration" : 4,
  "organization" : "chat-demo",
  "applicationName" : "testapp"
}
```

**returns 400, indicating a massage structure error**

``` json
{
  "error": "json_parse",
  "timestamp": 1543922465246,
  "duration": 0,
  "exception": "org.codehaus.jackson.JsonParseException",
  "error_description": "Unexpected character ('}' (code 125)): was expecting double-quote to start field name\n at [Source: java.io.BufferedInputStream@7ba7eef2; line: 11, column: 2]"
}
```

**return value 401, means unauthorized [no token, token error, token expired]**

``` json
{
  "error": "auth_bad_access_token",
  "timestamp": 1543922590032,
  "duration": 0,
  "exception": "org.apache.usergrid.rest.exceptions.SecurityException",
  "error_description": "Unable to authenticate due to corrupt access token"
}
```

If the return result is <font color='red'> 429, 503 </font> or other <font color='red'> 5xx </font>, it may mean that the interface is flow-limited, please pause for a while and retry. See [interface flow restriction instructions](/server_rest_interface_flow_limiting_instructions.html) for details

[Testing online with Platform API](http://api-docs.easemob.com/)

------------------------------------------------------------------------

## Sending custom messages

Custom message: If the normal message type does not meet the user's message needs, you can use a custom message to customize the message type, mainly through the customEvent field of message.

**Note**: In the calling procedure, if the request body exceeds 5kb will result in a 413 error and will need to be split into several smaller requests to retry. See [interface flow limit description](/server_rest_interface_flow_limiting_instructions.html) for details.

#### HTTP Request

<table border="1" cellspacing="0" bordercolor="#000000">
  <tr>
    <th>POST</th>
    <th>/{org_name}/{app_name}/messages</th>
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
    <td>Bearer ${token}</td>
  </tr>
</table>

#### Request Body

<table border="1" cellspacing="0" bordercolor="#000000">
  <tr>
    <th>Parameter</th>
    <th>Description</th>
  </tr>
  <tr>
    <td>target_type</td>
    <td>The type of target to send; users: to send messages to users, chatgroups: to send messages to groups, chatrooms: to send messages to chat rooms</td>
  </tr>
  <tr>
    <td>target</td>
    <td>The target to send; note that you need to use an array, the maximum number of users added to the array is 600 by default, even if there is only one user, use the array ['u1']; when sending to users, the array element is the user name, when sending to groups, the array element is grouppid</td>
  </tr>
  <tr>
    <td>msg</td>
    <td>Message content</td>
  </tr>
  <tr>
    <td>type</td>
    <td>Message type; txt: text message, img: image message, loc: location message, audio: audio message, video: video message, file: file message, custom: custom message</td>
  </tr>
  <tr>
    <td>customEvent</td>
    <td>user custom event type, must be string, value must satisfy the regular expression  [a-zA-Z0-9-\_/\. ]{1,32}, shortest 1 character longest 32 characters</td>
  </tr>
  <tr>
    <td>customExts</td>
    <td>user custom event attribute, type must be Map\<String,String>, can contain up to 16 elements. customExts is optional and can be left out if not needed</td>
  </tr>
  <tr>
    <td>from</td>
    <td>indicates the message sender; without this field Server will default to "from":"admin", The request fails when there is a from field but the value is an empty string ("")</td>
  </tr>
  <tr>
    <td>ext</td>
    <td>Extended attribute, defined by APP itself. It is fine that there is not this field, but if there is, the value can not be "ext:null" this form, otherwise the error</td>
  </tr>
  <tr>
    <td>attr1</td>
    <td>The extended content of the message, you can add fields to extend the main parsing part of the message. It must be basic type data</td>
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
    <td>The username of the recipient of the message</td>
  </tr>
  <tr>
    <td>success</td>
    <td>means the message was sent successfully</td>
  </tr>
</table>

#### Request example

``` php
curl -L -X POST 'https://a1.easecdn.com/chat-demo/testapp/messages' -H 'Accept: application/json' -H 'Content-Type: application/json' -H 'Authorization: Bearer ${token}' -d '{ "target_type":"users", "target":["user2"], "msg":{"type":"custom", "customEvent":"gift_1", "customExts":{"name":"flower","size":"16","price":"100"}}, "from":"user1","ext":{"attr1":"test"}}'
```

#### Examples of possible returned results

**returns a value of 200, indicating that the message was sent successfully**

``` json
{
    "path": "/messages",
    "uri": "https://a1.easecdn.com/chat-demo/testapp/messages",
    "timestamp": 1597292981767,
    "organization": "chat-demo",
    "application": "e82bcc5f-3366-4d96-a7c1-92de917ea2b0",
    "action": "post",
    "data": {
        "user2": "success"
    },
    "duration": 0,
    "applicationName": "testapp"
}
```

**returns 400, indicating a massage structure error**

``` json
{
    "error": "illegal_argument",
    "exception": "java.lang.IllegalArgumentException",
    "timestamp": 1597292940854,
    "duration": 1,
    "error_description": "target_type must be provided"
}
```

**return value 401, means unauthorized [no token, token error, token expired]**

``` json
{
    "error": "unauthorized",
    "exception": "ChatSecurityException",
    "timestamp": 1597292804746,
    "duration": 1,
    "error_description": "Unable to authenticate (OAuth)"
}
```

If the return result is <font color='red'> 429, 503 </font> or other <font color='red'> 5xx </font>, it may mean that the interface is flow-limited, please pause for a while and retry. See [interface flow restriction instructions](/server_rest_interface_flow_limiting_instructions.html) for details

[Testing online with Platform API](http://api-docs.easemob.com/)

------------------------------------------------------------------------

## Sending extended messages

Extended messages: If the normal message type does not meet the user's messaging needs, extended messages can be used. Extensions are supported for any type of message, mainly through the ext field of message.

**Note**: In the calling procedure, a request body that exceeds 5kb will result in a 413 error and will need to be split into several smaller requests to retry. See [interface flow limit description](/server_rest_interface_flow_limiting_instructions.html) for details.

#### HTTP Request

<table border="1" cellspacing="0" bordercolor="#000000">
  <tr>
    <th>POST</th>
    <th>/{org_name}/{app_name}/messages</th>
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
    <td>Bearer ${token}</td>
  </tr>
</table>

#### Request Body

<table border="1" cellspacing="0" bordercolor="#000000">
  <tr>
    <th>Parameter</th>
    <th>Description</th>
  </tr>
  <tr>
    <td>target_type</td>
    <td>The type of target to send; users: to send messages to users, chatgroups: to send messages to groups, chatrooms: to send messages to chat rooms</td>
  </tr>
  <tr>
    <td>target</td>
    <td>The target to send; note that you need to use an array, the maximum number of users added to the array is 600 by default, even if there is only one user, use the array ['u1']; when sending to users, the array element is the user name, when sending to groups, the array element is grouppid</td>
  </tr>
  <tr>
    <td>msg</td>
    <td>Message content</td>
  </tr>
  <tr>
    <td>type</td>
    <td>Message type, not limited to text messages. Any message type can be extended; txt: text message, img: image message, loc: location message, audio: audio message, video: video message, file: file message</td>
  </tr>
  <tr>
    <td>from</td>
    <td>indicates the message sender; without this field Server will default to "from":"admin", The request fails when there is a from field but the value is an empty string ("")</td>
  </tr>
  <tr>
    <td>ext</td>
    <td>Extended attribute, defined by APP itself.  It is fine that there is not this field, but if there is, the value can not be \"ext:null\"\this form. Key value type must be NSString, Value value type must be NSString or NSNumber type BOOL, int, unsigned in, long long, double</td>
  </tr>
  <tr>
    <td>attr1</td>
    <td>The extended content of the message, you can add fields to extend the main parsing part of the message. It must be basic type data</td>
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
    <td>The username of the recipient of the message</td>
  </tr>
  <tr>
    <td>success</td>
    <td>means the message was sent successfully</td>
  </tr>
</table>

#### Request Example

``` php
curl -X POST -H "Authorization:Bearer YWMtxc6K0L1aEeKf9LWFzT9xEAAAAT7MNR_9OcNq-GwPsKwj_TruuxZfFSC2eIQ" -i "https://a1.easecdn.com/chat-demo/testapp/messages" -d '{"target_type":"users","target":["user2","user3"],"msg":{"type":"txt","msg":"testmessage"},"from":"user1","ext":{"attr1":"test"}}'
```

#### Examples of possible returned results

**returns a value of 200, indicating that the message was sent successfully**

``` json
{
  "action" : "post",
  "application" : "4d7e4ba0-dc4a-11e3-90d5-e1ffbaacdaf5",
  "uri" : "https://a1.easecdn.com/chat-demo/testapp/messages",
  "entities" : [ ],
  "data" : {
    "user2" : "success",
    "user3" : "success"
  },
  "timestamp" : 1415167842297,
  "duration" : 4,
  "organization" : "chat-demo",
  "applicationName" : "testapp"
}
```

**returns 400, indicating a massage structure error**

``` json
{
  "error": "json_parse",
  "timestamp": 1543922465246,
  "duration": 0,
  "exception": "org.codehaus.jackson.JsonParseException",
  "error_description": "Unexpected character ('}' (code 125)): was expecting double-quote to start field name\n at [Source: java.io.BufferedInputStream@7ba7eef2; line: 11, column: 2]"
}
```

**return value 401, means unauthorized [no token, token error, token expired]**

``` json
{
  "error": "auth_bad_access_token",
  "timestamp": 1543922590032,
  "duration": 0,
  "exception": "org.apache.usergrid.rest.exceptions.SecurityException",
  "error_description": "Unable to authenticate due to corrupt access token"
}
```

If the return result is <font color='red'> 429, 503 </font> or other <font color='red'> 5xx </font>, it may mean that the interface is flow-limited, please pause for a while and retry. See [interface flow restriction instructions](/server_rest_interface_flow_limiting_instructions.html) for details

[Testing online with Platform API](http://api-docs.easemob.com/)

### iOS extension messages

Chat provides the following types of extension fields.

<table border="1" cellspacing="0" bordercolor="#000000">
  <tr>
    <th>Extension Field</th>
    <th>Description</th>
  </tr>
  <tr>
    <td>em_push_content</td>
    <td><a href="/ios_push_content.html#custom-display"> custom push display </a></td>
  </tr>
  <tr>
    <td>em_push_category</td>
    <td><a href="/ios_push_content.html#add-category-field"> add category field to APNs Payload </a></td>
  </tr>
  <tr>
    <td>em_push_sound</td>
    <td><a href="/ios_push_content.html#custom-push-alert-sound"> customize push sound </a></td>
  </tr>
  <tr>
    <td>em_push_mutable_content</td>
    <td><a href="/ios_push_content.html#enable-APNs-alert-extension"> enable APNs notification extension </a></td>
  </tr>
  <tr>
    <td>em_ignore_notification</td>
    <td><a href="/ios_push_content.html#send-silent-message"> send silent message </a></td>
  </tr>
  <tr>
    <td>em_force_notification</td>
    <td><a href="/ios_push_content.html#set-up-forced-push-APNs"> set force push APNs </a></td>
  </tr>
</table>