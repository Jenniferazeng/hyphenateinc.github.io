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

  Parameter     Description
----------- -----------------------------------------------------------------------------
  msg_id           Message ID
  timestamp    Message Delivery Time
  from              Sender username
  to                   The username of the recipient or the ID of the receiving group
  chat_type      Used to determine whether it is a single chat, group chat, or chatroom. chat: single chat; groupchat: group chat; chatroom: chatroom
  payload         message bodies, different message types; message ext, custom extended attributes, etc.

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

  Parameter    Description
------ -----------------------
  msg               Message content
  type               \"txt\"，text message type

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

  Parameter      Description
------------- ----------------------------------------------------------
  file_length      size of the image attachment (in bytes)
  filename         \"image.jpg\",the name of the image with the image format
  secret              A string is returned after uploading an image, only if it contains secret can the image be downloaded
  size                  the size of the image length and width
  type                \"img\",the type of the image message
  url                   The address of the image upload message, which will return the UUID after successful upload.

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

  Parameter      Description
------ --------------------------------
  addr                 "address" Description of the text content of the address to be sent
  lat   				  latitude
  lng    				longitude
  type  			   \"loc\"， location message type

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

  Parameter 	Description
------------- --------------------------------------------------------------
  file_length     Voice attachment size (in bytes)
  filename      \"audio,amr\", voice file with file format
  secret       	 will return a string after uploading the voice, only contains secret can download this voice file
  length       	 voice time (in seconds)
  type         	\"audio\",voice message type
  url          		The address of the voice file to be uploaded, the UUID will be returned after successful upload.

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

  Parameter 	Description
-------------- ----------------------------------------------------------------------
  file_length   	   Video attachment size (in bytes)
  filename       	\"video,mp4\", video file with file format
  secret        		 Will return a string after uploading the video, only contains secret can download this video file
  length        	      The length of the video playback (in seconds)
  size          		     Video thumbnail length and width size
  thumb                  The remote address of the video thumbnail that will be returned as a UUID after uploading the video thumbnail.
  thumb_secret     The string to be returned after uploading the video thumbnail, only if it contains a secret can the video thumbnail be downloaded
  type           		 \"video\",video message type
  url            	     	The address of the uploaded video, the UUID will be returned after a successful upload.

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

  Parameter 	Description
------------- ----------------------------------------------------------
  file_length   File size (in bytes)
  filename      \"file.zip\", file name with file format
  secret           After uploading a file, a string is returned and only the file containing secret can be downloaded.
  type             \"file\",image message type
  url           	The address of the uploaded file, and the UUID will be returned after a successful upload.

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

  Name 						Request 																			Description
------------------ ---------------------------------------------- --------------------------------------------
  Get history message file    /{org_name}/{app_name}/chatmessages/\${time}   A file that returns data based on the time range of the request, download to view

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

  ![](/im/server/ready/get.png){.align-left width="90"}   **/{org_name}/{app_name}/chatmessages/\${time}**
------------------------------------------------------- --------------------------------------------------

You need to fill in the {time} corresponding to the time period you need to get in the request.

#### Request Headers

  Parameter 		Description
--------------- ------------------
  Content-Type    application/json
  Authorization   Bearer \${token}

#### Response Body

View the information contained in the data field in the return value

  Parameter 	Description
------ ----------------------
  url    			   The address to download the chat log file

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

If the return result is \<wrap
em>5xx\</wrap>, it may mean that the interface is flow-limited, please pause a little and retry. See [Interface flow restriction description](/im/450errorcode/45restastrict) for details

[Test online using Easemob REST API](http://api-docs.easemob.com/)

> Tips

In rare cases, messages in the chat log may be recorded repeatedly.

The query time format is in 10-digit form (YYYYMMDDHH), for example, to query the history records from 7:00 to 8:00 on December 10, 2016, you need to enter 2016121007,7:00:00 messages will also be included in this file.

Because the history file takes some time to generate, it is recommended that users obtain the history with an hour interval, for example, after 09:00 of 2016/12/10, you can start downloading the message history of 2016/12/10 07:00 ~ 08:00.

The download address returned by the interface is valid for 30 minutes, and the server side saves 3 days of text messages and 7 days of file messages by default, please contact the business manager if you need to extend the storage time.

------------------------------------------------------------------------
