---
title: iOS Push Notification
keywords: ios
sidebar: ios_sidebar
toc: true
permalink: ios_push_notification.html
folder: ios
---
# iOS SDK's Introduction and import

------------------------------------------------------------------------

## DEMO（ChatDemo-UI3.0 App） experience


Download link：[download page](http://www.easemob.com/download/im)

##  APNs Push configuration

## SDK running status

-   When the App is visible in the foreground, the SDK is active in the foreground. The SDK is used  persistent connection to receive messages at this time.

```{=html}
<!-- -->
```
-   When the App enters the background and within 2 minutes, the SDK is active in the background. The SDK is used  persistent connection to receive messages at this time. The user implements local notification depending on their requirements, otherwise there will be no local notification pop-up).

```{=html}
<!-- -->
```
-   When the App enters the background for more than 2 minutes, it is suspended by the system. At this time, the SDK is in an inactive state, or the App process is actively killed. If there is a new message at this time, it  is reminded through Apple‘s APNs service. When the App is launched again, the SDK will go to the server to fetch the messages during the inactive period.

Note：**`Since local notifications are hard to distinguish from APNs, it is recommended that you should kill the App process when debugging to ensure that all notifications come from APNs pushes.`

## Message push process

### When the SDK is in the foreground or background active state:

![](/im/ios/apns/image006.png){.align-center}

### When the SDK is inactive or the App process is killed:

![](/im/ios/apns/image007.png){.align-center}

`APNs only work as the notifications here. When the user launched the App, the message will be received by clients through the SDK persistent connection. `

## Configuration push

### Apply for remote push certificate

First, log in to Apple's Developer Center. Create App
![](/im/ios/apns/apns_setting_1.jpg){width="800"}
![](/im/ios/apns/apns_setting_2.jpg){width="800"}
![](/im/ios/apns/apns_setting_3.jpg){width="800"}

Name the App. Wildcards cannot be used for the bundle id here, otherwise the push will not be received.

![](/im/ios/apns/apns_setting_4.jpg)

enable push function

![](/im/ios/apns/apns_setting_5.jpg)

![](/im/ios/apns/apns_setting_6.jpg)

Create push certificate

![](/im/ios/apns/apns_setting_7.jpg)

If you are in a test development environment, select Apple Push Notification service SSL (Sandbox) under Services. If it is in a production environment, you need to select Apple Push Notification service SSL(Sandbox & Production) under Services.

![](/im/ios/apns/apns_setting_8.jpg)

Select the App which the certificate belongs to

![](/im/ios/apns/apns_setting_9.jpg)

Upload CSR file

![](/im/ios/apns/apns_setting_10.jpg)

Next, let’s create a CSR file. First, select \"Keychain Access\"

![](/im/300iosclientintegration/apns_create9.jpg){width="800"}

Keychain Access \-- Certificate Assistant \-- Request a certificate from a certificate authority

![](/im/300iosclientintegration/apns_create10.jpg){width="800"}

There is no requirement for e-mail, just make sure is in a normal e-mail format, and there is no restriction on name. Please pay attention to change the parameter of "request is" to **"store to disk"**. After that, you will get a CSR file afterwards.

![](/im/300iosclientintegration/apns_create11.jpg){width="800"}

Return to the "Upload CRS File" page and upload the CSR file just generated.

![](/im/300iosclientintegration/apns_create12.jpg){width="800"}

Click on Continue and there will be a download page.

![](/im/ios/apns/apns_setting_11.jpg)

![](/im/ios/apns/apns_setting_12.jpg)

Click download, and an aps_development.cer will be downloaded. (aps.cer for production environment)

![](/im/300iosclientintegration/apns_create15.jpg){width="800"}

Double-click the cer file just downloaded and it will be added to "Keychain Access". Check the certificate, you will see a bundle id certificate, this is what's newly added.

![](/im/300iosclientintegration/apns_create16.jpg){width="800"}

`Right-click to export, and be careful not to expand when you right-click, please just right-click on the certificate`

![](/im/300iosclientintegration/apns_create17.jpg){width="800"}

