---
title: Upload and Download files
keywords: server
sidebar: server_sidebar
toc: true
permalink: server_upload_and_download_files.html
folder: server
---

# Upload and Download files

------------------------------------------------------------------------

Easemob uses REST to upload and download files such as voice and images. Also, to ensure the security of chat files, our API ensures the following.

- Only the logged-in user of the APP can upload files.
- When uploading files, you can choose whether to restrict the access rights.
- If you choose to restrict access, a secret will be returned after the upload request is completed, and only users who know this secret and are registered users of APP can download the file.
- If you choose unrestricted, you can download the file as long as you are a registered user of APP.

------------------------------------------------------------------------

# REST API

The REST API document that needs to be used during the file upload and download integration is detailed and can be tested online by using the [Easemob REST API](http://api-docs.easemob.com/) embedded in the documentation.

  Name Request Description

------------------- ---------------------------------------------- -------------------

  Upload voice/image files 		/{org_name}/{app_name}/chatfiles 							Upload voice/image files
  Download voice/image files 	{org_name}/{app_name}/chatfiles/{filestream} 	 Download voice/image files
  Download thumbnails 			/{org_name}/{app_name}/chatfiles/{file_uuid} 		 Download thumbnails

## Upload voice/image files

**Note: **The uploading file size cannot exceed 10M, it will fail to upload if exceeded. Required HTTP Header: \* Authorization -- the token getted \* restrict-access -- whether to restrict access.
Whether to restrict access. 

**Note:** This API does not take into account the value of this attribute, but just has the attribute. Finally, it is necessary to use the HTTP multipart/form-data form.

#### HTTP Request

  ![](/im/server/basics/post.png){width="90"}   **/{org_name}/{app_name}/chatfiles**
--------------------------------------------- --------------------------------------

#### Request Headers

  Parameter 			Description

----------------- ------------------

  restrict-access 		true
  Authorization 		Bearer \${token}

#### Response Body

View the information contained in the entities field in the return value

  Parameter 		Description

-------------- ------------------------------------------------

  uuid 					The unique ID of the file, specifying which file is needed to send the message
  type 					File type
  share-secret 	Returned after a successful upload, it will be required when sending a message

#### Request example

``` php
curl -X POST https://a1.easemob.com/easemob-demo/testapp/chatfiles -H 'Authorization: Bearer YWMtS1pRuFa-EemixAMhJgmGUAAAAAAAAAAAAAAAAAAAAAGL4CTw6XgR6LaXXVmNX4QCAgMAAAFp57yd7QBPGgDNLstNggrzgHV3JAbqbznTptqLhpG0fTOCaBFJZgduZA' -H 'content-type: multipart/form-data; boundary=----WebKitFormBoundary7MA4YWxkTrZu0gW'  -H 'restrict-access: true' -F file=@/Users/test/9.2/Easemob/image/IMG_2953.JPG
```

**Note:** In the above request example, `-F file=@/Users/test/9.2/Easemob/image/IMG_2953.JPG` is the local file path of Easemob, please replace it with your own file path when using it, otherwise the request will fail.

Example of possible returned results

**returns a value of 200, indicating a successful file upload**

``` json
{
    "action": "post",
    "application": "8be024f0-e978-11e8-b697-5d598d5f8402",
    "path": "/chatfiles",
    "uri": "https://a1.easemob.com/easemob-demo/testapp/chatfiles",
    "entities": [
        {
            "uuid": "5fd74830-56be-11e9-822a-81ea50bb049d",
            "type": "chatfile",
            "share-secret": "X9dIOla-EemnFYUgtUZLGyqG9Y-S01eL_ysw27NqTV1_g7Yc"
        }
    ],
    "timestamp": 1554371126338,
    "duration": 0,
    "organization": "easemob-demo",
    "applicationName": "testapp"
}
```

**return value 401, unauthorized\[no token, token error, token expired\]**

``` json
{
  "error": "auth_bad_access_token",
  "timestamp": 1542348025595,
  "duration": 0,
  "exception": "org.apache.usergrid.rest.exceptions.SecurityException",
  "error_description": "Unable to authenticate due to corrupt access token"
}
```

If the return result is \<wrap em>429, 503\</wrap> or other \<wrap
em>5xx\</wrap>, it may mean that the interface is flow-limited, please pause a little and retry. See [interface flow restriction instructions](/im/450errorcode/45restastrict) for details

[Test online using Easemob REST API](http://api-docs.easemob.com/)

------------------------------------------------------------------------

## Downloading voice/image files

Here we need to note that we need to bring the share-secret and the token of the currently logged in user in the HTTP header to be able to download, and we need to set the value of accept in the header to application/octet-stream.

The uuid and share-secret will be returned after the file is uploaded successfully.

#### HTTP Request

  ![](/im/server/basics/get.png){width="90"}   **/{org_name}/{app_name}/chatfiles/{uuid}**
-------------------------------------------- ---------------------------------------------

#### Request Headers

  Parameter 			Description

--------------- ------------------

  Content-Type 		application/json
  Authorization 		Bearer \${token}
  share-secret 			share-secret

#### Request Example

``` php
curl -X GET -H 'Accept: application/octet-stream' -H 'Authorization: Bearer YWMte3bGuOukEeiTkNP4grL7iwAAAAAAAAAAAAAAAAAAAAGL4CTw6XgR6LaXXVmNX4QCAgMAAAFnKdc-ZgBPGgBFTrLhhyK8woMEI005emtrLJFJV6aoxsZSioSIZkr5kw' -H 'share-secret: f0Vr-uyyEeiHpHmsu53Togur4ngZYgyLkdfsZ4xo2Z0cSBnB' 'http://a1.easemob.com/easemob-demo/testapp/chatfiles/7f456bf0-ecb2-11e8-b630-777db304f26c'
```

#### Example of possible returned results 

**returns a value of 200, which means the file was downloaded successfully**

``` json
{
//speech/image file
}
```

**return value 401, unauthorized \ [no token, token error, token expired \]**

``` json
{
  "error": "auth_bad_access_token",
  "timestamp": 1542349596366,
  "duration": 0,
  "exception": "org.apache.usergrid.rest.exceptions.SecurityException",
  "error_description": "Unable to authenticate due to corrupt access token"
}
```

If the return result is \<wrap em>429, 503\</wrap> or other \<wrap
em>5xx\</wrap>, it may mean that the interface is flow-limited, please pause for a while and retry. See [interface flow restriction instructions](/im/450errorcode/45restastrict) for details

[Test online using Easemob REST API](http://api-docs.easemob.com/)

------------------------------------------------------------------------

## Download thumbnails

Easemob supports creating thumbnails of images automatically on the server side. You can download the thumbnails first, and then download the larger image when the user has a need for it.
The only difference between this and downloading a large image is that the header has an additional "thumbnail: true", when the server sees that the header of the request includes this, it will return the thumbnail, otherwise it returns the original large image.

#### HTTP Request

  ![](/im/server/basics/get.png){width="90"}   **/{org_name}/{app_name}/chatfiles/{file_uuid}**
-------------------------------------------- --------------------------------------------------

You need to fill in {file_uuid} when requesting, you need to get the uuid returned from the file.

#### Request Headers

  Parameter 			Description

--------------- ------------------

  Content-Type 		application/json
  Authorization 		Bearer \${token}

#### Request Example

``` php
curl -X GET -H 'Accept: application/octet-stream' -H 'Authorization: Bearer YWMte3bGuOukEeiTkNP4grL7iwAAAAAAAAAAAAAAAAAAAAGL4CTw6XgR6LaXXVmNX4QCAgMAAAFnKdc-ZgBPGgBFTrLhhyK8woMEI005emtrLJFJV6aoxsZSioSIZkr5kw' -H 'share-secret: f0Vr-uyyEeiHpHmsu53Togur4ngZYgyLkdfsZ4xo2Z0cSBnB' -H 'thumbnail: true' 'http://a1.easemob.com/easemob-demo/testapp/chatfiles/7f456bf0-ecb2-11e8-b630-777db304f26c'
```

#### Example of possible returned results 

**returns a value of 200, indicating that the download of the thumbnail was successful**

``` json
{
//thumbnail information
}
```

**return value 401, unauthorized\[no token, token error, token expired\]**

``` json
{
  "error": "auth_bad_access_token",
  "timestamp": 1542350943210,
  "duration": 0,
  "exception": "org.apache.usergrid.rest.exceptions.SecurityException",
  "error_description": "Unable to authenticate due to corrupt access token"
}
```

If the return result is \<wrap em>429, 503\</wrap> or other \<wrap
em>5xx\</wrap>, it may mean that the interface is flow-limited, please pause a little and retry. See [interface flow restriction instructions](/im/450errorcode/45restastrict) for details

[Test online using Easemob REST API](http://api-docs.easemob.com/)

------------------------------------------------------------------------

\<WRAP group> \<WRAP half column>
Previous page: [chat log](/im/server/basics/chatrecord) \</WRAP

\<WRAP half column
Next page: [Conference management](/im/server/basics/conferencemanage) \</WRAP> \</WRAP>
