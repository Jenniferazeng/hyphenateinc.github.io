---
title: iOS Connect To Chat Server
keywords: ios
sidebar: ios_sidebar
toc: true
permalink: ios_connect_to_chat_server.html
folder: ios
---

## Introduction to asynchronous/synchronous methods in SDK

In the SDK, most interfaces provide both synchronous and asynchronous methods (note: synchronous methods may block the main thread. The user need to create their own asynchronous threads to execute them; methods with block are asynchronous methods.)

## Initialize the SDK

**Required the initialization in the application's `oncreate` method**，Need to pass in the configured options when initializing.

``` objc
- (BOOL)application:(UIApplication *)application didFinishLaunchingWithOptions:(NSDictionary *)launchOptions
{
    //AppKey:The registered AppKey, see note below for details.
    //apnsCertName:Push the certificate name (no suffix required), see note below for details.
    AgoraOptions *options = [AgoraOptions optionsWithAppkey:@"douser#istore"];
    options.apnsCertName = @"istore_dev";
    [[AgoraChatClient sharedClient] initializeSDKWithOptions:options];

    return YES;
}


## create a user account

There are two registration modes, open registration and authorized registration. The client can register only when the it is open registration .

-   The open registration is for test . It is not recommended to use this method to register a chat account in a formal environment.
-   The authorization registration process should be that the server registers through the Platform API provided by Agora, and then saves it to your server or returns it to the client.

Registered user names will be automatically converted to lowercase letters. It is recommended that all user names are registered in lowercase. (It is strongly recommended that developers call the REST interface in the backend to register the chat user ID. The client registration method is not recommended.)

``` objc
    [[AgoraChatClient sharedClient] registerWithUsername:@"8001" password:@"111111" completion:^(NSString *aUsername, AgoraError *aError) {
    if (!aError) {
        NSLog(@"Register successfully");
    } else {
        NSLog(@"Reasons for registration failure---%@", aError.errorDescription);
    }
}];
```

## Login

**note：**

* Except for registering for monitoring, all other SDK operations can only be executed after logging in.
      
* It is recommended to use asynchronous login method to prevent the situation of stuck UI main thread when network is unstable.


``` objc
    [[AgoraChatClient sharedClient] loginWithUsername:@"8001" password:@"111111" completion:^(NSString *aUsername, AgoraError *aError) {
    if (!aError) {
        NSLog(@"Login successful");
    } else {
        NSLog(@"Reasons for login failure---%@", aError.errorDescription);
    }
}];
```

## Automatic login

Automatic Login: sdk will determine whether to execute auto-login based on isAutoLogin in options during initialization. If YES, sdk will directly log in the account that was last logged in and did not do the logout operation, if the value is NO, it will be the state that no account is logged in. **isAutoLogin is set as YES by default**.

Automatic login will be cancelled in the following situations:

-   The user invoked the logout action of the SDK;
-   The user changes the password on another device, which causes failure of the automatic login on this device;
-   The user's account is deleted from the server;
-   The user logs in from another device to kick out the user logged in on the current device.

``` objc
/*!
 *  This callback will be received when the current login has been deleted from the server side
 */
- (void)userAccountDidRemoveFromServer;
```

-   The user's account is banned

``` objc
/*!
 *  Service is banned
 */
- (void)userDidForbidByServer;
```

-   The user's account was forcibly taken offline from the server side
-   The user logs in on another device which kicks out the user from the current device
-   The user changed the password on another device

``` objc
/*!
 *  This callback is received when the currently logged-in account is forced to log out, for the following reasons：
 *    1.Password has been changed；
 *    2.Too many logged-in devices；
 *    3.Forced to be taken offline from the server side；
 */
- (void)userAccountDidForcedToLogout:(AgoraError *)aError;
```

In the SDK, if an automatic login occurs, the following callbacks will be made：

``` objc
/*!
 *  Automatic login return results
 *
 *  @param error Error message
 */
- (void)autoLoginDidCompleteWithError:(AgoraError *)error

//Add a callback listener proxy: [[AgoraChatClient sharedClient] addDelegate:self delegateQueue:nil];
```

## logout


``` objc
[[AgoraChatClient sharedClient] logout:YES completion:^(AgoraError *aError) {
    if (!aError) {
        NSLog(@"Log out successfully");
    } else {
        NSLog(@"Reasons for logout failure---%@", aError.errorDescription);
    }
}];
```

## Registered connection status listening
When the connection is dropped, the iOS SDK will automatically reconnect, just listen to the reconnection-related callbacks, no need to do anything.

``` objc
/*!
 *  The callback is received when the status of the connection between SDK and server changes
 *
 *  There are several cases that will cause this method to be called：
 *  1. This callback will be called when the phone cannot access the Internet after successful login
 *  2. This callback will be called when the network status changes after successful login
 *
 *  @param aConnectionState Current Status
 */
- (void)connectionStateDidChange:(AgoraConnectionState)aConnectionState;
```

## Exporting information to log files

The user can export the required information to the SDK log file in the app. This interface is supported since SDK 3.8.1 and needs to be called after completing the initialization of SDK. The call method is as follows

    [[AgoraChatClient sharedClient] log:@"Initialization is completed here"];

------------------------------------------------------------------------
