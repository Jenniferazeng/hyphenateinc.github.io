---
title: User Metadata
keywords: ios
sidebar: ios_sidebar
toc: true
permalink: ios_user.html
folder: ios
---

# User attributes

## Introduction to the product

User attributes are user's information, including: nickname, avatar, age, mobile phone number, etc.
User attributes are optional services. If the user does not want sensitive information to be stored in the Chat server, users can maintain it by themselves.

Use the user attribute function to obtain the following capabilities:

-   standard reading and writing capability of user attributes, including: nickname, avatar url, email address, phone number, gender, signature, birthday;
-   Customized reading and writing capabilities of Customize user attributes provide custom extensions. It can add multiple attributes through JSON strings.

## overview of Integration 

The SDK provides user attribute hosting services, allowing users to save common fields such as avatars, nicknames, and an extended field. If the user does not want AgoraChat to get your user attribute information, the user needs to maintain the relevant data by himself.

The user attribute management module can be got from the SDK using the following methods as follows

    [[AgoraChatClient sharedClient] userInfoManager];

The user attribute information that can be managed by AgoraChat is as follows:

    @interface AgoraUserInfo : NSObject<NSCopying>
    @property (nonatomic,copy) NSString *userId; /*! *\~ Chat user ID *\~english user's id */
    @property (nonatomic,copy) NSString *nickName; /*! *\~ User name. maximum of 64 bytes is recommended *\~english user's nickname */
    @property (nonatomic,copy) NSString *avatarUrl; /*! *\~ User avatar address. maximum of 256 bytes is recommended *\~english user's avatar file uri */
    @property (nonatomic,copy) NSString *mail; /*! *\~ User email address. maximum of 64 bytes is recommended *\~english user's mail  address */
    
    @property (nonatomic,copy) NSString *phone; /*! *\~ User contact information. maximum of 32 bytes is recommended *\~english user's phone  number */
    @property (nonatomic) NSInteger gender; /*! *\~ User gender, default is 0, 1 means male, 2 means female, other values are illegal *\~english user's phone  number */
    @property (nonatomic,copy) NSString* sign; /*! *\~ User signature, maximum of 256 bytes is recommended*\~english user's sign info */
    @property (nonatomic,copy) NSString* birth; /*! *\~ The user’s birthday, the  maximum of 64 bytes is recommended *\~english user's birth */
    @property (nonatomic,copy) NSString *ext; /*! *\~ extended field *\~english extention info */
    @end

## Set user attributes

Each user can only set his own user attributes, and cannot modify others'.
When setting user attributes, you can set all attributes of the user, or only one attribute of the user

-   Set all attributes of the user

The interface for setting all attributes of the user is as follows

    /*!
     *  \~chinese
     *  Set all your user attributes
     *
     *  @param aUserData       User attribute data to be set
     *  @param aCompletionBlock     Completed callback
     *
     *  \~english
     *  Set all own user info
     *
     *  @param aUserData       The user info data to set
     *  @param aCompletionBlock    The completion callback
     */
    - (void)updateOwnUserInfo:(AgoraUserInfo*)aUserData
                    completion:(void (^)(AgoraUserInfo*aUserInfo,AgoraError *aError))aCompletionBlock;

The calling process is as follows:

    AgoraUserInfo* userInfo = [[AgoraUserInfo alloc] init];
    userInfo.nickName = @"nickname";
    userInfo.avatarUrl = @"http://www.a.com/Image1.png";
    userInfo.mail = @"1111@qq.com";
    userInfo.phone = @"1234556666";
    userInfo.gender = 1;
    userInfo.sign = @"my signature";
    userInfo.birth = @"birthday";
    userInfo.ext = @"ext";
    [[[AgoraChatClient sharedClient] userInfoManager] updateOwnUserInfo:userInfo completion:^(AgoraUserInfo* aUserInfo,AgoraError *aError) {
            }];

-   Set user-specified attributes

The user attribute types that users can specify include:

    typedef NS_ENUM(NSInteger, AgoraUserInfoType) {
        AgoraUserInfoTypeNickName = 0,
        AgoraUserInfoTypeAvatarURL,
        AgoraUserInfoTypePhone,
        AgoraUserInfoTypeMail,
        AgoraUserInfoTypeGender,
        AgoraUserInfoTypeSign,
        AgoraUserInfoTypeBirth,
        AgoraUserInfoTypeExt = 100,
    };

