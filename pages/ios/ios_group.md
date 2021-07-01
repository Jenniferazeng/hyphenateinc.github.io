---
title:  Group
keywords: ios
sidebar: ios_sidebar
toc: true
permalink: ios_group.html
folder: ios
---


# Group Management

------------------------------------------------------------------------


The Easemob SDK header files mainly involved in group management are as follows:

``` objc
// Group, it includes attributes such as group id
AgoraGroup.h

// Group attribute options，used to set the attributes when creating a group
AgoraGroupOptions.h

// Group shared files
AgoraGroupSharedFile.h

// Group method call，such as adding proxy, removing proxy, creating groups, getting group lists, etc.
IAgoraGroupManager.h

// Protocol callback methods of the group, such as the callback method for monitoring users to add groups, etc.
AgoraGroupManagerDelegate.h
```

Register group module callback：

``` objc
Protocol: AgoraGroupManagerDelegate

proxy:
//Register group callback
[[AgoraChatClient sharedClient].groupManager addDelegate:self delegateQueue:nil];

//Remove group callback
[[AgoraChatClient sharedClient].groupManager removeDelegate:self];
```

------------------------------------------------------------------------

There are four types of groups.

``` objc
/*!
 @enum
 @brief Group type
 @constant AgoraGroupStylePrivateOnlyOwnerInvite private group, after creation, only Owner is allowed to invite users to join the group
 @constant AgoraGroupStylePrivateMemberCanInvite Private group, after creation, only Owner and group members are allowed to invite users to join the group
 @constant AgoraGroupStylePublicJoinNeedApproval public group. After creation, only Owner is allowed to invite users to join. Users who are not members of the group need to send an application to join the group. After the owner approves his application, he can join the group
 @constant AgoraGroupStylePublicOpenJoin public group, after the creation is completed, non-group members are allowed to join the group without the approval of the administrator
 @discussion
    eGroupStyle+Private：Private group, only allow group members to invite people to join the group
    eGroupStyle+Public： Public groups, allowing non-group members to join the group
 */
typedef NS_ENUM(NSInteger, AgoraGroupStyle){
    AgoraGroupStylePrivateOnlyOwnerInvite  = 0,
    AgoraGroupStylePrivateMemberCanInvite,
    AgoraGroupStylePublicJoinNeedApproval,
    AgoraGroupStylePublicOpenJoin,
};
```

## Group operation

### Create a group

The currently supported configuration attributes by group creation are:

-   Group name
-   Group description
-   Group number (modification is not supported, the current maximum number is 3000)
-   Group type (the four group types mentioned above)

Asynchronous method：

``` objc
    AgoraGroupOptions *setting = [[AgoraGroupOptions alloc] init]; // Group attribute options
    setting.maxUsersCount = 500; // the maximum number of members of the group(Including group owners and administrators, the default value is 200, the maximum is 3000)
    setting.IsInviteNeedConfirm = NO; //When inviting group members, is an invitation required. If NO, the invited person will automatically join the group.
    setting.style = AgoraGroupStylePublicOpenJoin;// to create different types of groups, you need to input different types here
    setting.ext = @"Group extension information"; // extension information
    
/*!
 *  Create a group
 *  
 *  Asynchronous method
 *
 *  @param aSubject        Group name
 *  @param aDescription    Group description
 *  @param aInvitees       Group members (not including the creator himself, no more than 100)
 *  @param aMessage        Invitation message
 *  @param aSetting        Group attribute
 *  @param pError          Error information
 *
 *  @return    Group created
 */
- (void)createGroupWithSubject:(NSString *)aSubject
                   description:(NSString *)aDescription
                      invitees:(NSArray *)aInvitees
                       message:(NSString *)aMessage
                       setting:(AgoraGroupOptions *)aSetting
                    completion:(void (^)(AgoraGroup *aGroup, AgoraError *aError))aCompletionBlock;
               
// call:                    
[[AgoraChatClient sharedClient].groupManager createGroupWithSubject:@"Group name"description:@"Group description" invitees:@[@"6001",@"6002"] message:@"Invites you to join the group" setting:setting completion:^(AgoraGroup *aGroup, AgoraError *aError) {
    if(!aError){
        NSLog(@"Group created successfully -- %@",aGroup);
    } else {
        NSLog(@"Reasons for failed group creation --- %@", aError.errorDescription);
    }
}];
```

### Get the group details

`Note: The parameter "Whether to get group members" is supported since version 3.7.4.`

``` objc
/*!
 *  Get group details, including group ID, group name, group description, group basic attributes, group owner, group manager
 *
 *  @param aGroupId              Group ID
 *  @param fetchMembers     	 Whether to get group members, The default is up to 200 people
 *  @param aCompletionBlock      Completed callback
 *
 */
- (void)getGroupSpecificationFromServerWithId:(NSString *)aGroupId
                                   fetchMembers:(BOOL)fetchMembers
                                   completion:(void (^)(AgoraGroup *aGroup, AgoraError *aError))aCompletionBlock;
                                   
//call:                       
[[AgoraChatClient sharedClient].groupManager getGroupSpecificationFromServerWithId:self.group.groupId fetchMembers:YES completion:^(AgoraGroup *aGroup, AgoraError *aError) {
    if (!aError) {
        // AgoraGroup contains many group attributes, such as group id, group members, whether to block group messages (isBlocked attribute), etc., more detrails in the AgoraGroup.h header file in the SDK.
        NSLog(@"Get group details successfully");
    } else {
        NSLog(@"Reasons for failure to get group details --- %@", aError.errorDescription);
    }                
}];             
```

### Join the group

