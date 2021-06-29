---
title: Chat Log
keywords: server
sidebar: server_sidebar
toc: true
permalink: server_chat_log.html
folder: server
---

# Chat Log

------------------------------------------------------------------------

Easemob Info supports exporting chat logs via the REST interface.
If the export of chat logs is abnormal, it is possible that the interface has been restricted, so please pause a little and retry. See [interface flow restriction description](/im/450errorcode/45restastrict) for details.

##  Chat log data structure

The description of the main fields of the chat log data format can be found in the returned data structure example

<table border="1" cellspacing="0" bordercolor="#000000">
  <tr>
    <th>Parameter</th>
    <th>Description</th>
  </tr>
  <tr>
    <td>msg_id</td>
    <td>Message ID</td>
  </tr>
  <tr>
    <td>timestamp</td>
    <td>Message Delivery Time</td>
  </tr>
  <tr>
    <td>from</td>
    <td>Sender username</td>
  </tr>
  <tr>
    <td>to</td>
    <td>The username of the recipient or the ID of the receiving group</td>
  </tr>
  <tr>
    <td>chat_type</td>
    <td>Used to determine whether it is a single chat, group chat, or chatroom. chat: single chat; groupchat: group chat; chatroom: chatroom</td>
  </tr>
  <tr>
    <td>payload</td>
    <td>message bodies, different message types; message ext, custom extended attributes, etc.</td>
  </tr>
</table>  

Example of chat log return data format

``` json
{
    "msg_id": "5I02W-16-8278a", 
    "timestamp": 1403099033211, 
    "direction":"outgoing",
    "to": "1402541206787",
    "from": "zw123", 
    "chat_type": "chat", 
    "payload": {
        "bodies": [
          {
             //The different message types are explained below
          }
        ],
        "ext": {
            "key1": "value1", 
               ...
         },
         "from":"zw123",
         "to":"1402541206787"
    }
}
```

## Text type messages

The bodies parameter of the above message corresponds to the text type message parameter description

<table border="1" cellspacing="0" bordercolor="#000000">
  <tr>
    <th>Parameter</th>
    <th>Description</th>
  </tr>
  <tr>
    <td>msg</td>
    <td>Message content</td>
  </tr>
  <tr>
    <td>type</td>
    <td>"txt",text message type</td>
  </tr>
</table>  

Example of text type message format

``` json
"bodies": [ 
   {
       "msg":"welcome to easemob!",
       "type":"txt"
   }
]
```

## Image type messages

Corresponding to the bodies parameter of the above message, the description of the parameters of the picture type message

<table border="1" cellspacing="0" bordercolor="#000000">
  <tr>
    <th>Parameter</th>
    <th>Description</th>
  </tr>
  <tr>
    <td>file_length</td>
    <td>size of the image attachment (in bytes)</td>
  </tr>
  <tr>
    <td>filename</td>
    <td>"image.jpg",the name of the image with the image format</td>
  </tr>
  <tr>
    <td>secret</td>
    <td>A string is returned after uploading an image, only if it contains secret can the image be downloaded</td>
  </tr>
  <tr>
    <td>size</td>
    <td>the size of the image length and width</td>
  </tr>
  <tr>
    <td>type</td>
    <td>"img",the type of the image message</td>
  </tr>
  <tr>
    <td>url</td>
    <td>The address of the image upload message, which will return the UUID after successful upload</td>
  </tr>
</table>  

Example of image type message format

``` json
"bodies": [ 
   {
       "file_length":128827,
       "filename":"test1.jpg", 
       "secret":"DRGM8OZrEeO1vafuJSo2IjHBeKlIhDp0GCnFu54xOF3M6KLr", 
       "size":{"height":1325,"width":746},
       "type":"img",
        "url":"https://a1.easemob.com/easemob-demo/chatdemoui/chatfiles/65e54a4a-fd0b-11e3-b821-ebde7b50cc4b", 
   }
]
```

## Address location type messages

Corresponding to the bodies parameter of the above message, the geolocation type message parameter description

<table border="1" cellspacing="0" bordercolor="#000000">
  <tr>
    <th>Parameter</th>
    <th>Description</th>
  </tr>
  <tr>
    <td>addr</td>
    <td>"address" Description of the text content of the address to be sent</td>
  </tr>
  <tr>
    <td>lat</td>
    <td>latitude</td>
  </tr>
  <tr>
    <td>lng</td>
    <td>longitude</td>
  </tr>
  <tr>
    <td>type</td>
    <td>"loc",location message type</td>
  </tr>
</table>  

Example of geographic location type message format

``` json
"bodies": [
   {
       "addr":"Xibianmen Bridge, Xicheng District ",
       "lat":39.9053,
       "lng":116.36302, 
       "type":"loc"  
   }
]
```

## Voice type messages

Corresponding to the bodies parameter of the above message, the voice type message parameter description

