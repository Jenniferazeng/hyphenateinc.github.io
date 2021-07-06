---
title:  Contact
keywords: ios
sidebar: ios_sidebar
toc: true
permalink: ios_relationship.html
folder: ios
---

# Friend management 

------------------------------------------------------------------------

Note: In the Easemob, if you are not a friend, you can also chat. It is not recommended to use the friend mechanism of Easemob. If you have your own server or friend mechanism, please maintain the friend mechanism yourself.

The Easemob SDK header files involved in friend management are as follows:

``` objc
// Friend method call, such as adding agent, removing agent, adding, deleting friend, etc.
IAgoraContactManager.h

// The callback method of the friend's protocol, such as the callback method for listening and receiving friend requests, etc.
AgoraContactManagerDelegate.h
```

## Query friends list

To query the list of friends, Easemob provides two methods.

### Get all friends from the server

``` objc
[[AgoraChatClient sharedClient].contactManager getContactsFromServerWithCompletion:^(NSArray *aList, AgoraError *aError) {
    if (!aError) {
        NSLog(@"Get all friends successfully -- %@",aList);
    } else {
        NSLog(@"Reason for failure to get all friends --- %@", aError.errorDescription);
    }
}];
```

#### Get all friends from the database

``` objc
// After getting all friends from the server, you can get friends from the local
NSArray *userlist = [[AgoraChatClient sharedClient].contactManager getContacts];
```

## Friend requests

### Send friend request

Easemob iOS SDK provides a method to add friends.

``` objc
/*!
 *  add friend
 *
 *  @param aUsername        the user to be added
 *  @param aMessage         invitation message
 *  @param aCompletionBlock the completed callback
 */
- (void)addContact:(NSString *)aUsername
           message:(NSString *)aMessage
        completion:(void (^)(NSString *aUsername, AgoraError *aError))aCompletionBlock;
        
// calling        
[[AgoraChatClient sharedClient].contactManager addContact:@"6001" message:@"I want to add you as a friend" completion:^(NSString *aUsername, AgoraError *aError) {
    if (!aError) {
        NSLog(@"Add friend successfully -- %@",aUsername);
    } else {
        NSLog(@"Reasons for failure to add friends --- %@", aError.errorDescription);
    }
}];
```

### Listen to add friend requests

When you receive a friend request, if you do not process it, please save the data by yourself. It will not be sent every time under the new protocol.

``` objc
protocal:AgoraContactManagerDelegate

proxy:
//Registered friend callback
[[AgoraChatClient sharedClient].contactManager addDelegate:self delegateQueue:nil];
//Remove friend callback
[[AgoraChatClient sharedClient].contactManager removeDelegate:self];
```

监听回调

``` objc
/*!
 *  User A sends a friend application to user B, and user B will receive this callback
 *
 *  @param aUsername   username
 *  @param aMessage    attachment information
 */
- (void)friendRequestDidReceiveFromUser:(NSString *)aUsername
                                message:(NSString *)aMessage; 
```

### Agree to friend request

``` objc
/*!
 *  Agree to the friend request
 *
 *  @param aUsername        applicant
 *  @param aCompletionBlock the Completed callback

 */
- (void)approveFriendRequestFromUser:(NSString *)aUsername
                          completion:(void (^)(NSString *aUsername, AgoraError *aError))aCompletionBlock;
                          
// calling:                          
[[AgoraChatClient sharedClient].contactManager approveFriendRequestFromUser:@"6001" completion:^(NSString *aUsername, AgoraError *aError) {
    if (!aError) {
        NSLog(@"Agree to the friend request successfully");
    } else {
        NSLog(@"Reasons for the failure of request to add friends --- %@", aError.errorDescription);
    }
}];
```

### Refuse to friend request

``` objc
/*!
 *  Reject the request to add friends
 *
 *  @param aUsername        applicant
 *  @param aCompletionBlock the completed request
 */
- (void)declineFriendRequestFromUser:(NSString *)aUsername
                          completion:(void (^)(NSString *aUsername, AgoraError *aError))aCompletionBlock;
                          
// calling:
[[AgoraChatClient sharedClient].contactManager declineFriendRequestFromUser:@"6001" completion:^(NSString *aUsername, AgoraError *aError) {
    if (!aError) {
        NSLog(@"Refuse to friend request successfully");
    } else {
        NSLog(@"Reasons for failure of the application for friends --- %@", aError.errorDescription);
    }
}];                          
```