Save

![](/im/300iosclientintegration/apns_create18.jpg){width="800"}

Please remember the password here, and it will be used later.
`Note: When exporting a p12 certificate, the password length of the certificate should not be set to exceed 20 characters. It is recommended to use a combination of pure English or numbers. Special characters are not recommended.`

![](/im/300iosclientintegration/apns_create19.jpg){width="800"}

Allow keychain to access this item

![](/im/300iosclientintegration/apns_create20.jpg){width="800"}

Save, and you will get a file with a suffix of p12

![](/im/300iosclientintegration/apns_create21.jpg){width="800"}

### Upload the push certificate to Easemob

Log in to the Easemob management background, find the Appkey of certificate which you want to upload, and click to enter and see more details.

![](/im/ios/apns/3_.png)

![](/im/ios/apns/Application details.png)

Select **"Certificate"**

![](/im/ios/apns/Certificate management.png)

Choose **"Apple"**

![](/im/ios/apns/Upload certificate.png)

Give the certificate a name, and remember the name, it will be useful later. Select Upload Certificate. Upload the P12 file generated in the previous step, and set the password set during export. Select the certificate type, here is [Development Environment] (if you used production before, you should select production here). Fill in the package name, which should be **bundle id**. Click Upload to complete the uploading certificate operation.

`Note: The length of the certificate name and password should not exceed 20 characters. It is recommended to use a combination of pure English or numbers. Special characters are not recommended.`

![](/im/ios/apns/ios certificate.png)

### How does the client apply for DeviceToken

1、 notification for remote Registration 

    if ([application respondsToSelector:@selector(registerUserNotificationSettings:)]) {
        //Register push, for iOS8 and above
        UIUserNotificationSettings *settings = [UIUserNotificationSettings settingsForTypes:(UIUserNotificationTypeBadge | UIUserNotificationTypeSound | UIUserNotificationTypeAlert) categories:nil];
        [application registerUserNotificationSettings:settings];
    } else {
        //Register push, for iOS8 and below
        UIRemoteNotificationType myTypes = UIRemoteNotificationTypeBadge | UIRemoteNotificationTypeAlert | UIRemoteNotificationTypeSound;
        [application registerForRemoteNotificationTypes:myTypes];
    }
        
    - (void)application:(UIApplication *)application didRegisterUserNotificationSettings:(UIUserNotificationSettings *)notificationSettings {
        [application registerForRemoteNotifications];
    }

2、 Pass the obtained deviceToken to SDK

`If it is iOS13 and above, please update SDK to v3.6.4 or above`

    // Pass the obtained deviceToken to SDK
    - (void)application:(UIApplication *)application didRegisterForRemoteNotificationsWithDeviceToken:(NSData *)deviceToken
    {
        dispatch_async(dispatch_get_global_queue(DISPATCH_QUEUE_PRIORITY_DEFAULT, 0), ^{
            [[AgoraChatClient sharedClient] bindDeviceToken:deviceToken];
        });
    }

**Note: It must be a real OS, the simulator does not support APNs. APNs registration failure is usually caused by the use of a general certificate or simulator debugging mode. Please check the certificate and debug with a real OS. Here is the error reported by the iOS system. If you are not sure with it, please search for relevant information on the Internet. **

### How to configure the push certificate on the client

When the SDK is initialized, set the push certificate which you going to be used.

    // set Appkey
    AgoraOptions *options = [AgoraOptions optionsWithAppkey:@"easemob-demo#chatdemoui"];
    // Set the name of the push certificate
    options.apnsCertName = @"apnsTest";
    // Initialize SDK
    [[AgoraChatClient sharedClient] initializeSDKWithOptions:options];

## Common problem

### How to implement local notification