There are 4 types of group groups. Currently, the SDK does not support active selection of whether to join the group. We will explain the operations required to join the group for each type.

-   AgoraGroupStylePrivateOnlyOwnerInvite:
    This type of group only allows the owner (Owner) to add people into the group, and other people cannot actively join.
-   AgoraGroupStylePrivateMemberCanInvite:
    (Recommended) This type of group allows all group members to add people to the group, and other people cannot actively join.
-   AgoraGroupStylePublicJoinNeedApproval:
    (Recommended) This type of group only allows the owner (Owner) to add people into the group. If other people want to join the group, they need to send an application first, and join the group with the approval of owner. Others cannot actively join.
-   AgoraGroupStylePublicOpenJoin:
    (Not recommended) This type of group allows anyone to actively join the group.

#### Add people to the group

The added person will receive a callback:

``` objc
/*!
 *  After the SDK automatically approved user A's invitation to user B to join the group, user B receives the callback and needs to set isAutoAcceptGroupInvitation of AgoraGroupOptions to YES
 *
 *  @param aGroup    Group instance
 *  @param aInviter  Inviter
 *  @param aMessage  Invitation message
 */
- (void)didJoinGroup:(AgoraGroup *)aGroup
             inviter:(NSString *)aInviter
             message:(NSString *)aMessage;     
```

The interface for adding people is as follows:

``` objc
/*!
 *  Invite users to join the group
 *
 *  @param aUsers           List of invited usernames
 *  @param aGroupId         Group ID
 *  @param aMessage         Welcome message
 *  @param aCompletionBlock Completed callback
 */
- (void)addMembers:(NSArray *)aUsers
           toGroup:(NSString *)aGroupId
           message:(NSString *)aMessage
        completion:(void (^)(AgoraGroup *aGroup, AgoraError *aError))aCompletionBlock;
        
// call:        
[[AgoraChatClient sharedClient].groupManager addMembers:@[@"user1"] toGroup:@"groupId" message:@"Invites you to join the group" completion:^(AgoraGroup *aGroup, AgoraError *aError) {
    if (!aError) {
        NSLog(@"Invite to join the group successfully --- %@", aGroup);
    } else {
        NSLog(@"Reasons for the failed invitation to join the group --- %@", aError.errorDescription);
    }
}];
```

#### Send the group request

``` objc
/*!
 *  Apply to join a public group which requires the approval, the group type should be AgoraGroupStylePublicJoinNeedApproval
 *
 *  @param aGroupId         public group ID
 *  @param aMessage         Message of request
 *  @param aCompletionBlock Completed callback
 */
- (void)requestToJoinPublicGroup:(NSString *)aGroupId
                         message:(NSString *)aMessage
                      completion:(void (^)(AgoraGroup *aGroup, AgoraError *aError))aCompletionBlock;
                      
// call:                     
[[AgoraChatClient sharedClient].groupManager requestToJoinPublicGroup:@"groupId" message:@"Apply to join the group" completion:^(AgoraGroup *aGroup, AgoraError *aError) {
    if (!aError) {
        NSLog(@"Successfully applied to join the public group --- %@", aGroup);
    } else {
        NSLog(@"Reasons for the failure to apply to join the public group --- %@", aError.errorDescription);
    }
}];
```

#### Process the group request

Only the Owner has the rights to process group joining request.

1\. Group request received

``` objc
/*!
 *  The owner of the group receives the user’s application for joining the group. The type of the group is AgoraGroupStylePublicJoinNeedApproval
 *
 *  @param aGroup      Group instance
 *  @param aApplicant  Applicant
 *  @param aReason     Applicant's additional information
 */
- (void)joinGroupRequestDidReceive:(AgoraGroup *)aGroup
                              user:(NSString *)aUsername
                            reason:(NSString *)aReason;
```

2\. approve the request

``` objc
/*!
 *  To approve group membership request, Owner permission is required
 *
 *  @param aGroupId         ID of group which aprrove your application
 *  @param aUsername        Applicant
 *  @param aCompletionBlock Completed callback
 */
- (void)approveJoinGroupRequest:(NSString *)aGroupId
                         sender:(NSString *)aUsername
                     completion:(void (^)(AgoraGroup *aGroup, AgoraError *aError))aCompletionBlock;

//call:
[[AgoraChatClient sharedClient].groupManager approveJoinGroupRequest:@"groupId" sender:@"userId" completion:^(AgoraGroup *aGroup, AgoraError *aError) {
    if (!aError) {
        NSLog(@"Approval of group membership application is successful --- %@", aGroup);
    } else {
        NSLog(@"Reasons for failure to approve group membership --- %@", aError.errorDescription);
    }
}];
```

3\. Refuse to join the group application.

``` objc
/*!
 *  decline group application, Owner permission is required
 *
 *  @param aGroupId         ID of group which rejected your application
 *  @param aUsername        Applicant
 *  @param aReason          Reason for rejection
 *  @param aCompletionBlock Completed callback
 */
- (void)declineJoinGroupRequest:(NSString *)aGroupId
                         sender:(NSString *)aUsername
                         reason:(NSString *)aReason
                     completion:(void (^)(AgoraGroup *aGroup, AgoraError *aError))aCompletionBlock;
                     
// call:
[[AgoraChatClient sharedClient].groupManager declineJoinGroupRequest:@"groupId" sender:@"userId" reason:@"Reason for rejection" completion:^(AgoraGroup *aGroup, AgoraError *aError) {
    if (!aError) {
        NSLog(@"Successfully refused to join the group --- %@", aGroup);
    } else {
        NSLog(@"Reasons for rejection of group application failure--- %@", aError.errorDescription);
    }
}];                     
```