<table border="1" cellspacing="0" bordercolor="#000000">
  <tr>
    <th>Parameter</th>
    <th>Description</th>
  </tr>
  <tr>
    <td>file_length</td>
    <td>Voice attachment size (in bytes)</td>
  </tr>
  <tr>
    <td>filename</td>
    <td>"audio.amr", voice file with file format</td>
  </tr>
  <tr>
    <td>secret</td>
    <td>will return a string after uploading the voice, only contains secret can download this voice file</td>
  </tr>
  <tr>
    <td>length</td>
    <td>voice time (in seconds)</td>
  </tr>
  <tr>
    <td>type</td>
    <td>"audio",voice message type</td>
  </tr>
  <tr>
    <td>url</td>
    <td>The address of the voice file to be uploaded, the UUID will be returned after successful upload</td>
  </tr>
</table>

Voice type message format example

``` json
"bodies": [ 
   {
       "file_length":6630,
       "filename":"test1.amr",
       "length":10, 
       "secret":"DRGM8OZrEeO1vafuJSo2IjHBeKlIhDp0GCnFu54xOF3M6KLr",
       "type":"audio",
        "url":"https://a1.easemob.com/easemob-demo/chatdemoui/chatfiles/0637e55a-f606-11e3-ba23-51f25fd1215b"
   }
]
```

## Video type message

Corresponding to the bodies parameter of the above message, the video type message parameter description

<table border="1" cellspacing="0" bordercolor="#000000">
  <tr>
    <th>Parameter</th>
    <th>Description</th>
  </tr>
  <tr>
    <td>file_length</td>
    <td>Video attachment size (in bytes)</td>
  </tr>
  <tr>
    <td>filename</td>
    <td>"video.mp4", video file with file format</td>
  </tr>
  <tr>
    <td>secret</td>
    <td>Will return a string after uploading the video, only contains secret can download this video file</td>
  </tr>
  <tr>
    <td>length</td>
    <td>The length of the video playback (in seconds)</td>
  </tr>
  <tr>
    <td>size</td>
    <td>Video thumbnail length and width size</td>
  </tr>
  <tr>
    <td>thumb</td>
    <td>The remote address of the video thumbnail that will be returned as a UUID after uploading the video thumbnail</td>
  </tr>
  <tr>
    <td>thumb_secret</td>
    <td>The string to be returned after uploading the video thumbnail, only if it contains a secret can the video thumbnail be downloaded</td>
  </tr>
  <tr>
    <td>type</td>
    <td>"video",video message type</td>
  </tr>
  <tr>
    <td>url</td>
    <td>The address of the uploaded video, the UUID will be returned after a successful upload</td>
  </tr>
</table>

Video type message format example

``` json
"bodies": [
   {
       "file_length": 58103,
       "filename": "1418105136313.mp4",
       "length": 10,
       "secret": "VfEpSmSvEeS7yU8dwa9rAQc-DIL2HhmpujTNfSTsrDt6eNb_",
       "size":{"height":480,"width":360},
       "thumb": "https://a1.easemob.com/easemob-demo/chatdemoui/chatfiles/67279b20-7f69-11e4-8eee-21d3334b3a97",
       "thumb_secret": "ZyebKn9pEeSSfY03ROk7ND24zUf74s7HpPN1oMV-1JxN2O2I",
       "type": "video",
        "url": "https://a1.easemob.com/easemob-demo/chatdemoui/chatfiles/671dfe30-7f69-11e4-ba67-8fef0d502f46"
   }
]
```

## File type messages

Corresponding to the bodies parameter of the above message, the description of the file type message parameters

<table border="1" cellspacing="0" bordercolor="#000000">
  <tr>
    <th>Parameter</th>
    <th>Description</th>
  </tr>
  <tr>
    <td>file_length</td>
    <td>File size (in bytes)</td>
  </tr>
  <tr>
    <td>filename</td>
    <td>"file.zip", file name with file format</td>
  </tr>
  <tr>
    <td>secret</td>
    <td>After uploading a file, a string is returned and only the file containing secret can be downloaded</td>
  </tr>
  <tr>
    <td>type</td>
    <td>"file",image message type</td>
  </tr>
  <tr>
    <td>url</td>
    <td>The address of the uploaded file, and the UUID will be returned after a successful upload</td>
  </tr>
</table>

Example of file type message format

``` json
"bodies": [ 
   {
       "file_length":3279,
       "filename":"record.md",
       "secret":"2RNXCgeeEee2caV-fSQ1btZXJH4cgr2admVXn560He2PD3RX",
       "type":"file",
       "url":"https://a1.easemob.com/sxqxwdong/mychatdemo/chatfiles/d9135700-079e-11e7-b000-a7039876610f"
   }
]
```

------------------------------------------------------------------------

# REST API

The export chat log interface is not a real-time interface, and there is a certain delay in getting success, so it cannot be used as a real-time pull message interface. The following APIs require enterprise administrator privileges to access.