### Result of friend request callback

Listen to callbacks

``` objc
/*!
 @method
 @brief User A sends an friend request to user B. After user B agrees, user A will receive this callback
 */
- (void)friendRequestDidApproveByUser:(NSString *)aUsername;

/*!
 @method
 @brief User A sends an friend request to user B. After user B refuses, user A will receive this callback

 */
- (void)friendRequestDidDeclineByUser:(NSString *)aUsername;
```

## delete friend

``` objc
/*!
 *  delete friend
 *
 *  @param aUsername                Friend to delete
 *  @param aIsDeleteConversation    Whether to delete the conversation
 *  @param aCompletionBlock         The completed callback
 */
- (void)deleteContact:(NSString *)aUsername
 isDeleteConversation:(BOOL)aIsDeleteConversation
           completion:(void (^)(NSString *aUsername, AgoraError *aError))aCompletionBlock;
           
// calling:
[[AgoraChatClient sharedClient].contactManager deleteContact:@"6001" isDeleteConversation:YES completion:^(NSString *aUsername, AgoraError *aError) {
    if (!aError) {
        NSLog(@"deleted friends successfully");
    } else {
        NSLog(@"Reasons for failure to delete friends --- %@", aError.errorDescription);
    }
}];           
```

### Delete friend callback

Listen to callbacks

``` objc
/*!
 @method
 @brief After user B deletes the friend of user A, users A and B will receive this callback
*/
- (void)friendshipDidRemoveByUser:(NSString *)aUsername;
```

## Blacklist

### Query friends blacklist

Easemob's blacklist system is independent and not related to friends. In other words, you can add anyone to the blacklist, regardless of whether he is a friend of you. At the same time, if you add a friend to the blacklist, he is still your friend, but he is also on the blacklist at the same time.

To query the blacklist list, Easemob provides two methods.

#### Asynchronous method

``` objc
/*!
 *  Get the blacklist list from the server
 *
 *  @param aCompletionBlock the Completed callback
 */
- (void)getBlackListFromServerWithCompletion:(void (^)(NSArray *aList, AgoraError *aError))aCompletionBlock;

// Calling:
[[AgoraChatClient sharedClient].contactManager getBlackListFromServerWithCompletion:^(NSArray *aList, AgoraError *aError) {
    if (!aError) {
        NSLog(@"Get the blacklist list successfully -- %@",aList);
    } else {
        NSLog(@"Reasons for failure to get blacklist --- %@", aError.errorDescription);
    }
}];
```

#### Get the blacklist from the database

``` objc
// After getting blacklist from the server, you can get blacklist from the local
NSArray *blockList = [[AgoraChatClient sharedClient].contactManager getBlackList];
```

### Add friends to blacklist

``` objc
/*!
 *  Add users to the blacklist
 *
 *  @param aUsername        User to be blacklisted
 *  @param aCompletionBlock The completed callback
 */
- (void)addUserToBlackList:(NSString *)aUsername
                completion:(void (^)(NSString *aUsername, AgoraError *aError))aCompletionBlock;
                
// Calling:                
[[AgoraChatClient sharedClient].contactManager addUserToBlackList:@"6001" completion:^(NSString *aUsername, AgoraError *aError) {
    if (!aError) {
        NSLog(@"add the user to the blacklist successfully");
    } else {
        NSLog(@"Reasons for the failure to add users to the blacklist --- %@", aError.errorDescription);
    }
}];
```

### Remove from blacklist

Interface calling

``` objc
/*!
 *  Remove user from blacklist
 *
 *  @param aUsername        Users to be removed the black order
 *  @param aCompletionBlock The completed callback
 */
- (void)removeUserFromBlackList:(NSString *)aUsername
                     completion:(void (^)(NSString *aUsername, AgoraError *aError))aCompletionBlock;
                     
// Calling:
[[AgoraChatClient sharedClient].contactManager removeUserFromBlackList:@"6001" completion:^(NSString *aUsername, AgoraError *aError) {
    if (!aError) {
        NSLog(@"The user was  removed from the blacklist successfully");
    } else {
        NSLog(@"Reasons for the failure to remove the user from the blacklist --- %@", aError.errorDescription);
    }
}];                     
```

------------------------------------------------------------------------