#### Join the group of type AgoraGroupStylePublicOpenJoin

``` objc
/*!
 *  Join a public group, the group type should be AgoraGroupStylePublicOpenJoin
 *
 *  @param aGroupId         public group ID
 *  @param aCompletionBlock Completed callback
 */
- (void)joinPublicGroup:(NSString *)aGroupId
             completion:(void (^)(AgoraGroup *aGroup, AgoraError *aError))aCompletionBlock;
             
// call:
[[AgoraChatClient sharedClient].groupManager joinPublicGroup:@"groupId" completion:^(AgoraGroup *aGroup, AgoraError *aError) {
    if (!aError) {
        NSLog(@"Successfully joined the public group --- %@", aGroup);
    } else {
        NSLog(@"Reasons for failure to join the public group --- %@", aError.errorDescription);
    }
}];
```

### Leave the group

The owner (Owner) cannot leave the group, but can only disband the group.。

Withdrawing from the group is classified as active withdrawal and passive withdrawal. Passive withdrawal from the group represents being kicked out of the group by the Owner.

#### Active withdrawal

``` objc
/*!
 *  Withdrawing from the group. The owner (Owner) cannot leave the group, but can only disband the group.。
 *
 *  @param aGroupId         Group ID
 *  @param aCompletionBlock Completed callback
 */
- (void)leaveGroup:(NSString *)aGroupId
        completion:(void (^)(AgoraError *aError))aCompletionBlock;
        
// call:
[[AgoraChatClient sharedClient].groupManager leaveGroup:@"groupId" completion:^(AgoraError *aError) {
    if (!aError) {
        NSLog(@"Successfully left the group");
    } else {
        NSLog(@"Reasons for failure to leave the group --- %@", aError.errorDescription);
    }
}];
```

#### Passive withdrawal

The kicked person will be notified through the following callback.

``` objc
/*!
 *  Withdrawing from group callback
 *
 *  @param aGroup     Group instance
 *  @param aReason    Reason for Withdrawing
 */
- (void)didLeaveGroup:(AgoraGroup *)aGroup
               reason:(AgoraGroupLeaveReason)aReason;
```

### Disband the group

Disbanding the group requires Owner's permission

``` objc
/*!
 *  Disbanding the group requires Owner's permission
 *
 *  @param aGroupId         Group ID
 *  @param aCompletionBlock Completed callback
 */
- (void)destroyGroup:(NSString *)aGroupId
    finishCompletion:(void (^)(AgoraError *aError))aCompletionBlock;
    
// call:    
[[AgoraChatClient sharedClient].groupManager destroyGroup:@"groupId" finishCompletion:^(AgoraError *aError) {
    if (!aError) {
        NSLog(@"Disbanded the group successfully");
    } else {
        NSLog(@"Reasons for the failure to dissolve the group --- %@", aError.errorDescription);
    }
}];
```

### Modify the group subject (name)

Only the Owner has permission to modify it.

``` objc
/*!
 *  \~chinese
 *  To modify the group's subject, owner permission is required
 *
 *  @param aSubject         new subject
 *  @param aGroupId         Group ID
 *  @param aCompletionBlock Completed callback
 *
 *
 *  \~english
 *  Change the group subject, owner‘s authority is required
 *
 *  @param aSubject         New group‘s subject
 *  @param aGroupId         Group id
 *  @param aCompletionBlock The callback block of completion
 *
 */
- (void)updateGroupSubject:(NSString *)aSubject
                  forGroup:(NSString *)aGroupId
                completion:(void (^)(AgoraGroup *aGroup, AgoraError *aError))aCompletionBlock;
                
// call:                
[[AgoraChatClient sharedClient].groupManager updateGroupSubject:@"modify the group's subject" forGroup:@"groupId" completion:^(AgoraGroup *aGroup, AgoraError *aError) {
    if (!aError) {
        NSLog(@"modifed the group's subject successfully --- %@", aGroup.subject);
    } else {
        NSLog(@"reason for failed to modifed the group's subject --- %@", aError.errorDescription);
    }
}];
```

### Modify the group description

\*\* not recommended \*\*，only the owner has the permission

``` objc
/*!
 *  To modify group description, owner permission is required
 *
 *  @param aDescription     description
 *  @param aGroupId         Group ID
 *  @param aCompletionBlock Completed callback
 */
- (void)updateDescription:(NSString *)aDescription
                 forGroup:(NSString *)aGroupId
               completion:(void (^)(AgoraGroup *aGroup, AgoraError *aError))aCompletionBlock;
               
// call:               
[[AgoraChatClient sharedClient].groupManager updateDescription:@"modify group description" forGroup:@"groupId" completion:^(AgoraGroup *aGroup, AgoraError *aError) {
    if (!aError) {
        NSLog(@"modify group description successfully --- %@", aGroup.description);
    } else {
        NSLog(@"reason for failed to modify group description--- %@", aError.errorDescription);
    }
}];
```

### Get the list of group members

The group members are sorted according to the time when the group members enter the group, and the members who enter the group late are listed above.

