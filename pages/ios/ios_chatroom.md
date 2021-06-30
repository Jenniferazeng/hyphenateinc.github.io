---
title: ios Chatroom
keywords: ios
sidebar: ios_sidebar
toc: true
permalink: ios_chatroom.html
folder: ios
---
# iOS SDK's Introduction and import

------------------------------------------------------------------------

## DEMO（ChatDemo-UI3.0 App） experience


Download link：[download page](http://www.easemob.com/download/im)

# Chat Room Management

------------------------------------------------------------------------

` The client SDK does not support creating chat rooms, you can call the REST interface to create `

The chat room management mainly involves the following header files of the Easemob SDK:

``` objc
// Chat room, with chat room id and other attributes
AgoraChatroom.h

// Chat room method calls, such as add proxy, remove proxy, get chat room, etc.
IAgoraChatroomManager.h

// the chatroom protocol callback methods, such as listen to callbacks of the user joining a group, etc.
AgoraChatroomManagerDelegate.h
````

The Easemob chat room model supports a maximum membership of 5000. Unlike groups, after a member in a chat room goes offline for 2 minutes, the server will remove that member from the chat room and will not send push to that member. This member will not be able to automatically enter the chat room after going online.

- The maximum number of members supported is 5000.
- The chat room has three identities: owner, administrator and -.
- support for muting, blacklisting, kicking and other operations.
- not support client-side invitations.
- No support for REST invitations.
- The chat room API is usually synchronous and needs to be executed in a separate thread; if you need to use the asynchronous API, please use the API with the async prefix.

The main features of the Easemob chat room client include

- support for querying all APP chat rooms.
- support for querying chat room details.
- Join chat rooms.
- quit chat rooms.

### Server-side API

Server-side chat room related REST
operations, please refer to [Chatroom Management](/im/server/basics/chatroom).

### get the list of chat rooms by page

``` objc
/*!
 *  Get the specified number of chat rooms from the server
 *
 *  @param aPageNum              Get the page number
 *  @param aPageSize             How many pages to get
 *  @param aCompletionBlock      Completed callbacks
 */

- (void)getChatroomsFromServerWithPage:(NSInteger)aPageNum
                              pageSize:(NSInteger)aPageSize
                            completion:(void (^)(AgoraPageResult *aResult, AgoraError *aError))aCompletionBlock;

// Calling :
[[AgoraChatClient sharedClient].roomManager getChatroomsFromServerWithPage:0 pageSize:50 completion:^(AgoraPageResult *aResult, AgoraError *aError) {
    if (!aError) {
        NSLog(@"Successfully get the specified number of chat rooms from the server");
    } else {
       NSLog(@"The reason for the failure to obtain the specified number of chat rooms from the server --- %@", aError.errorDescription);
    }
}];
```

### Join the chat room

``` objc
/*!
 * Join the chat room
 *
 * @param aChatroomId 			The ID of the chat room
 * @param aCompletionBlock 		The completed callback
 */
- (void)joinChatroom:(NSString *)aChatroomId
          completion:(void (^)(AgoraChatroom *aChatroom, AgoraError *aError))aCompletionBlock; 

// Called:
[[AgoraChatClient sharedClient].roomManager joinChatroom:@"chatroomId" completion:^(AgoraChatroom *aChatroom, AgoraError *aError) {
    if (!aError) {
       NSLog(@"Join chatroom successful");
    } else {
        NSLog(@"Reason for failure to join chatroom --- %@", aError.errorDescription);
    }
}];                          
```

### Leaving the chat room

``` objc
/*!
 *  Quit the chat room
 *
 * @param aChatroomId 			Chatroom ID
 * @param aCompletionBlock 		The completed callback
 */
- (void)leaveChatroom:(NSString *)aChatroomId
           completion:(void (^)(AgoraError *aError))aCompletionBlock;   

// Call:
[[AgoraChatClient sharedClient].roomManager leaveChatroom:@"chatroomId" completion:^(AgoraError *aError) {
    if (!aError) {
        NSLog(@"Exit the chat room successfully");
    } else {
        NSLog(@"Reasons for failure to exit the chat room --- %@", aError.errorDescription);
    }
}];
```

When you leave the chat room, the SDK will delete all local messages in this chat room by default, if you don't want to delete them, you can set the following property to NO

``` objc
/*!
 *  \~chinese
 *  Whether to delete all messages when leaving the chat room, default is YES
 *
 *  \~english 
 *  Whether to delete all the chat room messages when leaving the chat room, default is YES
 */
@property (nonatomic, assign) BOOL isDeleteMessagesWhenExitChatRoom;

// Calling :
AgoraOptions *retOpt = [AgoraOptions optionsWithAppkey:@"appkey"];
retOpt.isDeleteMessagesWhenExitChatRoom = NO;
```

### Get chat room details

``` objc
/*!
 *  Get chat room details
 *
 *  @param aChatroomId           Chat ID
 * @param aCompletionBlock 		The completed callback
 *
 */
- (void)getChatroomSpecificationFromServerWithId:(NSString *)aChatroomId
                                      completion:(void (^)(AgoraChatroom *aChatroom, AgoraError *aError))aCompletionBlock;
                               
// Calling:                                 
[[AgoraChatClient sharedClient].roomManager getChatroomSpecificationFromServerWithId:@"chatroomId" completion:^(AgoraChatroom *aChatroom, AgoraError *aError) {
    if (!aError) {
        NSLog(@"Get chat room details successfully");
    } else {
        NSLog(@"Reasons for failure to get chat room details --- %@", aError.errorDescription);
    }
}];
```

### Paging to get a list of chat room members

``` objc
/*!
 *  Get the list of chat room members
 *
 *  @param aChatroomId      	Chat Room ID
 * @param aCursor 				cursor
 * @param aPageSize 			How many to get
 * @param aCompletionBlock 		The completed callback
 */
- (void)getChatroomMemberListFromServerWithId:(NSString *)aChatroomId
                                       cursor:(NSString *)aCursor
                                     pageSize:(NSInteger)aPageSize
                                   completion:(void (^)(AgoraCursorResult *aResult, AgoraError *aError))aCompletionBlock;
                               
// Calling :                                 
[[AgoraChatClient sharedClient].roomManager getChatroomMemberListFromServerWithId:@"chatroomId" cursor:@"cursor" pageSize:50 completion:^(AgoraCursorResult *aResult, AgoraError *aError) {
    if (!aError) {
        // The AgoraCursorResult class has the cursor property
        NSLog(@"Success in getting the list of chat room members");
    } else {
        NSLog(@"Reason for failure to get the list of chat room members --- %@", aError.errorDescription);
    }
}];
```

###  get the chat room blacklists by page

Requires Owner or Admin permission

``` objc
/*!
 * Get a chat room blacklists, requires owner/admin permission
 *
 * @param aChatroomId 				Chatroom ID
 * @param aPageNum 					Get the number of pages
 * @param aPageSize 				how many to get 
 * @param aCompletionBlock 			The completed callback
 */
- (void)getChatroomBlacklistFromServerWithId:(NSString *)aChatroomId
                                  pageNumber:(NSInteger)aPageNum
                                    pageSize:(NSInteger)aPageSize
                                  completion:(void (^)(NSArray *aList, AgoraError *aError))aCompletionBlock;
                               
// Called:                                 
[[AgoraEClient sharedClient].roomManager getChatroomBlacklistFromServerWithId:@"chatroomId" pageNumber:1 pageSize:50 completion:^( NSArray *aList, AgoraError *aError) {
    if (!aError) {
        NSLog(@"Getting the chat room blacklist list was successful");
    } else {
        NSLog(@"Reason for failure to get chat room blacklist list --- %@", aError.errorDescription);
    }
}];
```

### get the list of muted member in chat room by page

``` objc
/*!
 * Get the list of muted member in chat room
 *
 * @param aChatroomId 			Chatroom ID
 * @param aPageNum 				Gets the number of pages
 * @param aPageSize 			Get how many entries
 * @param aCompletionBlock 		The completed callback
 */
- (void)getChatroomMuteListFromServerWithId:(NSString *)aChatroomId
                                 pageNumber:(NSInteger)aPageNum
                                   pageSize:(NSInteger)aPageSize
                                 completion:(void (^)(NSArray *aList, AgoraError *aError))aCompletionBlock;
                               
// Calling :                                 
[[AgoraChatClient sharedClient].roomManager getChatroomMuteListFromServerWithId:@"chatroomId" pageNumber:1 pageSize:50 completion:^(NSArray *aList, AgoraError *aError) {
    if (!aError) {
        NSLog(@"Get chat room muted list successfully");
    } else {
        NSLog(@"Reason for failure in getting the chat room muted list --- %@", aError.errorDescription);
    }
}];
```

### Update chat room name

Requires Owner permission

``` objc
/*!
 * Change chat room name, requires owner permission
 *
 * @param aSubject 			new subject
 * @param aChatroomId 		Chatroom ID
 * @param aCompletionBlock 	completed callback
 */
- (void)updateSubject:(NSString *)aSubject
          forChatroom:(NSString *)aChatroomId
           completion:(void (^)(AgoraChatroom *aChatroom, AgoraError *aError))aCompletionBlock;
                               
// Called:                                 
[[AgoraChatClient sharedClient].roomManager updateSubject:@"newSubject" forChatroom:@"chatroomId" completion:^(AgoraChatroom *aChatroom, AgoraError *aError) {
    if (!aError) {
        NSLog(@"Changed chatroom subject successfully");
    } else {
        NSLog(@"Reason for failure to change chatroom subject --- %@", aError.errorDescription);
    }
}];
```

### Update chat room description

Requires Owner permission

``` objc
/*!
 * Change chat room description, requires owner permission
 *
 * @param aDescription 			Description 
 * @param aChatroomId 			Chatroom ID
 * @param aCompletionBlock 		The completed callback
 */
- (void)updateDescription:(NSString *)aDescription
              forChatroom:(NSString *)aChatroomId
               completion:(void (^)(AgoraChatroom *aChatroom, AgoraError *aError))aCompletionBlock;
                               
// Calling :                                 
[[AgoraChatClient sharedClient].roomManager updateDescription:@"newDescription" forChatroom:@"chatroomId" completion:^(AgoraChatroom *aChatroom, AgoraError *aError) {
    if (!aError) {
        NSLog(@"Changing chat room description was successful");
    } else {
        NSLog(@"Reason for failure to change chat room description--- %@", aError.errorDescription);
    }
}];
```

### Get chat room announcement

``` objc
/*!
 * Get the chat room announcement
 *
 * @param aChatroomId Chatroom ID
 * @param aCompletionBlock The completed callback
 */
- (void)getChatroomAnnouncementWithId:(NSString *)aChatroomId
                           completion:(void (^)(NSString *aAnnouncement, AgoraError *aError))aCompletionBlock;

// Called:
[[AgoraChatClient sharedClient].roomManager getChatroomAnnouncementWithId:@"chatroomId" completion:^(NSString *aAnnouncement, AgoraError * aError) {
    if (!aError) {
        NSLog(@"Getting chatroom announcement was successful");
    } else {
        NSLog(@"Reason for failure to get chat room announcement --- %@", aError.errorDescription);
    }
}];
```

It is also possible to get a message push for a chat room announcement through the chat room listener interface. See [chatroom-related callbacks](http://docs-im.easemob.com/im/ios/basics/chatroom#聊天室相关的回调)

### Update chat room announcements

``` objc
/*!
 *  Modify chat room announcement, need Owner / Admin permission
 *
 * @param aChatroomId 				Chatroom ID
 * @param aAnnouncement 			group announcement
 * @param aCompletionBlock 			The completed callback
 */
- (void)updateChatroomAnnouncementWithId:(NSString *)aChatroomId
                            announcement:(NSString *)aAnnouncement
                              completion:(void (^)(AgoraChatroom *aChatroom, AgoraError *aError))aCompletionBlock;

// Calling :
[[AgoraChatClient sharedClient].roomManager updateChatroomAnnouncementWithId:@"chatroomId" announcement:@"newAnnouncement" completion:^(AgoraChatroom *aChatroom, AgoraError *aError) {
    if (!aError) {
        NSLog(@"Modify chat room announcement successfully");
    } else {
        NSLog(@"Reasons for failure to modify chat room announcements --- %@", aError.errorDescription);
    }
}];
```

When the chat room owner/Admin modifies the chat room announcement, other members will receive a notification that the chat room announcement has been updated

``` objc
/*!
 *  Chat room announcement has been updated
 *
 *  @param aChatroom        Chat Room
 *  @param aAnnouncement    Announcement
 */
- (void)chatroomAnnouncementDidUpdate:(AgoraChatroom *)aChatroom
                         announcement:(NSString *)aAnnouncement;
```

### Enable and disable full mute

Owners and administrators can enable and disable full mutes.

``` objc
/*!
 * \~chinese
 * Set full mute, need Owner / Admin permission
 *
 * @param aChatroomId 		Chatroom ID
 * @param aCompletionBlock 	The completed callback
 */
- (void)muteAllMembersFromChatroom:(NSString *)aChatroomId
                        completion:(void(^)(AgoraChatroom *aChatroom, AgoraError *aError))aCompletionBlock;
                        
                        
/*!
 * \~chinese
 * Unblock all members, requires Owner / Admin permission
 *
 * @param aChatroomId Chatroom ID
 * @param aCompletionBlock The completed callback
 */
- (void)unmuteAllMembersFromChatroom:(NSString *)aChatroomId
                          completion:(void(^)(AgoraChatroom *aChatroom, AgoraError *aError))aCompletionBlock;
```

### Whitelist management

Users can be added to a whitelist. The user whitelist takes effect when the administrator has enabled a full mute and  whitelisted users can send messages.
In addition you can move users out of the whitelist, check if you are in the whitelist and get the whitelist list.

``` objc
/*!
 *  \~chinese
 *  Adding a whitelist requires Owner / Admin permission
 *
 * @param aMembers 			The added members list <NSString>
 * @param aChatroomId 		Chatroom ID
 * @param aCompletionBlock 	Callback for completed
 */
- (void)addWhiteListMembers:(NSArray *)aMembers
               fromChatroom:(NSString *)aChatroomId
                 completion:(void (^)(AgoraChatroom *aChatroom, AgoraError *aError))aCompletionBlock;
                 
/*!
 *  \~chinese
 *  Remove whitelist, requires Owner / Admin permission
 *
 * @param aMembers 			The list of removed members <NSString>
 * @param aChatroomId 		Chatroom ID
 * @param aCompletionBlock 	Callback for completed
 */
- (void)removeWhiteListMembers:(NSArray *)aMembers
                  fromChatroom:(NSString *)aChatroomId
                    completion:(void (^)(AgoraChatroom *aChatroom, AgoraError *aError))aCompletionBlock;
                    
                    
/*!
 *  \~chinese
 *  Check if you are in the chat room whitelist
 *
 * @param aChatroomId 		Chatroom ID
 * @param pError 			error message
 */
- (BOOL)isMemberInWhiteListFromServerWithChatroomId:(NSString *)aChatroomId
                                              error:(AgoraError **)pError;
                                              
                                              
/*!
 *  \~chinese
 *  Get a list of chat room whitelists
 *
 * @param aChatroomId 		Chatroom ID
 * @param aCompletionBlock 	The completed callback
 */
- (void)getChatroomWhiteListFromServerWithId:(NSString *)aChatroomId
                                  completion:(void (^)(NSArray *aList, AgoraError *aError))aCompletionBlock;
```

### Move members out of the chat room

Requires Owner or Admin permission

``` objc
/*!
 *  Move members out of the chat room, requires owner/admin permission
 *
 * @param aMembers 			list of users to be removed
 * @param aChatroomId 		Chatroom ID
 * @param aCompletionBlock 	The completed callback
 */
- (void)removeMembers:(NSArray *)aMembers
         fromChatroom:(NSString *)aChatroomId
           completion:(void (^)(AgoraChatroom *aChatroom, AgoraError *aError))aCompletionBlock;
                               
// Calling :                                
[[AgoraChatClient sharedClient].roomManager removeMembers:@[@"username"] fromChatroom:@"chatroomId" completion:^(AgoraChatroom *aChatroom, AgoraError *aError) {
    if (!aError) {
        NSLog(@"Removal of member from chat room successful");
    } else {
        NSLog(@"Reasons for failure to move members out of the chat room --- %@", aError.errorDescription);
    }
}];
```

### Add user to chat room blacklist

Requires Owner or Admin permission

``` objc
/*!
 * Adding people to the chat room blacklist
 *
 * @param aMembers 			Users to be blacklisted
 * @param aChatroomId		 Chatroom ID
 * @param aCompletionBlock 	The completed callback
 */
- (void)blockMembers:(NSArray *)aMembers
        fromChatroom:(NSString *)aChatroomId
          completion:(void (^)(AgoraChatroom *aChatroom, AgoraError *aError))aCompletionBlock;
                               
// Called:                                 
[[AgoraChatClient sharedClient].roomManager blockMembers:@[@"username"] fromChatroom:@"chatroomId" completion:^(AgoraChatroom *aChatroom, AgoraError *aError) {
    if (!aError) {
        NSLog(@"Adding people to chatroom blacklist was successful");
    } else {
        NSLog(@"Reason why adding people to the chatroom blacklist failed --- %@", aError.errorDescription);
    }
}];
```

### Remove a user from chat room blacklist

Requires Owner or Admin permission

``` objc
/*!
 * Remove a user from chat room blacklist
 *
 * @param aMembers 				List of user to remove from the blacklist
 * @param aChatroomId 			Chatroom ID
 * @param aCompletionBlock 		The completed callback
 */
- (void)unblockMembers:(NSArray *)aMembers
          fromChatroom:(NSString *)aChatroomId
            completion:(void (^)(AgoraChatroom *aChatroom, AgoraError *aError))aCompletionBlock;
                               
// Calling :                                 
[[AgoraChatClient sharedClient].roomManager unblockMembers:@[@"username"] fromChatroom:@"chatroomId" completion:^(AgoraChatroom *aChatroom, AgoraError *aError) {
    if (!aError) {
        NSLog(@"Success in remove users from chat room blacklist");
    } else {
        NSLog(@"Reason for failure to remove users from chat room blacklist --- %@", aError.errorDescription);
    }
}];
```

### Change chat room creator

Requires Owner permission

``` objc
/*!
 * Change chat room creator, requires Owner permission
 *
 * @param aChatroomId 		Chatroom ID
 * @param aNewOwner 		newOwner
 * @param aCompletionBlock 	The completed callback
 */
- (void)updateChatroomOwner:(NSString *)aChatroomId
                   newOwner:(NSString *)aNewOwner
                 completion:(void (^)(AgoraChatroom *aChatroom, AgoraError *aError))aCompletionBlock;
                               
// Called:                                 
[[AgoraChatClient sharedClient].roomManager updateChatroomOwner:@"chatroomId" newOwner:@"newOwner" completion:^(AgoraChatroom *aChatroom, AgoraError *aError) {
    if (!aError) {
        NSLog(@"Changing chatroom creator was successful");
    } else {
        NSLog(@"Reason for failure to change chatroom creator --- %@", aError.errorDescription);
    }
}];
```

### Add chat room administrator

Requires Owner permission

``` objc
/*!
 * Add chat room administrator, requires Owner permission
 *
 * @param aAdmin 				the group administrator to add
 * @param aChatroomId 			Chatroom ID
 * @param aCompletionBlock 		The completed callback
 */
- (void)addAdmin:(NSString *)aAdmin
      toChatroom:(NSString *)aChatroomId
      completion:(void (^)(AgoraChatroom *aChatroomp, AgoraError *aError))aCompletionBlock;
                               
// Called with:                                 
[[AgoraChatClient sharedClient].roomManager addAdmin:@"adminId" toChatroom:@"chatroomId" completion:^(AgoraChatroom *aChatroomp, AgoraError * aError) {
    if (!aError) {
        NSLog(@"Add chatroom admin successfully");
    } else {
        NSLog(@"Reason for failure to add chatroom admin --- %@", aError.errorDescription);
    }
}];
```

### Remove chat room administrator

Requires Owner permission

``` objc
/*!
 * Remove chat room administrator, requires Owner permission
 *
 * @param aAdmin 			the group administrator to remove
 * @param aChatroomId 		Chatroom ID
 * @param aCompletionBlock 	Callback for completed
 */
- (void)removeAdmin:(NSString *)aAdmin
       fromChatroom:(NSString *)aChatroomId
         completion:(void (^)(AgoraChatroom *aChatroom, AgoraError *aError))aCompletionBlock;
                               
// Called:                                 
[[AgoraChatClient sharedClient].roomManager removeAdmin:@"adminId" fromChatroom:@"chatroomId" completion:^(AgoraChatroom *aChatroom, AgoraError *aError) {
    if (!aError) {
        NSLog(@"Remove of chatroom administrator successfully");
    } else {
        NSLog(@"Reason for failure to remove chatroom administrator --- %@", aError.errorDescription);
    }
}];
```

### Muting chat room members

Those with high permission can mute those with low permission, and vice versa not allowed

``` objc
/*!
 * Muting a group member requires Owner / Admin permission
 *
 * @param aMuteMembers List of members to be muted <NSString>
 * @param aMuteMilliseconds length of mute (in milliseconds, if "-1" means permanent mute)
 * @param aChatroomId Chatroom ID
 * @param aCompletionBlock The completed callback
 */
- (void)muteMembers:(NSArray *)aMuteMembers
   muteMilliseconds:(NSInteger)aMuteMilliseconds
       fromChatroom:(NSString *)aChatroomId
         completion:(void (^)(AgoraChatroom *aChatroom, AgoraError *aError))aCompletionBlock;
                               
// Called:                                 
[[AgoraChatClient sharedClient].roomManager muteMembers:@[@"userName"] muteMilliseconds:10000 fromChatroom:@"chatroomId" completion:^( AgoraChatroom *aChatroom, AgoraError *aError) {
    if (!aError) {
        NSLog(@"muted a group member successfully");
    } else {
        NSLog(@"Reason for muting a group member failed --- %@", aError.errorDescription);
    }
}];
```

### Unmuting

High permission can mute low permission, and vice versa not allowed

``` objc
/*!
 * To unmute, you need Owner / Admin permission.
 *
 * @param aMuteMembers list of unmuted <NSString>
 * @param aChatroomId Chatroom ID
 * @param aCompletionBlock The completed callback
 */
- (void)unmuteMembers:(NSArray *)aMembers
         fromChatroom:(NSString *)aChatroomId
           completion:(void (^)(AgoraChatroom *aChatroom, AgoraError *aError))aCompletionBlock;
                               
// Called:                                 
[[AgoraChatClient sharedClient].roomManager unmuteMembers:@[@"userName"] fromChatroom:@"chatroomId" completion:^(AgoraChatroom *aChatroom, AgoraError *aError) {
    if (!aError) {
        NSLog(@"unmute successful");
    } else {
        NSLog(@"Reason for unmuting failure --- %@", aError.errorDescription);
    }
}];
```

### Callbacks related to chat rooms

Registering for a chat room callback

``` objc
protocal:AgoraChatroomManagerDelegate

Proxy:
//Register for chat room callbacks
[[AgoraChatClient sharedClient].roomManager addDelegate:self delegateQueue:nil];
//Remove chat room callbacks
[[AgoraChatClient sharedClient].roomManager removeDelegate:self];
```

Callback method:

``` objc
/*!
 * A user has joined the chat room
 *
 * @param aChatroom 	chat room joined
 * @param aUsername 	Joiner
 */
- (void)userDidJoinChatroom:(AgoraChatroom *)aChatroom
                       user:(NSString *)aUsername;

/*!
 * A user has left the chatroom
 *!
 * @param aChatroom 	The chatroom that left
 * @param aUsername 	The person who left
 */
- (void)userDidLeaveChatroom:(AgoraChatroom *)aChatroom
                        user:(NSString *)aUsername;

/*!
 * Kicked out of the chatroom
 *!
 * @param aChatroom 	the chatroom which user was kicked out of 
 * @param aReason 		the reason for being kicked out of the chatroom
 */
- (void)didDismissFromChatroom:(AgoraChatroom *)aChatroom
                        reason:(AgoraChatroomBeKickedReason)aReason;

/*!
 * A member has been added to the mute list
 *
 * @param aChatroom 		chatroom
 * @param aMutedMembers 	muted members
 * @param aMuteExpire 		mute expiration time, temporarily unavailable
 */
- (void)chatroomMuteListDidUpdate:(AgoraChatroom *)aChatroom
                addedMutedMembers:(NSArray *)aMutes
                       muteExpire:(NSInteger)aMuteExpire;

/*!
 * A member was moved out of the muted list
 *
 * @param aChatroom 		chatroom
 * @param aMutedMembers 	Member removed from the mute list
 */
- (void)chatroomMuteListDidUpdate:(AgoraChatroom *)aChatroom
              removedMutedMembers:(NSArray *)aMutes;

/*!
 * A member has been added to the list of administrators
 *
 * @param aChatroom 	chatroom
 * @param aAdmin 		Member added to the administrators list
 */
- (void)chatroomAdminListDidUpdate:(AgoraChatroom *)aChatroom
                        addedAdmin:(NSString *)aAdmin;

/*!
 * A member was moved out of the admin list
 *
 * @param aChatroom chatroom
 * @param aAdmin The member that was moved out of the admin list
 */
- (void)chatroomAdminListDidUpdate:(AgoraChatroom *)aChatroom
                      removedAdmin:(NSString *)aAdmin;

/*!
 * Chatroom creator has updates
 *
 * @param aChatroom 	chatroom
 * @param aNewOwner 	New group owner
 * @param aOldOwner 	old group owner
 */
- (void)chatroomOwnerDidUpdate:(AgoraChatroom *)aChatroom
                      newOwner:(NSString *)aNewOwner
                      oldOwner:(NSString *)aOldOwner;
```

------------------------------------------------------------------------


