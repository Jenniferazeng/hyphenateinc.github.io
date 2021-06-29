---
title: web Run the Sample Project
keywords: web
sidebar: web_sidebar
toc: true
permalink: web_run_the_sample_project.html
folder: web
---

# Web IM Integration Introduction

## Set up a local test environment

Before setting up the environment, you need to have a general understanding of the reference documents provided by Agora, which are mainly in the following form, please select the source code reference by using the keywords

-   Complete real-time demo based on react development.
    Keywords: **at least IE9**, **<u>entire process</u>**, **webpack+react** 

1\. First clone the source code to the local

``` bash
$ git clone https://github.com/HyphenateInc/Hyphenate-Demo-Web
```

2\. Go to the official website to install[NodeJS](https://nodejs.org/zh-cn/)，suggest12+

-   Because the whole set of code needs to rely on [npm](https://www.npmjs.com/) NodeJS package management tool, 
    installing NodeJS will  lead to installation NPM tool by default. 

3\. Navigate to the root directory 
    Execute the following command in the terminal to install the dependent modules needed for the test

-   Ensure that there is no error occurs in this process
    The termination is successful. If there is an error interruption, please keep the error log and try again. The connection is interrupted due to network failure in most cases. 

``` bash
npm i
```

4\. After successfully finishing the above steps

``` bash
# Start the test environment
npm start （If you need https, start it with HTTPS=true npm start）
# Release package，the file is in the /build directory
npm run build
```

5\. Browser access to see the test page：

-   http：<http://localhost:3000/>

-   https：<https://localhost:3000/>

## Integration

The Web SDK can be referenced in the following way: 

-   Use NPM

### NPM

Web SDK has been released to[NPM](https://www.npmjs.com/package/agora-chat-sdk). Use the following methods to integrate:

1\. Install the Web SDK via NPM.

    npm install agora-chat-sdk --save

2\. Import first, then visit Web IM. 

``` javascript
import websdk from "agora-chat-sdk"
```

**Note：**
This method only references the Web SDK, the parameters in the WebIMConfig file still need to be configured in the project to instantiate the websdk. 

## Configuration 

For the 3.0 SDK, deploy the following configuration in the WebIMConfig.js file

``` javascript
socketServer: '//im-api-v2.easemob.com/ws',    // socket Server address

restServer: '//a1.easemob.com',               // rest Server address

appkey: 'easemob-demo#chatdemoui',        // App key

https : false,                            // Whether to use https

isHttpDNS: true,                          // 3.0 SDK support to prevent DNS hijacking to obtain XMPPUrl, restUrl from the server

isMultiLoginSessions: false,              // Whether to enable multi-page synchronization to receive messages, note that you need  to contact the business to activate this function

isDebug: false,                           // Turn on debugging, the log will be automatically printed, and you can view the log in the console of the console 

autoReconnectNumMax: 2,                   // Maximum number of disconnected reconnections

delivery: false,                           // Whether to send a read receipt 

useOwnUploadFun: false         // Whether to use your own upload method (such as uploading image files to your own server, and only uploading the url when building a message) 
```

`Note：`

`To meet the business requirements of different customers, Agora has deployed data centers in multiple locations. The REST API request domain name and WebSocket access domain name of different data centers are different. Please configure it according to data center which suits you.`

`` REST API request domain name and WebSocket access domain name of different data centers of Agora:``
|Data center|REST API request address|WebSocket access domain name|
| -------- | -------| ----- |
 |Domestic Zone 1|a1.easemob.com|im-api-v2.easemob.com| 
 |Domestic Zone 2|a31.easemob.com|im-api-v2-31.easemob.com| 
 |Domestic VIP Zone|Please contact the business manager|Please contact the business manager|
  |Customer service|Please contact the business manager|Please contact the business manager| 
  |Singapore Zone 1|a1-sgp.easemob.com|im-api-sgp-v2.easemob.com| 

``The data center where the application is located can be viewed in Agora 
User Management Background>Application Information: ``

![console](/images/web/console.jpg)