``` objc
/*!
 *  Get the list of group members
 *
 *  @param aGroupId         Group ID
 *  @param aCursor          cursor
 *  @param aPageSize        How many to get
 *  @param aCompletionBlock Completed callback
 */
- (void)getGroupMemberListFromServerWithId:(NSString *)aGroupId
                                    cursor:(NSString *)aCursor
                                  pageSize:(NSInteger)aPageSize
                                completion:(void (^)(AgoraCursorResult *aResult, AgoraError *aError))aCompletionBlock;

// call:                                 
[[AgoraChatClient sharedClient].groupManager getGroupMemberListFromServerWithId:@"groupId" cursor:@"cursor" pageSize:10 completion:^(AgoraCursorResult *aResult, AgoraError *aError) {
    if (!aError) {
        // aResult.list: The returned list of members, the internal value of which is the member's Easemob id.
        // aResult.cursor: The returned cursor, if you want to get the list of the next page, you need to pass this cursor as an argument to get the list of group members.
        NSLog(@"Get group members list successfully --- %@", aResult);
    } else {
        NSLog(@"Reasons for failure to get the list of group members --- %@", aError.errorDescription);
    }
}];
```

### Get group owners and group administrators

It is recommended to get the group details first, and then get the group owner and administrator from the returned aGroup object.

``` objc
// Method to get group details
[[AgoraChatClient sharedClient].groupManager getGroupSpecificationFromServerWithId:@"groupId" completion:^(AgoraGroup *aGroup, AgoraError *aError) {
    if (!aError) {
        // get the group owner
        NSString *owner = aGroup.owner;

        // get the group administrator
        NSArray *adminList = aGroup.adminList;
    } else {
        NSLog(@"Reasons for failure to get --- %@", aError.errorDescription);
    }
}];
```

### Get group blacklist

Requires owner or admin permissions.

``` objc
/*!
 *  Get group blacklist, requires owner/admin permission
 *
 *  @param aGroupId         Group ID
 *  @param aPageNum         get which page
 *  @param aPageSize        How many to get
 *  @param aCompletionBlock Completed callback
 */
- (void)getGroupBlacklistFromServerWithId:(NSString *)aGroupId
                               pageNumber:(NSInteger)aPageNum
                                 pageSize:(NSInteger)aPageSize
                               completion:(void (^)(NSArray *aList, AgoraError *aError))aCompletionBlock;
                               
// call:                                 
[[AgoraChatClient sharedClient].groupManager getGroupBlacklistFromServerWithId:@"groupId" pageNumber:1 pageSize:50 completion:^(NSArray *aList, AgoraError *aError) {
    if (!aError) {
        NSLog(@"Get group blacklist successfully --- %@", aList);
    } else {
        NSLog(@"Reasons for failure to get group blacklist --- %@", aError.errorDescription);
    }
}];
```

### Get a list of muted members

Requires owner or admin permissions.

``` objc
/*!
 *  Get a list of banned members in the group
 *
 *  @param aGroupId         Group ID
 *  @param aPageNum         Get the which pages
 *  @param aPageSize        How many to get
 *  @param aCompletionBlock Completed callback
 */
- (void)getGroupMuteListFromServerWithId:(NSString *)aGroupId
                              pageNumber:(NSInteger)aPageNum
                                pageSize:(NSInteger)aPageSize
                              completion:(void (^)(NSArray *aList, AgoraError *aError))aCompletionBlock;
   
// call:                             
[[AgoraChatClient sharedClient].groupManager getGroupMuteListFromServerWithId:@"groupId" pageNumber:1 pageSize:50 completion:^(NSArray *aList, AgoraError *aError) {
    if (!aError) {
        NSLog(@"Get a list of mutened members in the group successfully --- %@", aList);
    } else {
        NSLog(@"Reasons for failure to get the list of mutened members in the group --- %@", aError.errorDescription);
    }
}];
```

### Get group announcements

``` objc
/*!
 *  Get group announcements
 *
 *  @param aGroupId         Group ID
 *  @param aCompletionBlock Completed callback
 */
- (void)getGroupAnnouncementWithId:(NSString *)aGroupId
                        completion:(void (^)(NSString *aAnnouncement, AgoraError *aError))aCompletionBlock;

// call:
[[AgoraChatClient sharedClient].groupManager getGroupAnnouncementWithId:@"groupId" completion:^(NSString *aAnnouncement, AgoraError *aError) {
    if(!aError){
        NSLog(@"Get group announcement successfully --- %@", aAnnouncement);
    } else {
        NSLog(@"Reasons for failure to get group announcement --- %@", aError.errorDescription);
    }
}];
```

### Modify group announcement

``` objc
/*!
 *  Modify group announcement, requires Owner/Admin permission
 *
 *  @param aGroupId         Group ID
 *  @param aAnnouncement    group announcement
 *  @param aCompletionBlock Completed callback
 */
- (void)updateGroupAnnouncementWithId:(NSString *)aGroupId
                         announcement:(NSString *)aAnnouncement
                           completion:(void (^)(AgoraGroup *aGroup, AgoraError *aError))aCompletionBlock;
// call:
[[AgoraChatClient sharedClient].groupManager updateGroupAnnouncementWithId:@"groupId" announcement:@"announcement" completion:^(AgoraGroup *aGroup, AgoraError *aError) {
    if(!aError){
        NSLog(@"Modify group announcement successfully");
    } else {
        NSLog(@"Reasons for failure to modify group announcement --- %@", aError.errorDescription);
    }
}];
```

When the group owner or administrator modifies the group announcement, other group members will receive a callback for the group announcement update

    /*!
     *  Group announcement has been updated
     *
     *  @param aGroup           group
     *  @param aAnnouncement    Group Announcement
     */
    - (void)groupAnnouncementDidUpdate:(AgoraGroup *)aGroup
                          announcement:(NSString *)aAnnouncement;

### Get a list of group shared files

