---
title:  Push Content
keywords: ios
sidebar: ios_sidebar
toc: true
permalink: ios_push_content.html
folder: ios
---


## Parse APNs content

## Single chat

### Do not display the details

    {
        "aps":{
            "alert":{
                "body":"you got a new message"
            },   
            "badge":1,               
            "sound":"default"        
        },
        "f":"6001",                  
        "t":"6006",                  
        "m":"373360335316321408"             
    }

-   alert: messages display
-   badge: corner mark, represent the number of offline messages
-   sound: sound of alert when receving APNs
-   f: Easemob ID of the message sender
-   t: Easemob ID of the message recevier
-   m: message ID

------------------------------------------------------------------------

### Display the details

    {
        "aps":{
            "alert":{
                "body":"ApnsNickName:xxxx"
            },   
            "badge":1,               
            "sound":"default"        
        },
        "f":"6001",                  
        "t":"6006",                  
        "m":"373360335316321408"             
    }

-   alert: messages display
-   ApnsName: User name set by the sender(the user's nickname seen in the backstage of Easemob)
-   xxxx: Content of message（it will show what the sender sent）
-   badge:corner mark, represent the number of offline messages
-   sound: sound of alert when receving APNs
-   f: Easemob ID of the message sender
-   t: Easemob ID of the message recevier
-   m:  message ID

------------------------------------------------------------------------

## Group Chat

### do not display the details

    {
        "aps":{
            "alert":{
                "body":"you got a new message"
            },   
            "badge":1,               
            "sound":"default"        
        },
        "f":"6001",                  
        "t":"6006", 
        "g":"1421300621769",                 
        "m":"373360335316321408"             
    }

-   alert: messages display
-   badge: corner mark, represent the number of offline messages
-   sound: sound of alert when receiving APNs
-   f: Easemob ID of the message sender
-   t: Easemob ID of the message receiver
-   g: group ID
-   m: message ID

------------------------------------------------------------------------

### Display the details

    {
        "aps":{
            "alert":{
                "body":"ApnsName:xxxx"
            },   
            "badge":1,               
            "sound":"default"        
        },
        "f":"6001",                  
        "t":"6006",     
        "g":"1421300621769",
        "m":"373360335316321408"             
    }

-   alert: messages display
-   Apns Name: User name set by the sender(the user's nickname seen in the backstage of Easemob)
-   xxxx: Content of message（it will show what the sender sent）
-   badge: corner mark, represent the number of offline messages
-   sound: sound of alert when receiving APNs
-   f: Easemob ID of the message sender
-   t: Easemob ID of the message receivier
-   g: group ID
-   m: message ID

------------------------------------------------------------------------

## Add extension fields to APNs

APNs extension fields(em_apns_ext): After adding it, the APNs you received will contain the fields you filled in, which can help you distinguish APNs.

Easemob provides the following extension fields:

  extension fields 			descriptions
------------------------- --------------------------------------
  em_push_content          Custom push display
  em_push_category         Add category field to APNs Payload
  em_push_sound             Custom push alert sound
  em_push_mutable_content   Enable APNs alert extension

#### Parse content

    {
        "apns": {
            "alert": {
                "body": "hello from rest"
            }, 
            "badge": 1, 
            "sound": "default"
        }, 
        "e": "Custom push extension", 
        "f": "6001", 
        "t": "6006", 
        "m": "373360335316321408"
    }

-   e: Custom push extension sent by you

#### REST Send

（[REST Sending messages](/im/server/basics/messages#发送扩展消息)）

    {
        "target_type": "users", 
        "target": [
            "6006"
        ], 
        "msg": {
            "type": "txt", 
            "msg": "hello from rest"
        }, 
        "ext": {
            "em_apns_ext": {
                "extern": "Custom push extension"
            }
        }, 
        "from": "6001"
    }

#### iOS Send

（[iOS Sending messages](/im/ios/basics/message#构造扩展消息)）

    AgoraTextMessageBody *body = [[AgoraTextMessageBody alloc] initWithText:@"test"];
    AgoraMessage *message = [[AgoraMessage alloc] initWithConversationID:@"6006" from:@"6001" to:@"6006" body:body ext:nil];
    message.ext = @{@"em_apns_ext":@{@"extern":@"custom push extension"}}; // The ext here is the same as the ext passed when the message is initialized. The purpose of list it separately here is to express it more clearly.
    message.chatType = AgoraChatTypeChat; // Set the message type
    [AgoraChatClient.sharedClient.chatManager sendMessage:message progress:nil completion:nil];

------------------------------------------------------------------------

### Custom display

After set up, the APNs alert information you receive will be the information you set

#### parse

    {
        "aps":{
            "alert":{
                "body":"Custom push display"
            },   
            "badge":1,               
            "sound":"default"        
        },
        "f":"6001",                  
        "t":"6006",                  
        "m":"373360335316321408"             
    }

#### REST Send

([REST sending messages](/im/server/basics/messages#发送文本消息))

    {
        "target_type": "users", 
        "target": [
            "6006"
        ], 
        "msg": {
            "type": "txt", 
            "msg": "hello from rest"
        }, 
        "from": "6001", 
        "ext": {
            "em_apns_ext": {
                "em_push_content": "Custom push display"
            }
        }
    }

#### iOS Send

([iOS sending message](/im/ios/basics/message#构造消息))

    AgoraTextMessageBody *body = [[AgoraTextMessageBody alloc] initWithText:@"test"];
    AgoraMessage *message = [[AgoraMessage alloc] initWithConversationID:@"6006" from:@"6001" to:@"6006" body:body ext:nil];
    message.ext = @{@"em_apns_ext":@{@"extern":@"custom push display"}}; // The ext here is the same as the ext passed when the message is initialized. The purpose of list it separately here is to express it more clearly.
    message.chatType = AgoraChatTypeChat; // Set the message type
    [AgoraChatClient.sharedClient.chatManager sendMessage:message progress:nil completion:nil];

------------------------------------------------------------------------

### Custom display and custom extension

The custom display and custom extension will be sent to the other party at the same time.

#### parse

    {
        "aps":{
            "alert":{
                "body":"Custom push display"
            },   
            "badge":1,               
            "sound":"default"        
        },
        "f":"6001",                  
        "t":"6006",                  
        "m":"373360335316321408",
        "e": "custom push extension", 
    }

#### REST send

（[REST sending](/im/server/basics/messages#发送扩展消息)）

    {
        "target_type": "users", 
        "target": [
            "6006"
        ], 
        "msg": {
            "type": "txt", 
            "msg": "hello from rest"
        }, 
        "from": "6001", 
        "ext": {
            "em_apns_ext": {
                "em_push_content": "Custom push display",
                "extern": "custom push extension"
            }
        }
    }

#### iOS Send

([iOS sending message](/im/ios/basics/message#构造扩展消息))

    NSString *willSendText = [EaseConvertToCommonEmoticonsHelper convertToCommonEmoticons:text];
    AgoraTextMessageBody *body = [[AgoraTextMessageBody alloc] initWithText:@"test"];
    AgoraMessage *message = [[AgoraMessage alloc] initWithConversationID:@"6006" from:@"6001" to:@"6006" body:body ext:nil];
    message.ext = @{@"em_apns_ext":@{@"extern":@"custom push extension",@"em_push_content":@"custom push display"}}; // The ext here is the same as the ext passed when the message is initialized. The purpose of list it separately here is to express it more clearly.
    message.chatType = AgoraChatTypeChat; // Set the message type
    [AgoraChatClient.sharedClient.chatManager sendMessage:message progress:nil completion:nil];

------------------------------------------------------------------------

### Add category field

Add the category field to APNs Payload.

[description for apple](https://developer.apple.com/library/content/documentation/NetworkingInternet/Conceptual/RemoteNotificationsPG/CreatingtheNotificationPayload.html#//apple_ref/doc/uid/TP40008194-CH10-SW1)

#### REST Send

（[REST sending messages](/im/server/basics/messages#发送扩展消息)）

    {
        "target_type": "users", 
        "target": [
            "6006"
        ], 
        "msg": {
            "type": "txt", 
            "msg": "hello from rest"
        }, 
        "from": "6001", 
        "ext": {
            "em_apns_ext": {
                  "em_push_category" : "NEW_MESSAGE_CATEGORY"
            }
        }
    }

------------------------------------------------------------------------

### Custom push alert sound

After set up, the APNs alert sound you receive will be what you set.

**Note：**

      * If this field is not set, the default sound will be played; if there is this field, the sound will be played if the specified sound is found, otherwise the default sound will be played. If this field is an null, iOS 7 will play the default sound, and iOS 8 and above will keep silent
    
    * The following example custom alert sound file name is custom.caf.
    
    * Integration method: Import the caf format audio file of the custom alert sound into the iOS project. sending message will follow the following example to increase the message extension. When the receiver at offline receives the offline push of APNs, the custom alert sound can be played.

support format Linear PCM MA4 (IMA/ADPCM) µLaw aLaw

Storage path AppData/Library/Sounds,The duration must not exceed 30 seconds。
For the details，please refer to Apple's official documents[Generating a Remote
Notification](https://developer.apple.com/documentation/usernotifications/setting_up_a_remote_notification_server/generating_a_remote_notification?language=objc)

#### parse

    {
        "aps":{
            "alert":{
                "body":"you got a new message"
            },  
            "badge":1,  
            "sound":"custom.caf"  
        },
        "f":"6001",  
        "t":"6006",  
        "m":"373360335316321408"  
    }

#### REST Send

（[REST sending message](/im/server/basics/messages#发送扩展消息)）

**Note：**\"em_push_sound\" is the extension field for setting custom APNs alert sound, value is the name of the audio file in string type.

    {
        "target_type": "users", 
        "target": [
            "6006"
        ], 
        "msg": {
            "type": "txt", 
            "msg": "hello from rest"
        }, 
        "from": "6001", 
        "ext": {
            "em_apns_ext": {
                "em_push_sound": "custom.caf"
            }
        }

#### iOS Send

([iOS sending message](/im/ios/basics/message#构造扩展消息))

    AgoraTextMessageBody *body = [[AgoraTextMessageBody alloc] initWithText:@"test"];
    AgoraMessage *message = [[AgoraMessage alloc] initWithConversationID:@"6006" from:@"6001" to:@"6006" body:body ext:nil];
    message.ext = @{@"em_apns_ext":@{@"em_push_sound":@"custom.caf"}}; // the extension field for setting custom APNs alert sound, value is the name of the audio file in string type
    message.chatType = AgoraChatTypeChat; // Set the message type
    [AgoraChatClient.sharedClient.chatManager sendMessage:message progress:nil completion:nil];

------------------------------------------------------------------------

### Enable APNs alert extension

（UNNotificationServiceExtension）

After set up, the APNs push of this message will support UNNotificationServiceExtension on the server side, and UNNotificationServiceExtension needs to be integrated in the project in the app. After these operation, APNs notification extension can be used. See the iOS integration method.
[Apple  offical document](https://developer.apple.com/documentation/usernotifications/unnotificationserviceextension?language=objc)

#### parse

    {
        "aps":{
            "alert":{
                "body":"you got a new message"
            },  
            "badge":1,  
            "sound":"default",
            "mutable-content":1  
        },
        "f":"6001",  
        "t":"6006",  
        "m":"373360335316321408"  
    }

#### REST Send

（[REST sending message）

**注：**\"em_push_mutable_content\"'s value is bool type，true
 is enable；false or not set, it will be the Remote Notification.

    {
        "target_type": "users", 
        "target": [
            "6006"
        ], 
        "msg": {
            "type": "txt", 
            "msg": "hello from rest"
        }, 
        "from": "6001", 
        "ext": {
            "em_apns_ext": {
                "em_push_mutable_content": true
            }
        }
    }

#### iOS Send

([iOS sending message](/im/ios/basics/message#构造扩展消息))

    AgoraTextMessageBody *body = [[AgoraTextMessageBody alloc] initWithText:@"test"];
    AgoraMessage *message = [[AgoraMessage alloc] initWithConversationID:@"6006" from:@"6001" to:@"6006" body:body ext:nil];
    message.ext = @{@"em_apns_ext":@{@"em_push_mutable_content":@YES}}; // @YES is enable，@NO or not set, it will be the Remote Notification.
    message.chatType = AgoraChatTypeChat; // Set the message type
    [AgoraChatClient.sharedClient.chatManager sendMessage:message progress:nil completion:nil]; 

------------------------------------------------------------------------

## APNs push international configuration

The offline messages push of can support the following APNs' international configurations'
key：`title-loc-key`，`title-loc-args`，`loc-key`，`loc-args`

The official explanation about these keys:

-   title-loc-key（String）：The key for a localized title string.
    Specify this key instead of the title key to retrieve the title from
    your app's Localizable.strings files. The value must contain the
    name of a key in your strings file.

```{=html}
<!-- -->
```
-   title-loc-args（Array of strings）：An array of strings containing
    replacement values for variables in your title string. Each %@
    character in the string specified by the title-loc-key is replaced
    by a value from this array. The first item in the array replaces the
    first instance of the %@ character in the string, the second item
    replaces the second instance, and so on.

```{=html}
<!-- -->
```
-   loc-key（String）：The key for a localized message string. Use this
    key, instead of the body key, to retrieve the message text from your
    app\'s Localizable.strings file. The value must contain the name of
    a key in your strings file.

```{=html}
<!-- -->
```
-   loc-args（Array of strings）：An array of strings containing
    replacement values for variables in your message text. Each %@
    character in the string specified by loc-key is replaced by a value
    from this array. The first item in the array replaces the first
    instance of the %@ character in the string, the second item replaces
    the second instance, and so on.

[Apples offical development documents](https://developer.apple.com/documentation/usernotifications/setting_up_a_remote_notification_server/generating_a_remote_notification)

**Note：**

-   `title-loc-key` is `title-loc-args`  a pair，without `title-loc-key`
    ， `title-loc-args` will not work，If there is no variable parameter, you can just set
    `title-loc-key`;

```{=html}
<!-- -->
```
-   `loc-key` is `loc-args` a pair，without `loc-key` ， `loc-args`
    will not work，If there is no variable parameter, you can just set `loc-key`；

```{=html}
<!-- -->
```
-   You can also set only one of the above two pairs.

### iOS Client configuration

-   Configure the internationalization file `Localizable.strings` ，as configured in the demo
    `Localizable.strings (Chinese (Simplified))`、`Localizable.strings (English)`；

```{=html}
<!-- -->
```
-   Configure `title-loc-key` and `loc-key` in `Localizable.strings`
    And assign values, you need to use variable parameter string in the `title-loc-args` and `loc-args`
    Use `%@`  to replace it ; as follows:

configure in `Localizable.strings (Chinese (Simplified))` :

    "GAME_PLAY_REQUEST_FORMAT" = "%@ game invitaion %@";
    "GAME_PLAY_REQUEST" = "game invitaion";

configure in  `Localizable.strings (English)` 

    "GAME_PLAY_REQUEST_FORMAT" = "%@ GAME_PLAY_REQUEST_FORMAT %@";
    "GAME_PLAY_REQUEST" = "GAME_PLAY_REQUEST_CUSTOM";

### Set the message extension when sending a message

#### REST Send

    {
        "target_type" : "users",
        "target" : ["6006"],
        "msg" : {
            "type" : "txt",
            "msg" : "hello from rest"
            },
        "ext": {
            "em_apns_ext": {
                "em_push_content": "Custom push display", # You can set up a custom push display at the same time, and an app without configured loc-key will display "Custom Push Display"
                "em_push_title_loc_key" : "GAME_PLAY_REQUEST_FORMAT", # corresponds to title_loc_key
                "em_push_title_loc_args" : ["Shelly", "Rick"], # corresponds to title_loc_args
                "em_push_body_loc_key" : "GAME_PLAY_REQUEST_FORMAT", # corresponds to loc_key
                "em_push_body_loc_args" : ["Shelly", "Rick"] # corresponds to loc_args
            }
        },
        "from" : "6001"
    }

#### iOS send

    AgoraTextMessageBody *body = [[AgoraTextMessageBody alloc] initWithText:@"test"];
    AgoraMessage *message = [[AgoraMessage alloc] initWithConversationID:@"6006" from:@"6001" to:@"6006" body:body ext:nil];
    message.ext = @{@"em_apns_ext":@{@"em_push_title_loc_key":@"GAME_PLAY_REQUEST_FORMAT", @"em_push_title_loc_args":@[@"Shelly", @"Rick"], @"em_push_body_loc_key":@"GAME_PLAY_REQUEST_FORMAT", @"em_push_body_loc_args":@[@"Shelly", @"Rick"]}};
    message.chatType = AgoraChatTypeChat; // Set the message type
    [AgoraChatClient.sharedClient.chatManager sendMessage:message progress:nil completion:nil];

### parse

    { 
        "aps":{ 
            "alert":{ 
                "body":"Custom push display",
                "title-loc-key":"GAME_PLAY_REQUEST_FORMAT",
                "title-loc-args":["Shelly","Rick"],
            "loc-key":"GAME_PLAY_REQUEST_FORMAT", 
                "loc-args":["Shelly","Rick"] 
            },  
            "badge":1,  
            "sound":"default"   
        }, 
        "f":"6001", 
        "t":"6006", 
        "m":"373360335316321408"    
    } 

## Send silent message

（em_ignore_notification）

No APNs will be sent. After adding with sending a message, the message will not be pushed by APNs.

### REST send

（[REST sending messages](/im/server/basics/messages#发送扩展消息)）

    {
        "target_type":"users",
        "target":[
            "6006"
        ],
        "msg":{
            "type":"txt",
            "msg":"hello from rest"
        },
        "from":"6001",
        "ext":{
            "em_ignore_notification":true
        }
    }

------------------------------------------------------------------------

### iOS Send

([iOS sending messages](/im/ios/basics/message#构造扩展消息))

    AgoraTextMessageBody *body = [[AgoraTextMessageBody alloc] initWithText:@"test"];
    AgoraMessage *message = [[AgoraMessage alloc] initWithConversationID:@"6006" from:@"6001" to:@"6006" body:body ext:nil];
    message.ext = @{@"em_ignore_notification":@YES};
    message.chatType = AgoraChatTypeChat; // Set the message type
    // Send message example
    [AgoraChatClient.sharedClient.chatManager sendMessage:message progress:nil completion:nil];

------------------------------------------------------------------------

## Set up forced push APNs

（em_force_notification）

After set up, it will be forced to push the message, even if the client has set the do not disturb time, it will be pushed. Priority is lower than
em_ignore_notification. if em_ignore_notification is set at the same time
After that, the attribute will become invalid.

### REST Send

（[REST sending message）

    {
        "target_type":"users",
        "target":[
            "6006"
        ],
        "msg":{
            "type":"txt",
            "msg":"hello from rest"
        },
        "from":"6001",
        "ext":{
            "em_force_notification":true
        }
    }

------------------------------------------------------------------------

### iOS send

([iOS sending messages)

    AgoraTextMessageBody *body = [[AgoraTextMessageBody alloc] initWithText:@"test"];
    AgoraMessage *message = [[AgoraMessage alloc] initWithConversationID:@"6006" from:@"6001" to:@"6006" body:body ext:nil];
    message.ext = @{@"em_force_notification":@YES};
    message.chatType = AgoraChatTypeChat; // Set the message type
    // Send message example
    [AgoraChatClient.sharedClient.chatManager sendMessage:message progress:nil completion:nil];

------------------------------------------------------------------------

