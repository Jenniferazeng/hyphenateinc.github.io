---
title: ios Group
keywords: ios
sidebar: ios_sidebar
toc: true
permalink: ios_multi_device.html
folder: ios
---
# iOS SDK's Introduction and import

------------------------------------------------------------------------

## DEMO（ChatDemo-UI3.0 App） experience


Download link：[download page](http://www.easemob.com/download/im)

# Multiple devices

------------------------------------------------------------------------

The main SDK header files involved in multiple devices are as follows:

``` objc
// The information part of the logged-in device, including the UUID of the device, the device name, etc.
AgoraDeviceConfig.h

// Multi-device method call, such as obtaining all logged-in device information from the server, forcing the specified device to log out, etc.
AgoraChatClient.h

// Multi-device protocol callback methods, such as monitoring friend multi-device events, group multi-device event callback methods, etc.
AgoraMultiDevicesDelegate.h
```

## Device ID log in on the other terminal

When the PC terminal and the mobile terminal log in to the same account, the device ID of the PC terminal can be obtained by a specific method on the mobile terminal. The device ID can be accepted as a special friend Username and can be used directly for chat, and the usage method is similar to a friend.

#### Interface

``` objc
/*!
 *  get information of all logged-in device from the server
 *
 *  @param aUsername        User name 
 *  @param aPassword        password
 *  @param aCompletionBlock the completed callback
 *  @result aList           Information of logged in device <AgoraDeviceConfig>
 */
- (void)getLoggedInDevicesFromServerWithUsername:(NSString *)aUsername
                                        password:(NSString *)aPassword
                                      completion:(void (^)(NSArray *aList, AgoraError *aError))aCompletionBlock;
```

#### Example of use

``` objc
[[AgoraChatClient sharedClient] getLoggedInDevicesFromServerWithUsername:@"username" password:@"password" completion:^(NSArray *aList, AgoraError *aError) {
    if (!aError) {
        // The returned aList array contains the AgoraDeviceConfig object. AgoraDeviceConfig is the information of the logged-in device. For details, please check the introduction in the AgoraDeviceConfig.h header file of the Easemob SDK
        NSLog(@"Successfully get all logged-in device information from the server --- %@", aList);
    } else {
        NSLog(@"Successfully get reason for failure information of all logged-in device  from the server --- %@", aError.errorDescription);
    }
}];
```

## Multi-device event callback

Account A is logged in to device A and device B at the same time. Account A performs some operations on device A. Device B will receive notifications corresponding to these operations. The specific description are as follows:

#### Interface AgoraMultiDevicesDelegate

``` objc
/*!
 * Multi-device event type
 * UserA, log in to two devices DeviceA1 and DeviceA2, and UserB
 */
typedef NS_ENUM(NSInteger, AgoraMultiDevicesEvent) {
    AgoraMultiDevicesEventUnknow = -1, // default
    AgoraMultiDevicesEventContactRemove = 2, // UserB and UserA are friends, UserA deletes UserB on DeviceA1, DeviceA2 will receive the callback
    AgoraMultiDevicesEventContactAccept = 3, // UserB sends a friend request to UserA, UserA agrees to the request on DeviceA1, and DeviceA2 will receive the callback
    AgoraMultiDevicesEventContactDecline = 4, // UserB sends a friend request to UserA, UserA rejects the request on DeviceA1, DeviceA2 will receive the callback
    AgoraMultiDevicesEventContactBan = 5, // UserA adds UserB to the blacklist on DeviceA1, and DeviceA2 will receive the callback
    AgoraMultiDevicesEventContactAllow = 6, // UserA removes UserB from the blacklist on DeviceA1, and DeviceA2 will receive the callback
    
    AgoraMultiDevicesEventGroupCreate = 10, // UserA creates a group Group on DeviceA1, DeviceA2 will receive the callback
    AgoraMultiDevicesEventGroupDestroy = 11, // UserA destroyed the group Group on DeviceA1, DeviceA2 will receive the callback
    AgoraMultiDevicesEventGroupJoin = 12, // UserA actively joined the group Group on DeviceA1, DeviceA2 will receive the callback
    AgoraMultiDevicesEventGroupLeave = 13, // UserA left the group on DeviceA1, DeviceA2 will receive the callback
    AgoraMultiDevicesEventGroupApply = 14, // UserA sends an application to enter the group on DeviceA1, and DeviceA2 will receive the callback
    AgoraMultiDevicesEventGroupApplyAccept = 15, // UserA receives UserB’s application to join the group, UserA agrees to the application on DeviceA1, and DeviceA2 will receive the callback
    AgoraMultiDevicesEventGroupApplyDecline = 16, // UserA receives UserB's application to join the group, UserA rejects the application on DeviceA1, DeviceA2 will receive the callback
    AgoraMultiDevicesEventGroupInvite = 17, // UserA invited some people into GroupA on DeviceA1, DeviceA2 will receive the callback
    AgoraMultiDevicesEventGroupInviteAccept = 18, //e UserBUserA joins the group, UserA agrees to UserB's invitation on DeviceA1, DeviceA2 will receive the callback
    AgoraMultiDevicesEventGroupInviteDecline = 19, // UserB invites UserA to join the group, UserA rejects UserB's invitation on DeviceA1, DeviceA2 will receive the callback
    AgoraMultiDevicesEventGroupKick = 20, // UserA kicked some members from GroupA on DeviceA1, DeviceA2 will receive the callback
    AgoraMultiDevicesEventGroupBan = 21, // UserA adds some members to the GroupA blacklist on DeviceA1, DeviceA2 will receive the callback
    AgoraMultiDevicesEventGroupAllow = 22, // UserA removes some members from the GroupA blacklist on DeviceA1, DeviceA2 will receive the callback
    AgoraMultiDevicesEventGroupBlock = 23, // UserA blocked GroupA's message on DeviceA1, DeviceA2 will receive the callback
    AgoraMultiDevicesEventGroupUnBlock = 24, // UserA unblocked the message of GroupA on DeviceA1, DeviceA2 will receive the callback
    AgoraMultiDevicesEventGroupAssignOwner = 25, // UserA updates the owner of GroupA on DeviceA1, DeviceA2 will receive the callback
    AgoraMultiDevicesEventGroupAddAdmin = 26, // UserA adds the administrator of GroupA to DeviceA1, DeviceA2 will receive the callback
    AgoraMultiDevicesEventGroupRemoveAdmin = 27, // UserA removed the administrator of GroupA on DeviceA1, DeviceA2 will receive the callback
    AgoraMultiDevicesEventGroupAddMute = 28, // UserA muted some members of GroupA on DeviceA1, DeviceA2 will receive the callback
    AgoraMultiDevicesEventGroupRemoveMute = 29, // UserA removed some banned members of GroupA on DeviceA1, DeviceA2 will receive the callback
};

/*!
 *  Friends multi-device event callback
 *
 *  @param aEvent       Multi-device event type
 *  @param aUsername    username
 *  @param aExt         extended information
 */
- (void)multiDevicesContactEventDidReceive:(AgoraMultiDevicesEvent)aEvent
                                  username:(NSString *)aUsername
                                       ext:(NSString *)aExt;

/*!
 *  Group multi-device event callback
 *
 *  @param aEvent       Multi-device event type
 *  @param aGroupId     group ID
 *  @param aExt         extended information, which is the array of objects used(NSMutableArray)
 */
- (void)multiDevicesGroupEventDidReceive:(AgoraMultiDevicesEvent)aEvent
                                 groupId:(NSString *)aGroupId
                                     ext:(id)aExt;

```

#### Example

``` objc
//Register to listen
[[AgoraChatClient sharedClient] addMultiDevicesDelegate:aDelegate delegateQueue:aQueue];

//Listen for callbacks
- (void)multiDevicesContactEventDidReceive:(AgoraMultiDevicesEvent)aEvent
                                  username:(NSString *)aTarget
                                       ext:(NSString *)aExt
{
    NSString *message = [NSString stringWithFormat:@"%li-%@-%@", (long)aEvent, aTarget, aExt];
    UIAlertView *alertView = [[UIAlertView alloc] initWithTitle:NSLocalizedString(@"alert.multi.contact", @"Contact Multi-devices") message:message delegate:self cancelButtonTitle:NSLocalizedString(@"ok", @"OK") otherButtonTitles:nil, nil];
    [alertView show];
}

- (void)multiDevicesGroupEventDidReceive:(AgoraMultiDevicesEvent)aEvent
                                 groupId:(NSString *)aGroupId
                                     ext:(id)aExt
{
    NSString *message = [NSString stringWithFormat:@"%li-%@-%@", (long)aEvent, aGroupId, aExt];
    UIAlertView *alertView = [[UIAlertView alloc] initWithTitle:NSLocalizedString(@"alert.multi.group", @"Group Multi-devices") message:message delegate:self cancelButtonTitle:NSLocalizedString(@"ok", @"OK") otherButtonTitles:nil, nil];
    [alertView show];
}

```

------------------------------------------------------------------------