``` objc
/*!
 *  Get a list of group shared files
 *
 *  @param aGroupId         Group ID
 *  @param aPageNum         Get the which pages
 *  @param aPageSize        How many to get
 *  @param aCompletionBlock Completed callback
 */
- (void)getGroupFileListWithId:(NSString *)aGroupId
                    pageNumber:(NSInteger)aPageNum
                      pageSize:(NSInteger)aPageSize
                    completion:(void (^)(NSArray *aList, AgoraError *aError))aCompletionBlock;

// call:
[[AgoraChatClient sharedClient].groupManager getGroupFileListWithId:@"groupId" pageNumber:1 pageSize:10 completion:^(NSArray *aList, AgoraError *aError) {
    if(!aError){
        // The aList array contains the AgoraGroupSharedFile objects
        NSLog(@"Get the list of group shared files successfully --- %@", aList);
    } else {
        NSLog(@"Reasons for failure to get the list of group shared files --- %@", aError.errorDescription);
    }
}];
```

### Upload and download group shared files

``` objc
/*!
 *  Upload group shared files
 *
 *  @param aGroupId         Group ID
 *  @param aFilePath        File path
 *  @param pError           Error message
 *
 *  @result    Group instance
 */
- (void)uploadGroupSharedFileWithId:(NSString *)aGroupId
                           filePath:(NSString*)aFilePath
                           progress:(void (^)(int progress))aProgressBlock
                         completion:(void (^)(AgoraGroupSharedFile *aSharedFile, AgoraError *aError))aCompletionBlock;

/*!
 *  Download group shared files
 *
 *  @param aGroupId         Group ID
 *  @param aFilePath        File path
 *  @param aSharedFileId    Shared File ID
 *  @param aProgressBlock   File download progress callback block
 *  @param aCompletionBlock completed callback block
 */
- (void)downloadGroupSharedFileWithId:(NSString *)aGroupId
                             filePath:(NSString *)aFilePath
                         sharedFileId:(NSString *)aSharedFileId
                             progress:(void (^)(int progress))aProgressBlock
                           completion:(void (^)(AgoraGroup *aGroup, AgoraError *aError))aCompletionBlock;

// call:
[[AgoraChatClient sharedClient].groupManager uploadGroupSharedFileWithId:@"groupId" filePath:@"filePath" progress:^(int progress) {
    NSLog(@"Upload file progress --- %d", progress);
} completion:^(AgoraGroupSharedFile *aSharedFile, AgoraError *aError) {
    if(!aError){
        // AgoraGroupSharedFile contains the file name, file publisher, file creation time, file size
        NSLog(@"Upload group shared files successfully");
    } else {
        NSLog(@"Reasons for failure to upload group shared files --- %@", aError.errorDescription);
    }
}];

// call:
[[AgoraChatClient sharedClient].groupManager downloadGroupSharedFileWithId:@"groupId" filePath:@"filePath" sharedFileId:@"sharedFileId" progress:^(int progress) {
    NSLog(@"Download file progress --- %d", progress);
} completion:^(AgoraGroup *aGroup, AgoraError *aError) {
    if(!aError){
        NSLog(@"Download group share file successfully");
    } else {
        NSLog(@"Reasons for failure to download group shared files --- %@", aError.errorDescription);
    }
}];
```

When someone in the group uploads a group shared file, other group members will get a callback that a new group file has been uploaded.

``` objc
/*!
 *  A user uploads a group shared file
 *
 *  @param aGroup       Group
 *  @param aSharedFile  shared file
 */
- (void)groupFileListDidUpdate:(AgoraGroup *)aGroup
               addedSharedFile:(AgoraGroupSharedFile *)aSharedFile;
```

### Delete group shared files

``` objc
/*!
 *  Delete group shared files
 *
 *  @param aGroupId         group ID
 *  @param aSharedFileId    Shared File ID
 *  @param aCompletionBlock Completed callback
 */
- (void)removeGroupSharedFileWithId:(NSString *)aGroupId
                       sharedFileId:(NSString *)aSharedFileId
                         completion:(void (^)(AgoraGroup *aGroup, AgoraError *aError))aCompletionBlock;

// call:
[[AgoraChatClient sharedClient].groupManager removeGroupSharedFileWithId:@"groupId" sharedFileId:@"sharedFileId" completion:^(AgoraGroup *aGroup, AgoraError *aError) {
    if(!aError){
        NSLog(@"Delete group shared files successfully");
    } else {
        NSLog(@"Reasons for failure to delete group shared files --- %@", aError.errorDescription);
    }
}];
```

When someone in the group deletes a group shared file, other group members will receive a callback when someone deletes a group file

``` objc
/*!
 *  Some users delete group shared files
 *
 *  @param aGroup       Group
 *  @param aFileId      Shared File ID
 */
- (void)groupFileListDidUpdate:(AgoraGroup *)aGroup
             removedSharedFile:(NSString *)aFileId;
```

### Modify group extension information

``` objc
/*!
 *  Modify group extension information, requires Owner privileges
 *
 *  @param aGroupId         group ID
 *  @param aExt             extension information
 *  @param aCompletionBlock Completed callback
 */
- (void)updateGroupExtWithId:(NSString *)aGroupId
                         ext:(NSString *)aExt
                  completion:(void (^)(AgoraGroup *aGroup, AgoraError *aError))aCompletionBlock;

// call:
[[AgoraChatClient sharedClient].groupManager updateGroupExtWithId:@"groupId" ext:@"ext" completion:^(AgoraGroup *aGroup, AgoraError *aError) {
    if(!aError){
        NSLog(@"Modify group extension information successfully");
    } else {
        NSLog(@"Reasons for failure to modify group extension information --- %@", aError.errorDescription);
    }
}];
```

## Group Member Management

### Change of group leader

Only the Owner permission can call the interface.

