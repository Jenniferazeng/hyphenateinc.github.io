---
title: Utils
keywords: web
sidebar: web_sidebar
toc: true
permalink: web_utils.html
folder: web
---

# helper apis 

------------------------------------------------------------------------


## Emoji parse tool

``` javascript
WebIM.utils.parseEmoji(message);
```

## Upload and download file tools

``` javascript
var fileInfo = WebIM.utils.getFileUrl(fileInputId);

//Upload
var options = {
  apiUrl:'//a1.easecdn.com',
  appName: 'chatdemoui',
  orgName: 'easecdn-demo',
  appKey:'1193210624041558#chatdemo',
  file:fileInfo,
  accessToken: 'YWMtjPPoovCqEeOQs7myPqqaOwAAAUaqNH0a8rRj4PwJLQju6-S47ZO6wYs3Lwo',
  onFileUploadComplete: function ( data ) { //upload file success },
  onFileUploadError: function ( e ) { //upload file error }
};

WebIM.utils.uploadFile(options);


//Download
var options = {
  responseType: 'blob',//default blob
  mimeType: 'text/plain; charset=x-user-defined',//default
  url:'http://s1.easecdn.com/weiquan2/a2/chatfiles/0c0f5f3a-e66b-11e3-8863-f1c202c2b3ae',
  secret: 'NSgGYPCxEeOou00jZasg9e-GqKUZGdph96EFxJ4WxW-qkxV4',
  accessToken: 'YWMtjPPoovCqEeOQs7myPqqaOwAAAUaqNH0a8rRj4PwJLQju6-S47ZO6wYs3Lwo',
  onFileDownloadComplete: function ( data ) { //download file success },
  onFileDownloadError: function ( e ) { //download file error }
};

WebIM.utils.download(options);
```

