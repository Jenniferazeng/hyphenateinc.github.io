# Tool Description 

------------------------------------------------------------------------

## File upload and download judgment

``` javascript
//whether or not files can be uploaded
WebIM.utils.isCanUploadFile;
//whether or not files can be downloaded
WebIM.utils.isCanDownLoadFile ;
//whether to set header 
WebIM.utils. isCanSetRequestHeader;
//whether to set mimetype
WebIM.utils.hasOverrideMimeType;
```

## Emoji parse tool

``` javascript
WebIM.utils.parseEmoji(message);
```

## Format string tool

Currently only the string `%s` can be parsed

``` javascript
WebIM.utils.sprintf(string[, args...])
```

## Upload and download file tools

``` javascript
var fileInfo = WebIM.utils.getFileUrl(fileInputId);

//Upload
var options = {
  apiUrl:'//a1.easemob.com',
  appName: 'chatdemoui',
  orgName: 'easemob-demo',
  appKey:'easemob-demo#chatdemoui',
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
  url:'http://s1.easemob.com/weiquan2/a2/chatfiles/0c0f5f3a-e66b-11e3-8863-f1c202c2b3ae',
  secret: 'NSgGYPCxEeOou00jZasg9e-GqKUZGdph96EFxJ4WxW-qkxV4',
  accessToken: 'YWMtjPPoovCqEeOQs7myPqqaOwAAAUaqNH0a8rRj4PwJLQju6-S47ZO6wYs3Lwo',
  onFileDownloadComplete: function ( data ) { //download file success },
  onFileDownloadError: function ( e ) { //download file error }
};

WebIM.utils.download(options);
```

## Send Ajax request

``` javascript
var options = {
  dataType: 'text',//default
  success: function () { //handle request success },
  error: function () { //handle request error },
  type: 'post',//default 'post'
  url: 'http://s1.easemob.com/weiquan2/a2/chatfiles/0c0f5f3a-e66b-11e3-8863-f1c202c2b3ae',
  headers: '',//default {}
  data: '';//default null
};

WebIM.utils.ajax(options);
```