***Api:***

``` objc
/*!
 *  Changing the group owner requires Owner permission.
 *
 *  Synchronous method, which blocks the current thread
 *
 *  @param aGroupId   group ID
 *  @param aNewOwner  New group owner
 *  @param pError     Error message
 *
 *  @result     return Group instance
 */
- (AgoraGroup *)updateGroupOwner:(NSString *)aGroupId
                     newOwner:(NSString *)aNewOwner
                        error:(AgoraError **)pError;

/*! 
 *  hanging the group owner requires Owner permission
 *
 *  @param aGroupId   group ID
 *  @param aNewOwner  New group owner
 *  @param aCompletionBlock Completed callback
 */
- (void)updateGroupOwner:(NSString *)aGroupId
                newOwner:(NSString *)aNewOwner
              completion:(void (^)(AgoraGroup *aGroup, AgoraError *aError))aCompletionBlock;

```

***Delegate:***

``` objc
/*!
 *  Group owner has updates
 *
 *  @param aGroup       Group
 *  @param aNewOwner    New group owner
 *  @param aOldOwner    Old group owner
 */
- (void)groupOwnerDidUpdate:(AgoraGroup *)aGroup
                   newOwner:(NSString *)aNewOwner
                   oldOwner:(NSString *)aOldOwner;
                   
// call:
[[AgoraChatClient sharedClient].groupManager updateGroupOwner:@"groupId" newOwner:@"newOwner" completion:^(AgoraGroup *aGroup, AgoraError *aError) {
    if (!aError) {
        NSLog(@"Group owner updated successfully");
    } else {
        NSLog(@"Reasons for group owner update failure --- %@", aError.errorDescription);
    }   
}];                   
                   
```

### Add group administrator

Only Owner can call.

***Api:***

``` objc
/*!
 *  To add group administrator, requires Owner permission
 *
 *  Synchronous method, which blocks the current thread
 *
 *  @param aAdmin     Group administrator to add
 *  @param aGroupId   group ID
 *  @param pError     Error message
 *
 *  @result    return Group instance
 */
- (AgoraGroup *)addAdmin:(NSString *)aAdmin
              toGroup:(NSString *)aGroupId
                error:(AgoraError **)pError;

/*!
 *  Add group administrator, requires Owner permission
 *
 *  @param aAdmin     Group administrator to add
 *  @param aGroupId   group ID
 *  @param aCompletionBlock Completed callback
 */
- (void)addAdmin:(NSString *)aAdmin
         toGroup:(NSString *)aGroupId
      completion:(void (^)(AgoraGroup *aGroup, AgoraError *aError))aCompletionBlock;
      
// call:
[[AgoraChatClient sharedClient].groupManager addAdmin:@"adminName" toGroup:@"groupId" completion:^(AgoraGroup *aGroup, AgoraError *aError) {
    if (!aError) {
        NSLog(@"Add group administrator successfully");
    } else {
        NSLog(@"Reasons for failure to add group administrator --- %@", aError.errorDescription);
    }       
}];           
```

***Delegate:***

``` objc
/*!
 *  A member has been added to the administrator list
 *
 *  @param aGroup    Group
 *  @param aAdmin    Members added to the admin list
 */
- (void)groupAdminListDidUpdate:(AgoraGroup *)aGroup
                     addedAdmin:(NSString *)aAdmin;
```

### Remove group administrator

Only Owner can call.

***Api:***

``` objc
/*!
 *  Remove group administrator, requires Owner permission
 *
 *  @param aAdmin     Group administrator to add
 *  @param aGroupId   group ID
 *  @param aCompletionBlock Completed callback
 */
- (void)removeAdmin:(NSString *)aAdmin
          fromGroup:(NSString *)aGroupId
         completion:(void (^)(AgoraGroup *aGroup, AgoraError *aError))aCompletionBlock;
         
// call:
[[AgoraChatClient sharedClient].groupManager removeAdmin:@"adminName" fromGroup:@"groupId" completion:^(AgoraGroup *aGroup, AgoraError *aError) {
    if (!aError) {
        NSLog(@"Remove group administrator successfully");
    } else {
        NSLog(@"Reasons for failure to remove group administrators --- %@", aError.errorDescription);
    }  
}];         
```

***Delegate:***

``` objc
/*!
 *  A member was removed from the mute list
 *
 *  @param aGroup           Group
 *  @param aMutedMembers    Members who have been removed from the mute list
 */
- (void)groupMuteListDidUpdate:(AgoraGroup *)aGroup
           removedMutedMembers:(NSArray *)aMutedMembers;
```

### Remove group members

Only Owner or Administrator can call.

``` objc
/*!
 *  Move group members out of the group
 *
 *  @param aUsers           List of users to remove from the group
 *  @param aGroupId         Group ID
 *  @param aCompletionBlock Completed callback
 */
- (void)removeMembers:(NSArray *)aUsers
            fromGroup:(NSString *)aGroupId
           completion:(void (^)(AgoraGroup *aGroup, AgoraError *aError))aCompletionBlock;
           
// call:
[[AgoraChatClient sharedClient].groupManager removeMembers:@[@"user1"] fromGroup:@"groupId" completion:^(AgoraGroup *aGroup, AgoraError *aError) {
    if (!aError) {
        NSLog(@"remove group members from the group successfully");
    } else {
        NSLog(@"Reasons for failure to remove group members from the group --- %@", aError.errorDescription);
    }       
}];           
```

### mute group members

Those with high permission can mute those with low permission, and vice versa are not allowed

***Api:***

