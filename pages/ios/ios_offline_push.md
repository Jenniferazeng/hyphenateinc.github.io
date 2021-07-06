---
title:  Offline Push
keywords: ios
sidebar: ios_sidebar
toc: true
permalink: ios_offline_push.html
folder: ios
---

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

------------------------------------------------------------------------