When the persistent connection still exists, Local notification  `receive the message through the callback of the Easemob receiving message`, and then judge the current App status, if it is in the background, you can use the code to actively pop up a notification  to notify the user. The specific reference is as follows:

        - (void)messagesDidReceive:(NSArray *)aMessages {
            for (AgoraMessage *msg in aMessages) {
                UIApplicationState state = [[UIApplication sharedApplication] applicationState];
                // App in the background
                if (state == UIApplicationStateBackground) {
                        //Send local push
                     if (NSClassFromString(@"UNUserNotificationCenter")) { // ios 10
                    // Set the trigger time
                    UNTimeIntervalNotificationTrigger *trigger = [UNTimeIntervalNotificationTrigger triggerWithTimeInterval:0.01 repeats:NO];
                    UNMutableNotificationContent *content = [[UNMutableNotificationContent alloc] init];
                    content.sound = [UNNotificationSound defaultSound];
                    // notification can be popped up as needed, such as displaying the details of the message, or displaying "You have a new message"
                    content.body = @"content of notification";
                    UNNotificationRequest *request = [UNNotificationRequest requestWithIdentifier:msg.messageId content:content trigger:trigger];
                    [[UNUserNotificationCenter currentNotificationCenter] addNotificationRequest:request withCompletionHandler:nil];
                }else {
                    UILocalNotification *notification = [[UILocalNotification alloc] init];
                    notification.fireDate = [NSDate date]; //The time the notification was triggered
                    notification.alertBody = @"content of notification";
                    notification.alertAction = @"Open";
                    notification.timeZone = [NSTimeZone defaultTimeZone];
                    notification.soundName = UILocalNotificationDefaultSoundName;
                    [[UIApplication sharedApplication] scheduleLocalNotification:notification];
                }
            }
        }
    }

### Parse of push content 

In the Easemob, APNs are only generated when the persistent connection does not exist (the App process does not exist or is suspended). At this time, iOS will not be allowed direct access to the APNs content, but when the user clicks on the push notification bar, it can be found in launchOptions from

    - (BOOL)application:(UIApplication *)application didFinishLaunchingWithOptions:(NSDictionary *)launchOptions;

The specific structure is as follows：

    {
        "aps":{
            "alert":{
                "body":"you got a new message" // content of message
            },   
            "badge":1,  // number of corner marks
            "sound":"default"   // alert sound   
         },
        "f":"6001", // sender
        "t":"6006", // recevier     
        "m":"373360335316321408"， // message id
        "g":"1421300621769"  // group id (There is no such field, if it is a single chat)
     }

For more usage, please refer to（[APNs parse](/im/ios/apns/content)）

### How to renew the certificate

After the certificate expires, if you want to renew the push certificate, you need to delete the old one in the Easemob management background, and then upload it again. `The name of the upload should be the same as the name of the old certificate`.

### Can multiple sets of push certificates be delivered under one Appkey

Multiple certificates can be transferred under one appkey, which can implement app chat. When initializing SDK in different apps, specify different push certificates, and each certificate corresponds to the bundle id of the current app. In this way, users are logging in to different apps. It will be bound to different push certificates to implement the chat and push of the app.


## APNs Offline push

------------------------------------------------------------------------

## Prerequisites