``` objc
/*!
 *  Banning a group member, requires Owner / Admin privileges.
 *
 *  @param aMuteMembers         List of members to ban <NSString>
 *  @param aMuteMilliseconds    Duration of ban (in milliseconds, if it is "-1" means permanent ban)
 *  @param aGroupId             group ID
 *  @param aCompletionBlock     Completed callback
 */
- (void)muteMembers:(NSArray *)aMuteMembers
   muteMilliseconds:(NSInteger)aMuteMilliseconds
          fromGroup:(NSString *)aGroupId
         completion:(void (^)(AgoraGroup *aGroup, AgoraError *aError))aCompletionBlock;
         
// call:
[[AgoraChatClient sharedClient].groupManager muteMembers:@[@"user1"] muteMilliseconds:10000 fromGroup:@"groupId" completion:^(AgoraGroup *aGroup, AgoraError *aError) {
    if (!aError) {
        NSLog(@"muted a group member successfully");
    } else {
        NSLog(@"Reasons for the failure to ban a group member --- %@", aError.errorDescription);
    }        
}];        
```

***Delegate:***

``` objc
/*!
 *  A member has been added to the ban list
 *
 *  @param aGroup           Group
 *  @param aMutedMembers    muted members
 *  @param aMuteExpire      Ban expiration time, currently unavailable
 */
- (void)groupMuteListDidUpdate:(AgoraGroup *)aGroup
             addedMutedMembers:(NSArray *)aMutedMembers
                    muteExpire:(NSInteger)aMuteExpire;
```

### Unmute

Those with high permission can mute those with low permission, and vice versa are not allowed

***Api:***

``` objc
/*!
 *  Unblock, need Owner / Admin permission
 *
 *  @param aMuteMembers     Unmuted members list<NSString>
 *  @param aGroupId          group ID
 *  @param aCompletionBlock Completed callback
 */
- (void)unmuteMembers:(NSArray *)aMembers
            fromGroup:(NSString *)aGroupId
           completion:(void (^)(AgoraGroup *aGroup, AgoraError *aError))aCompletionBlock;
           
// call:
[[AgoraChatClient sharedClient].groupManager unmuteMembers:@[@"user1"] fromGroup:@"groupId" completion:^(AgoraGroup *aGroup, AgoraError *aError) {
    if (!aError) {
        NSLog(@"Unmute successfully");
    } else {
        NSLog(@"Reasons for unmute failure --- %@", aError.errorDescription);
    }       
}];           
 
```

***Delegate:***

``` objc
/*!
 *  A member was removed from the mute list
 *
 *  @param aGroup           group
 *  @param aMutedMembers    members removed from the mute list
 */
- (void)groupMuteListDidUpdate:(AgoraGroup *)aGroup
           removedMutedMembers:(NSArray *)aMutedMembers;
```

### Set a full mute

Group owners and group administrators will not be muted by default

***Api:***

``` objc
/*!
 *  \~chinese
 *  Set a full ban, need Owner / Admin permission
 *
 *  @param aGroupId         Group ID
 *  @param aCompletionBlock Completed callback
 *
 *  \~english
 *  mute all members, need Owner / Admin permissions
 *
 *  @param aGroupId         Group id
 *  @param aCompletionBlock The callback block of completion
 *
 */
- (void)muteAllMembersFromGroup:(NSString *)aGroupId
                     completion:(void(^)(AgoraGroup *aGroup, AgoraError *aError))aCompletionBlock;
         
// call:
[[AgoraChatClient sharedClient].groupManager muteAllMembersFromGroup:@"groupId" completion:^(AgoraGroup *aGroup, AgoraError *aError) {
    if (!aError) {
        NSLog(@"All members muted successfully");
    } else {
        NSLog(@"Reasons for full muted failure --- %@", aError.errorDescription);
    }
}];        
```

***Delegate:***

``` objc
/*!
*  \~chinese
*  Group all ban status change
*
*  @param aGroup           Group
*  @param aMuted           Whether all muted
*
*  \~english
*  Group members are all muted
*
*  @param aGroup           Group
*  @param aMuted           All member muted
*/
- (void)groupAllMemberMuteChanged:(AgoraGroup *)aGroup
                 isAllMemberMuted:(BOOL)aMuted;
```

### Set full unmuted

***Api:***

``` objc
/*!
 *  \~chinese
 *  Set full unmuted，Requires Owner / Admin privileges
 *
 *  @param aGroupId         Group ID
 *  @param aCompletionBlock Completed callback
 *
 *  \~english
 *  unmute all members, need Owner / Admin permissions
 *
 *  @param aGroupId         Group id
 *  @param aCompletionBlock The callback block of completion
 *
 */
- (void)unmuteAllMembersFromGroup:(NSString *)aGroupId
                       completion:(void(^)(AgoraGroup *aGroup, AgoraError *aError))aCompletionBlock;
           
// call:
[[AgoraChatClient sharedClient].groupManager unmuteAllMembersFromGroup:@"groupId" completion:^(AgoraGroup *aGroup, AgoraError *aError) {
    if (!aError) {
        NSLog(@"Set full unmuted successfully")
    } else {
        NSLog(@"Reasons for full unmuted failure --- %@", aError.errorDescription);
    }
}];          
 
```

***Delegate:***

``` objc
/*!
*  \~chinese
*  Group all mute status change
*
*  @param aGroup           group
*  @param aMuted           whether full muted
*
*  \~english
*  Group members are all muted
*
*  @param aGroup           Group
*  @param aMuted           All member muted
*/
- (void)groupAllMemberMuteChanged:(AgoraGroup *)aGroup
                 isAllMemberMuted:(BOOL)aMuted;
```