The interface for setting the attributes of a specified user is as follows:

    /*!
     *  \~chinese
     *  Set your own specified user attributes
     *
     *  @param aValue        User attribute information to be set
     *  @param aType         User attribute type to be set
     *  @param aCompletionBlock     Completed callback
     *
     *  \~english
     *  Set special own user info
     *
     *  @param aValue       The user info data to set
     *  @param aType         The user info type to set
     *  @param aCompletionBlock    The completion callback
     */
    - (void)updateOwnUserInfo:(NSString*)aValue
                     withType:(AgoraUserInfoType)aType
                   completion:(void (^)(AgoraUserInfo*aUserInfo,AgoraError *aError))aCompletionBlock;

The calling process is as follow (example of nickname):

    [[[AgoraChatClient sharedClient] userInfoManager] updateOwnUserInfo:@"New nickname" withType:AgoraUserInfoTypeNickName completion:^(AgoraUserInfo* aUserInfo,AgoraError *aError) {
            }];

## Get user attributes

When getting user attributes, you can get all the attributes of the user, or you can get the specified attributes of the user, such as getting only the nickname, avatar, etc.

-   Get all attributes of the user

The interface for getting all attributes of a user is as follows:

    /*!
     *  \~chinese
     *  Get user attributes based on Chat user ID
     *
     *  @param aUserIds  			Chat user ID to get user attributes
     *  @param aCompletionBlock     Completed callback
     *
     *  \~english
     *  Get user info by id
     *
     *  @param aUserIds       The users's id to get user info
     *  @param aCompletionBlock    The completion callback
     */
    - (void)fetchUserInfoById:(NSArray<NSString*>*)aUserIds
                    completion:(void (^)(NSDictionary*aUserDatas,AgoraError *aError))aCompletionBlock;

The calling process is as follows

    [[[AgoraChatClient sharedClient] userInfoManager] fetchUserInfoById:[weakself.userIds copy] completion:^(NSDictionary *aUserDatas, AgoraError *aError) {
        if(!aError) {
            
        }
    }];

-   Get the specified user attributes of the user

The interface for getting the attributes of a specified user is as follows:

    /*!
     *  \~chinese
     *  Get user-specified attributes based on Chat user ID
     *
     *  @param aUserIds  	 		Chat user ID to get user attributes
     *  @param aType         		What types of user attributes are to be got
     *  @param aCompletionBlock     Completed callback
     *
     *  \~english
     *  Get user special info by id
     *
     *  @param aUserIds       The users's id to get user info
     *  @param aType              The user info type to get
     *  @param aCompletionBlock    The completion callback
     */
    - (void)fetchUserInfoById:(NSArray<NSString*>*)aUserIds
                          type:(NSArray<NSNumber*>*)aType
                    completion:(void (^)(NSDictionary*aUserDatas,AgoraError *aError))aCompletionBlock

The calling process is as follows:

    [[[AgoraChatClient sharedClient] userInfoManager] fetchUserInfoById:[weakself.userIds copy] type:@[[NSNumber numberWithInt:AgoraUserInfoTypeNickName],[NSNumber numberWithInt:AgoraUserInfoTypeAvatarURL]] completion:^(NSDictionary *aUserDatas, AgoraError *aError) {
        if(!aError && aUserDatas.count > 0) {
            // Store user attributes here
        }
    }];

## User avatar management

AgoraChat User-Attribute Hosting Service is not responsible for managing user avatar files, which only stores the remote url address of avatar files. Users need to use third-party file hosting services such as Alibaba Cloud and Tencent Cloud to store avatar files. When a user sets an avatar, he needs to upload the avatar file to a third-party file hosting server first, and then pass the url address obtained after uploading to the avatar url field of the user attribute to set the user attribute. When displaying the avatar, get the user attributes from the SDK first. Next,Please get the avatar's url, and then display the remote avatar image on the UI layer.

## Business card message

There is no card-type message in the SDK. The sending and receiving of card messages in the Demo use the existing custom message type, by specifying the event of the custom message as \"userCard\", and adding required Chat user ID, nickname, and avatar in the ext to display the user’s business card.

The business card message sending process is as follows:

    AgoraCustomMessageBody* body = [[AgoraCustomMessageBody alloc] initWithEvent:@"userCard" ext:@{@"uid":aUid ,@"nickname":aNickName,@"avatar":aUrl}];
    [self.chatController sendMessageWithBody:body ext:nil];

If users need to display more information in the business card, you can add more fields in ext.

For the display, please refer to the **AgoraUserCardMsgView** class in Demo


------------------------------------------------------------------------