1\. The push certificate is uploaded in the background. See details in [Integrate iOS SDK
Preparatory work-create and upload a push certificate](/start/300iosclientintegration/10prepareforsdkimport#制作并上传推送证书)。

2\. The push certificate used by the code configures APNs.

``` objc
AgoraOptions *options = [AgoraOptions optionsWithAppkey:@"appkey"];
options.apnsCertName = @"apnsCertName";
[[AgoraChatClient sharedClient] initializeSDKWithOptions:options];
```

3\. Code registration offline push.

``` objc
UIApplication *application = [UIApplication sharedApplication];

//iOS10 register APNs
if (NSClassFromString(@"UNUserNotificationCenter")) {
    [[UNUserNotificationCenter currentNotificationCenter] requestAuthorizationWithOptions:UNAuthorizationOptionBadge | UNAuthorizationOptionSound | UNAuthorizationOptionAlert completionHandler:^(BOOL granted, NSError *error) {
        if (granted) {
#if !TARGET_IPHONE_SIMULATOR
            [application registerForRemoteNotifications];
#endif
        }
    }];
    return;
}

if([application respondsToSelector:@selector(registerUserNotificationSettings:)])
{
    UIUserNotificationType notificationTypes = UIUserNotificationTypeBadge | UIUserNotificationTypeSound | UIUserNotificationTypeAlert;
    UIUserNotificationSettings *settings = [UIUserNotificationSettings settingsForTypes:notificationTypes categories:nil];
    [application registerUserNotificationSettings:settings];
}

#if !TARGET_IPHONE_SIMULATOR
if ([application respondsToSelector:@selector(registerForRemoteNotifications)]) {
    [application registerForRemoteNotifications];
}else{
    UIRemoteNotificationType notificationTypes = UIRemoteNotificationTypeBadge |
    UIRemoteNotificationTypeSound |
    UIRemoteNotificationTypeAlert;
    [[UIApplication sharedApplication] registerForRemoteNotificationTypes:notificationTypes];
}
#endif
```

After you registered the push function, iOS will automatically call back the following methods to get the deviceToken. you need to pass the deviceToken to SDK。

`If it is iOS13 and above, please update SDK to v3.6.4 and above.`

``` objc
// Pass the obtained deviceToken to SDK
- (void)application:(UIApplication *)application didRegisterForRemoteNotificationsWithDeviceToken:(NSData *)deviceToken
{
    dispatch_async(dispatch_get_global_queue(DISPATCH_QUEUE_PRIORITY_DEFAULT, 0), ^{
        [[AgoraChatClient sharedClient] bindDeviceToken:deviceToken];
    });
}

// Failed to register deviceToken
- (void)application:(UIApplication *)application didFailToRegisterForRemoteNotificationsWithError:(NSError *)error{
    NSLog(@"error -- %@",error);
}
```

**Note: It must be a real OS, the simulator does not support APNs. APNs registration failure is usually caused by the use of a general certificate or simulator debugging mode. Please check the certificate and debug with a real OS. Here is the error reported by the iOS system. If you are not sure with it, please search for relevant information on the Internet. **

## Get APNs configuration

The APNs attribute needs to be obtained from the server <u>obtaining side</u>.

It will be called after the user login successfully

``` java
/*!
 *  \~chinese
 *  Get push attributes from the server
 *
 *  Synchronous method, will block the current thread
 *
 *  @param pError  error infomation--------
 *
 *  @result Push attributes
 */
- (AgoraPushOptions *)getPushOptionsFromServerWithError:(AgoraError **)pError;

/*!
 *  \~chinese
 *  Get push attributes from the server
 *
 *  @param aCompletionBlock the Completed callback
 */
- (void)getPushNotificationOptionsFromServerWithCompletion:(void (^)(AgoraPushOptions *aOptions, AgoraError *aError))aCompletionBlock;
```

示例代码：

``` java
AgoraError *err;
AgoraPushOptions *options = [AgoraChatClient.sharedClient.pushManager getPushOptionsFromServerWithError:&err];
if (err) {
  // Get failed

}else {
  // Get successfully
}

```

## Set APNs display name

When you send a message to the other party and the other party is not online, the sender displays the name you set in the push;

It will be called after the user login successfully。

``` java
/*!
 *  \~chinese
 *  Set the name displayed in the push message
 *
 *  Synchronous method, will block the current thread
 *
 *  @param aNickname  Set the name you want
 *
 *  @result error information
 */
- (AgoraError *)updatePushDisplayName:(NSString *)aDisplayName;

/*!
 *  \~chinese
 *  Set the display name of the push
 *
 *  @param aDisplayName     push display name
 *  @param aCompletionBlock the Completed callback
 */
- (void)updatePushDisplayName:(NSString *)aDisplayName
                   completion:(void (^)(NSString *aDisplayName, AgoraError *aError))aCompletionBlock;
```

code example

``` java
AgoraError *err = [AgoraChatClient.sharedClient.pushManager updatePushDisplayName:@"Push Nickname"];
if (err) {
  // Get failed
}else {
  // Get successfully
}
```

## Set APNs display style

When you are not online, if someone sends you a message, you will receive a push. You can set the display details (xxx: Message content), or only show new messages (you have a new message);

``` java
/*!
 *  \~chinese 
 *  Display style of push messages
 */
typedef enum {
    AgoraPushDisplayStyleSimpleBanner = 0, /*! 
                                         *  Simply display "You got a new message"
                                         */

    AgoraPushDisplayStyleMessageSummary,   /*! 
                                         *  display message content
                                         */
} AgoraPushDisplayStyle;

```

``` java
/*!
 *  \~chinese
 *  Set the display style of push messages
 *
 *  Synchronous method, will block the current thread
 *
 *  @param pushDisplayStyle  Push style to be set
 *
 *  @result Error information
 */
- (AgoraError *)updatePushDisplayStyle:(AgoraPushDisplayStyle)pushDisplayStyle;


/*!
 *  \~chinese
 *  Set the display style of the push
 *
 *  @param pushDisplayStyle     Push display style
 *  @param aCompletionBlock     the Completed callback
 */
- (void)updatePushDisplayStyle:(AgoraPushDisplayStyle)pushDisplayStyle
                    completion:(void (^)(AgoraError *))aCompletionBlock;
```

code example

``` java
// Set to "You got a new message"
AgoraError *err = [AgoraChatClient.sharedClient.pushManager updatePushDisplayStyle:AgoraPushDisplayStyleSimpleBanner];
if (err) {
    // Set up failed
}else {
    // Set up successfully
}
```

## Set Do Not Disturb Time

When you don’t want to receive offline pushes during certain periods of time，You can set the do not disturb time period. After set up, within the time period you specify, Easemob will not send you offline pushes.
This setting has the highest priority. When it is set, the push of group and single chat cannot be received within the specified time period.

Enable offline push

``` java
/*!
 *  \~chinese
 *  Enable offline push
 *
 *  Synchronous method, will block the current thread
 *
 *  @result error information
 *
 */
- (AgoraError *)enableOfflinePush;
```

code example

``` java
AgoraError *err = [AgoraChatClient.sharedClient.pushManager enableOfflinePush];
if (err) {
    // Set up failed
}else {
    // Set up successfully
}
```

Set the specified time not to receive offline push

``` java
/*!
 *  \~chinese
 *  disable offline push
 *
 *  Synchronous method, will block the current thread
 *
 *  @param aStartHour    Starting time
 *  @param aEndHour      End Time
 *
 *  @result              error information
 */
- (AgoraError *)disableOfflinePushStart:(int)aStartHour end:(int)aEndHour;

```

code example

``` java
// If you don’t want to receive push notifications throughout the whole day，start:0, end:24;
// If you want to not receive push notifications from 7 am to 5 pm，start:7, end:17;
// If you want to not receive push notifications from 10pm to 8am，start:22, end:8;
AgoraError *err = [AgoraChatClient.sharedClient.pushManager disableOfflinePushStart:0 end:24];
if (err) {
    // Set up failed
}else {
    // Set up successfully
}
```

## Set group do not disturb

When you don’t want to receive offline push from a specific group, you can set the group to do not disturb

``` java
/*!
 *  \~chinese
 *  Set whether the group receives push
 *
 *  Synchronous method, will block the current thread
 *
 *  @param aGroupIds    Group ID
 *  @param disable      receives push or not
 *
 *  @result             error information
 */
- (AgoraError *)updatePushServiceForGroups:(NSArray *)aGroupIds
                            disablePush:(BOOL)disable;


/*!
 *  \~chinese
 *  Set whether the group receives push
 *
 *  @param aGroupIds            Group ID
 *  @param disable              receives push or not
 *  @param aCompletionBlock     the Completed callback
 */
- (void)updatePushServiceForGroups:(NSArray *)aGroupIds
                       disablePush:(BOOL)disable
                        completion:(void (^)(AgoraError *))aCompletionBlock;
```

code example

``` java
// do not receive group pushes from a group id of 82000139.
AgoraError *err = [AgoraChatClient.sharedClient.pushManager updatePushServiceForGroups:@[@"82000139"] disablePush:YES];
if (err) {
   // Set up failed
}else {
   // Set up successfully
}
```

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