### Add members to the group blacklist

Only Owner has the permission to call the interface, and only Owner have the permission to view the group blacklist.

owner can add group members and people who are not group members to the group blacklist.

``` objc
/*!
 *  Add people to the group blacklist, requires owner permission
 *
 *  @param aMembers         Users to be blacklisted
 *  @param aGroupId         Group ID
 *  @param aCompletionBlock Completed callback
 */
- (void)blockMembers:(NSArray *)aMembers
           fromGroup:(NSString *)aGroupId
          completion:(void (^)(AgoraGroup *aGroup, AgoraError *aError))aCompletionBlock; 

// call:
[[AgoraChatClient sharedClient].groupManager blockMembers:@[@"users1"] fromGroup:@"groupId" completion:^(AgoraGroup *aGroup, AgoraError *aError) {
    if (!aError) {
        NSLog(@"Add people to group blacklist successfully");
    } else {
        NSLog(@"Reasons for failure to add people to group blacklist --- %@", aError.errorDescription);
    }       
}];
```

### Remove members from group blacklist

Only Owner has the permission to call the interface, and only Owner have the permission to view the group blacklist.

Remove from the group blacklist, the user is no longer in the group and needs to rejoin the group.

``` objc
/*!
 *  Kick people from the group blacklist, requires owner permission
 *
 *  @param aMembers         List of usernames to remove from the blacklist
 *  @param aGroupId         group ID
 *  @param aCompletionBlock Completed callback
 */
- (void)unblockMembers:(NSArray *)aMembers
             fromGroup:(NSString *)aGroupId
            completion:(void (^)(AgoraGroup *aGroup, AgoraError *aError))aCompletionBlock;

// call:
[[AgoraChatClient sharedClient].groupManager unblockMembers:@[@"users1"] fromGroup:@"groupId" completion:^(AgoraGroup *aGroup, AgoraError *aError) {
    if (!aError) {
        NSLog(@"Successfully remove people from the group blacklist");
    } else {
        NSLog(@"Reasons for failure to remove people from group blacklist --- %@", aError.errorDescription);
    } 
}];
```

## Group messages

### Block/unblock group messages

Does not allow calls with Owner permissions.

``` objc
/*!
 *  Block group messages, the server no longer sends messages from this group to the user, the owner cannot block group messages
 *
 *  @param aGroupId         group ID to blocke
 *  @param aCompletionBlock Completed callback
 */
- (void)blockGroup:(NSString *)aGroupId
        completion:(void (^)(AgoraGroup *aGroup, AgoraError *aError))aCompletionBlock;
        
// call:
[[AgoraChatClient sharedClient].groupManager blockGroup:@"groupId" completion:^(AgoraGroup *aGroup, AgoraError *aError) {
    if (!aError) {
        NSLog(@"Successfully block group messages");
    } else {
        NSLog(@"Reasons for failure to mute group messages --- %@", aError.errorDescription);
    }        
}];        

/*!
 *  Unblock group messages
 *
 *  @param aGroupId         To unblock group ID
 *  @param aCompletionBlock Completed callback
 */
- (void)unblockGroup:(NSString *)aGroupId
          completion:(void (^)(AgoraGroup *aGroup, AgoraError *aError))aCompletionBlock;
          
// call:
[[AgoraChatClient sharedClient].groupManager unblockGroup:@"groupId" completion:^(AgoraGroup *aGroup, AgoraError *aError) {
    if (!aError) {
        NSLog(@"Block group messages successfully");
    } else {
        NSLog(@"Reasons for failure to unblock group messages --- %@", aError.errorDescription);
    }       
}];         

```

## Manage APNS offline push for groups

details in [APNS offline push-set whether the specified group receives APNS](/im/ios/apns/offline#设置指定群组是否接收_apns)。

## Get the group related to the logger

View all groups where the currently logged in account is located, including created and joined groups, 2 methods are provided.

1.Get a list of groups related to me from the server

``` objc
[[AgoraChatClient sharedClient].groupManager getJoinedGroupsFromServerWithPage:1 pageSize:50 completion:^(NSArray *aList, AgoraError *aError) {
    if (!aError) {
        NSLog(@"Get group list successfully --- %@", aList);
    } else {
        NSLog(@"Reasons for failure to get group list --- %@", aError.errorDescription);
    }
}];
```

2\. Fetch the value in memory

Get the list of groups from local after getting the list of groups related to the login from the server

``` objc
// Get all groups from memory. At the first time, load from database
NSArray *groupList = [[AgoraChatClient sharedClient].groupManager getJoinedGroups];
```

## Get public groups

Get the public groups in the specified range.

``` objc
/*!
 *  Get the public groups in the specified range from the server
 *
 *  @param aCursor          Get the cursor of the public group. For the first call, pass null
 *  @param aPageSize        The number of results expected to be returned. retuen all results at once if value < 0
 *  @param aCompletionBlock Completed callback
 */
- (void)getPublicGroupsFromServerWithCursor:(NSString *)aCursor
                                   pageSize:(NSInteger)aPageSize
                                 completion:(void (^)(AgoraCursorResult *aResult, AgoraError *aError))aCompletionBlock;
                                 
// call:                                 
[[AgoraChatClient sharedClient].groupManager getPublicGroupsFromServerWithCursor:nil pageSize:50 completion:^(AgoraCursorResult *aResult, AgoraError *aError) {
    if (!aError) {
        NSLog(@"Get public group successfully --- %@", aResult);
    } else {
        NSLog(@"Reasons for failure to get public groups --- %@", aError.errorDescription);
    }  
}];
```

------------------------------------------------------------------------