Chat logs need to be exported using the REST API, which can be tested online by using the [Easemob REST API](http://api-docs.easemob.com/) embedded in the documentation for online testing.

<table border="1" cellspacing="0" bordercolor="#000000">
  <tr>
    <th>Name</th>
    <th>Request</th>
    <th>Description</th>
  </tr>
  <tr>
    <td>Get history message file</td>
    <td>/{org_name}/{app_name}/chatmessages/${time}</td>
    <td>A file that returns data based on the time range of the request, download to view</td>
  </tr>
</table>

##  Get the history message file

The REST API provided by Easemob requires permission to access it, which is reflected by sending HTTP requests with a token
The following describes how to get the token. Note: The API description uses {APP's client_id} and other parameters need to be replaced with specific values.

**Important reminder: **When getting token, the server will return the token expiration date, refer to the expires_in field. 

Due to network latency and other reasons, the system does not guarantee that the token If you find that the token is used abnormally, please get a new token, for example, "http response code" returns 401.
In addition, please do not send frequent requests to the server to obtain token In addition, please do not send requests for token to the server too often, the same account will be blocked if you send this request more often.

`This interface can only get one hour of history messages at a time.`

The client_id and client_secret can be seen in the [APP
details page ](http://www.google.com) in the administrative backend of Easemob .

#### HTTP Request

<table border="1" cellspacing="0" bordercolor="#000000">
  <tr>
    <th>GET</th>
    <th>/{org_name}/{app_name}/chatmessages/${time}</th>
  </tr>
</table>

You need to fill in the {time} corresponding to the time period you need to get in the request.

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

#### Response Body

View the information contained in the data field in the return value

<table border="1" cellspacing="0" bordercolor="#000000">
  <tr>
    <th>Parameter</th>
    <th>Description</th>
  </tr>
  <tr>
    <td>url</td>
    <td>The address to download the chat log file</td>
  </tr>
</table>

#### Request Example

``` php
curl -X GET -H 'Accept: application/json' -H 'Authorization: Bearer YWMtVVK6cPFqEeif2wnxtHDB7AAAAAAAAAAAAAAAAAAAAAGL4CTw6XgR6LaXXVmNX4QCAgMAAAFnT6x89wBPGgCnBeC15W5VAU5kW2f80QS3_SQSnbEjyOtg8XCcmkvUXw' 'http://a1.easemob.com/easemob-demo/testapp/chatmessages/2018112717'
```

#### Examples of possible returned results

**Returns a value of 200, indicating that the chat file download address was successfully returned**

``` json
{
  "action": "get",
  "application": "8be024f0-e978-11e8-b697-5d598d5f8402",
  "uri": "http://a1.easemob.com/easemob-demo/testapp/chatmessages/2018112717",
  "data": [
    {
      "url": "http://ebs-chatmessage-a1.easemob.com/history/3D/easemob-demo/testapp/2018112717.gz?Expires=1543316122&OSSAccessKeyId=LTAIlKPZStPokdA8&Signature=2oQHPpaOgrGcqggkmeXqovM%2FWd8%3D"
    }
  ],
  "timestamp": 1543314322601,
  "duration": 0,
  "organization": "easemob-demo",
  "applicationName": "testapp"
}

Note: the url has an expiration time, the Expires timestamp in the url is the expiration time (seconds), please download the chat file through the url in time, it will not be downloaded after the expiration, you need to call the "Get History Message File" interface again to get the new url.
```

**Returns 400, indicating that the history to be obtained has expired and the history file to be obtained has not yet been generated**

``` json
{
  "error": "illegal_argument",
  "timestamp": 1543312726441,
  "duration": 0,
  "exception": "java.lang.IllegalArgumentException",
  "error_description": "illegal arguments: appkey: easemob-demo#testapp, time: 2018112717, maybe chat message history is expired or unstored"
}
```

**Returns value 401, means unauthorized\[no token, token error, token expired\]**

``` json
{
  "error": "unauthorized",
  "timestamp": 1543314417735,
  "duration": 0,
  "exception": "org.apache.shiro.authz.UnauthorizedException"
}
```

If the return result is <font color='red'> 5xx </font>, it may mean that the interface is flow-limited, please pause a little and retry. See [Interface flow restriction description](/server_rest_interface_flow_limiting_instructions.html) for details

[Test online using Easemob REST API](http://api-docs.easemob.com/)

> Tips

In rare cases, messages in the chat log may be recorded repeatedly.

The query time format is in 10-digit form (YYYYMMDDHH), for example, to query the history records from 7:00 to 8:00 on December 10, 2016, you need to enter 2016121007,7:00:00 messages will also be included in this file.

Because the history file takes some time to generate, it is recommended that users obtain the history with an hour interval, for example, after 09:00 of 2016/12/10, you can start downloading the message history of 2016/12/10 07:00 ~ 08:00.

The download address returned by the interface is valid for 30 minutes, and the server side saves 3 days of text messages and 7 days of file messages by default, please contact the business manager if you need to extend the storage time.

------------------------------------------------------------------------
