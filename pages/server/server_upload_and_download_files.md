---
title: Upload and Download files
keywords: server
sidebar: server_sidebar
toc: true
permalink: server_upload_and_download_files.html
folder: server
---

Chat uses Platform API to upload and download files such as audio and images. Also, to ensure the security of chat files, our API ensures the following.

- Only the logged-in user of the APP can upload files.
- When uploading files, you can choose whether to restrict the access rights.
- If you choose to restrict access, a secret will be returned after the upload request is completed, and only users who know this secret and are registered users of APP can download the file.
- If you choose unrestricted, you can download the file as long as you are a registered user of APP.

------------------------------------------------------------------------

# Platform API

The Platform API document that needs to be used during the file upload and download integration is detailed and can be tested online by using the [Platform API](http://api-docs.easemob.com/) embedded in the documentation.

<table border="1" cellspacing="0" bordercolor="#000000">
  <tr>
    <th>Name</th>
    <th>Request</th>
    <th>Description</th>
  </tr>
  <tr>
    <td>Upload audio/image files</td>
    <td>/{org_name}/{app_name}/chatfiles</td>
    <td>Upload audio/image files</td>
  </tr>
  <tr>
    <td>Download audio/image files</td>
    <td>/{org_name}/{app_name}/chatfiles/{filestream}</td>
    <td>Download audio/image files</td>
  </tr>
  <tr>
    <td>Download thumbnails</td>
    <td>/{org_name}/{app_name}/chatfiles/{file_uuid}</td>
    <td>Download thumbnails</td>
  </tr>
</table>

## Upload audio/image files

**Note:**The uploading file size cannot exceed 10M, it will fail to upload if exceeded. Required HTTP Header: *Authorization -- the token getted* restrict-access -- whether to restrict access.
Whether to restrict access. 

**Note:** This API does not take into account the value of this attribute, but just has the attribute. Finally, it is necessary to use the HTTP multipart/form-data form.

#### HTTP Request

<table border="1" cellspacing="0" bordercolor="#000000">
  <tr>
    <th>POST</th>
    <th>/{org_name}/{app_name}/chatfiles</th>
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

#### Response Body

View the information contained in the entities field in the return value

<table border="1" cellspacing="0" bordercolor="#000000">
  <tr>
    <th>Parameter</th>
    <th>Description</th>
  </tr>
  <tr>
    <td>uuid</td>
    <td>The unique ID of the file, specifying which file is needed to send the message</td>
  </tr>
  <tr>
    <td>type</td>
    <td>File type</td>
  </tr>
  <tr>
    <td>share-secret</td>
    <td>Returned after a successful upload, it will be required when sending a message</td>
  </tr>
</table>

#### Request example

``` php
curl -X POST 'https://a1.easecdn.com/chat-demo/testapp/chatfiles' -H 'Authorization: Bearer YWMtS1pRuFa-EemixAMhJgmGUAAAAAAAAAAAAAAAAAAAAAGL4CTw6XgR6LaXXVmNX4QCAgMAAAFp57yd7QBPGgDNLstNggrzgHV3JAbqbznTptqLhpG0fTOCaBFJZgduZA' -H 'content-type: multipart/form-data; boundary=----WebKitFormBoundary7MA4YWxkTrZu0gW'  -H 'restrict-access: true' -F file=@/Users/test/9.2/Chat/image/IMG_2953.JPG
```

**Note:** In the above request example, `-F file=@/Users/test/9.2/Chat/image/IMG_2953.JPG` is the local file path of Chat, please replace it with your own file path when using it, otherwise the request will fail.

Example of possible returned results

**returns a value of 200, indicating a successful file upload**

``` json
{
    "action": "post",
    "application": "8be024f0-e978-11e8-b697-5d598d5f8402",
    "path": "/chatfiles",
    "uri": "https://a1.easecdn.com/chat-demo/testapp/chatfiles",
    "entities": [
        {
            "uuid": "5fd74830-56be-11e9-822a-81ea50bb049d",
            "type": "chatfile",
            "share-secret": "X9dIOla-EemnFYUgtUZLGyqG9Y-S01eL_ysw27NqTV1_g7Yc"
        }
    ],
    "timestamp": 1554371126338,
    "duration": 0,
    "organization": "chat-demo",
    "applicationName": "testapp"
}
```

**return value 401, unauthorized [no token, token error, token expired]**

``` json
{
  "error": "auth_bad_access_token",
  "timestamp": 1542348025595,
  "duration": 0,
  "exception": "org.apache.usergrid.rest.exceptions.SecurityException",
  "error_description": "Unable to authenticate due to corrupt access token"
}
```

If the return result is <font color='red'> 429, 503 </font> or other <font color='red'> 5xx </font>, it may mean that the interface is flow-limited, please pause a little and retry. See [interface flow restriction instructions](/server_rest_interface_flow_limiting_instructions.html) for details

[Test online using Platform API](http://api-docs.easemob.com/)

------------------------------------------------------------------------

## Downloading audio/image files

Here we need to note that we need to bring the share-secret and the token of the currently logged in user in the HTTP header to be able to download, and we need to set the value of accept in the header to application/octet-stream.

The uuid and share-secret will be returned after the file is uploaded successfully.

#### HTTP Request

<table border="1" cellspacing="0" bordercolor="#000000">
  <tr>
    <th>GET</th>
    <th>/{org_name}/{app_name}/chatfiles/{uuid}</th>
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
  <tr>
    <td>share-secret</td>
    <td>share-secret</td>
  </tr>
</table>

#### Request Example

``` php
curl -X GET -H 'Accept: application/octet-stream' -H 'Authorization: Bearer YWMte3bGuOukEeiTkNP4grL7iwAAAAAAAAAAAAAAAAAAAAGL4CTw6XgR6LaXXVmNX4QCAgMAAAFnKdc-ZgBPGgBFTrLhhyK8woMEI005emtrLJFJV6aoxsZSioSIZkr5kw' -H 'share-secret: f0Vr-uyyEeiHpHmsu53Togur4ngZYgyLkdfsZ4xo2Z0cSBnB' 'http://a1.easecdn.com/chat-demo/testapp/chatfiles/7f456bf0-ecb2-11e8-b630-777db304f26c'
```

#### Example of possible returned results 

**returns a value of 200, which means the file was downloaded successfully**

``` json
{
//speech/image file
}
```

**return value 401, unauthorized [no token, token error, token expired ]**

``` json
{
  "error": "auth_bad_access_token",
  "timestamp": 1542349596366,
  "duration": 0,
  "exception": "org.apache.usergrid.rest.exceptions.SecurityException",
  "error_description": "Unable to authenticate due to corrupt access token"
}
```

If the return result is <font color='red'> 429, 503 </font> or other <font color='red'> 5xx </font>, it may mean that the interface is flow-limited, please pause for a while and retry. See [interface flow restriction instructions](/server_rest_interface_flow_limiting_instructions.html) for details

[Test online using Platform API](http://api-docs.easemob.com/)

------------------------------------------------------------------------

## Download thumbnails

Chat supports creating thumbnails of images automatically on the server side. You can download the thumbnails first, and then download the larger image when the user has a need for it.
The only difference between this and downloading a large image is that the header has an additional "thumbnail: true", when the server sees that the header of the request includes this, it will return the thumbnail, otherwise it returns the original large image.

#### HTTP Request

<table border="1" cellspacing="0" bordercolor="#000000">
  <tr>
    <th>GET</th>
    <th>/{org_name}/{app_name}/chatfiles/{file_uuid}</th>
  </tr>
</table>

You need to fill in {file_uuid} when requesting, you need to get the uuid returned from the file.

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

#### Request Example

``` php
curl -X GET -H 'Accept: application/octet-stream' -H 'Authorization: Bearer YWMte3bGuOukEeiTkNP4grL7iwAAAAAAAAAAAAAAAAAAAAGL4CTw6XgR6LaXXVmNX4QCAgMAAAFnKdc-ZgBPGgBFTrLhhyK8woMEI005emtrLJFJV6aoxsZSioSIZkr5kw' -H 'share-secret: f0Vr-uyyEeiHpHmsu53Togur4ngZYgyLkdfsZ4xo2Z0cSBnB' -H 'thumbnail: true' 'http://a1.easecdn.com/chat-demo/testapp/chatfiles/7f456bf0-ecb2-11e8-b630-777db304f26c'
```

#### Example of possible returned results 

**returns a value of 200, indicating that the download of the thumbnail was successful**

``` json
{
//thumbnail information
}
```

**return value 401, unauthorized [no token, token error, token expired]**

``` json
{
  "error": "auth_bad_access_token",
  "timestamp": 1542350943210,
  "duration": 0,
  "exception": "org.apache.usergrid.rest.exceptions.SecurityException",
  "error_description": "Unable to authenticate due to corrupt access token"
}
```

If the return result is <font color='red'> 429, 503 </font> or other <font color='red'> 5xx </font>, it may mean that the interface is flow-limited, please pause a little and retry. See [interface flow restriction instructions](/server_rest_interface_flow_limiting_instructions.html) for details

[Test online using Platform API](http://api-docs.easemob.com/)

