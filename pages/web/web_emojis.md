---
title: emojis
keywords: web
sidebar: web_sidebar
toc: true
permalink: web_emojis.html
folder: web
---

## Import third-party emoticons

-   Create a new folder under the project to store emoticon image files.

-   After quoting the SDK, execute the following code: 

``` javascript
WebIM.Emoji = {
    path: 'src/assets/faces/',  /*Emoticon path*/
    map: {
        '[):]': 'ee_1.png',
        '[:D]': 'ee_2.png',
        '[;)]': 'ee_3.png',
        '[:-o]': 'ee_4.png',
        '[:p]': 'ee_5.png'
    }
};
```

**Note：**

-   Global variable WebIM adds an Emoji attribute

-   path represents the path where the emoticon image is stored 

-   path represents the path where the emoticon image is stored 


-   value represents the file name of the emoticon image

Sending and receiving emoticon messages is similar to text messages. If the sent text message contains the key character of the emoticon, the SDK will convert the message into the actual path of the emoticon image. 

Eg. If the text message contains the string \"\[):\]\", it will be parsed as `WebIM.Emoji.path+WebIM.Emoji.map['[):]']= "src/assets/faces/ee_1.png"`. 


