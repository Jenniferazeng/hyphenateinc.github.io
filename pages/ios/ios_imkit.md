---
title: ios Group
keywords: ios
sidebar: ios_sidebar
toc: true
permalink: ios_group.html
folder: ios
---
# iOS SDK's Introduction and import

------------------------------------------------------------------------

## DEMO（EaseIM App） experience

Download link：[download page](http://www.easemob.com/download/im)

# EaseIMKit User's Guide

Users who are still using the old EaseUI version can refer to the documentation of the old EaseUI version
Documentation address: [EaseUI Integration](http://docs-im.easemob.com/im/ios/other/easeui)

- ## Introduction

  What is EaseIMKit?

  EaseIMKit is a UI component library based on the Huanxin IM SDK, which provides some common UI 
  components, such as 'conversation list', 'chat interface' and 'contact list', which allow developers to quickly build custom IM applications based on their business requirements.
  The components in EaseIMKit implement UI functions while calling the the corresponding interfaces of IM SDK to implement IM-related logic and data processing. Thus, developers only need to focus on their own business or personalization when using EaseIMKit.
  
  EaseIMKit source code address

  - [EaseIMKit project](https://github.com/easemob/easeui_ios/tree/EaseIMKit)

  Use EaseIMKit Easemob IM App address.

  - [Easemob IM](https://github.com/easemob/chat-ios)

  ## Import

  ### Supported System Version Requirements

  - EaseIMKit supports iOS 10.0 and above system versions

```{=html}
<!-- -->
```
-   EaseIM supports iOS 11.0 and above

### Quick integration

#### pod integration

``` java
pod 'EaseIMKit'
```

**Note:**

EaseIMKit: corresponds to HyphenateChat SDK (HyphenateChat does not contain real-time audio and video communication.  EaseIMKit does not contain audio and video communication, EaseIM depends on audio and video communication library. EaseCallKit the implementation of audio and video communication features)

EaseIMKit includes the functions of taking photos, sending voice, sending pictures, sending videos, sending locations, sending files, and requires the permission to use recording, camera, photo album, and geolocation. You need to add the corresponding permissions in your project's info.plist.

### Initialization

Step 1: import the relevant header files

``` java
 #import <EaseIMKit/EaseIMKit.h>
```

Step 2: Call the following methods in the AppDelegate of the project EaseIMKitManager's initialization method along with the initialization of the Easemob sdk.(Note: This method does not need to be called repeatedly)

``` java
 - (BOOL)application:(UIApplication *)application didFinishLaunchingWithOptions:(NSDictionary *)launchOptions {
    // Override point for customization after application launch.
    EMOptions *options = [EMOptions optionsWithAppkey:@"Your APPKEY"];
    [EaseIMKitManager initWithEMOptions:options];
    //Do the login operation again, you can accurately receive the system notification
    return YES;
}
```

EaseIMKitManager
It mainly contains methods such as system notification (friend application, group invitation/application) callbacks, total number of unread callbacks, etc.
Users need to register their own class to EaseIMKitManagerDelegate to receive the unread total change callback. Users need to add EaseIMKitSystemNotiDelegateproxy to receive system notification-related callbacks.

The system notification related callback interface, system notification is constructed as a local conversation, and each new notification is constructed as a local message. The conversationId of the
Conversation constructed by the system notifications is @\"emsystemnotificationid\"

#### whether system notifications are required

``` java
/*!
 @method
 @brief Whether to need system notification: friends / group application, etc.
 @discussion   requires system notification by default
 @result Return YES: Required NO: Not required
 */
- (BOOL)isNeedsSystemNotification;
```

#### Callback interface for information displayed by the system notification received

``` java
/*!
 @method
 @brief Request received to return display information
 @param   conversationId   conversation ID
   * For single chat type, the conversation ID is also the name of the target user.
   * For the group chat type, the conversation ID is also the ID of the target group, and is different from the group name.
   * For chat room type, the conversation ID is also the ID of the chat room, and is different from the name of the chat room.

 @param   requestUser   Requesting Party
 @param   reason   Reason for request
 @result    Return the information displayed in the system notification
 */
- (NSString *)requestDidReceiveShowMessage:(NSString *)conversationId requestUser:(NSString *)requestUser reason:(EaseIMKitCallBackReason)reason;
```

For return value handling: empty string does not generate a new message / nil: use the default implementation /
Non-empty string with length greater than 0, use the string to generate a new message

#### Receive system notification extended message callback interface

``` java
/*!
 @method
 @brief 		Request received to return extended information
 @param conversationId 		conversation ID
   * For single-chat type, the conversation ID is also the name of the target user.
   * For group chat types, the conversation ID is also the ID of the target group, and is different from the group name.
   * For chat room types, the conversation ID is also the ID of the chat room, and is different from the name of the chat room.

 @param requestUser 	The requesting party
 @param reason 			reason for the request
 @result 				Return a system notification carrying an extended dictionary of information
 */
- (NSDictionary *)requestDidReceiveConversationExt:(NSString *)conversationId requestUser:(NSString *)requestUser reason:(EaseIMKitCallBackReason)reason;
```

#### Unread total change callback interface

``` java
/*!
 @method
 @brief 			Total number of unread conversations changes
 @param unreadCount Total unread count of the current conversation list
 */
- (void)conversationsUnreadCountUpdate:(NSInteger)unreadCount;
```

## Quick build

### Chat conversation quick build

Import EaseIMKit headers

``` java
 #import <EaseIMKit/EaseIMKit.h>
```

EaseIMKit provides a ready-made chat conversation ViewController, which can be embedded into your own chat controller by creating EaseChatViewController object instance(see EMChatViewController in EaseIM
to implement the integration of EaseIMKit chat conversations.
To create a chat conversation page instance, pass the user 'Easemob ID' or 'Group ID' , the conversation type (EMConversationType) and the chat view configuration data model must be passed in EaseChatViewModel instance.

``` java
EaseChatViewModel *viewModel = [[EaseChatViewModel alloc]init];
EaseChatViewController *chatController = [EaseChatViewController initWithConversationId:@"custom"
                                              conversationType:EMConversationTypeChat
                                                  chatViewModel:viewModel];
[self addChildViewController:chatController];
[self.view addSubview:chatController.view];
chatController.view.frame = self.view.bounds;
```

After the chat controller embeds its own chat page, it also needs to pass in the message list messageList for the EaseChatViewControlle to display.

``` java
//isScrollBottom 	Whether to slide to the bottom of the page
- (void)loadData:(BOOL)isScrollBottom
{
    __weak typeof(self) weakself = self;
    void (^block)(NSArray *aMessages, EMError *aError) = ^(NSArray *aMessages, EMError *aError) {
        dispatch_async(dispatch_get_main_queue(), ^{
            //Pass the EaseChatViewController to message list messageList
            //isScrollBottom whether to scroll to the bottom of the page isInsertBottom whether the message data set is inserted at the bottom of the message list
            //no need to do any processing on the list before passing in the message list, just pass in the list data, otherwise it will fail to refresh the UI
            [weakself.chatController refreshTableViewWithData:aMessages  isInsertBottom:NO  isScrollBottom:isScrollBottom];
        });
    };
  [self.conversation loadMessagesStartFromId:nil count:50   searchDirection:EMMessageSearchDirectionUp completion:block];
}
```

### conversation List Quick Build

Importing EaseIMKit headers

``` java
 ### import <EaseIMKit/EaseIMKit.h>
```

You can embed the EaseIMKit conversation list page inside your own chat controller, create a conversation list instance. To instantiate the conversation list ,you must pass in the conversation list view data configuration model EaseConversationViewModel instance.

``` java
EaseConversationViewModel *viewModel = [[EaseConversationViewModel alloc] init];

EaseConversationsViewController *easeConvsVC = [[EaseConversationsViewController alloc] initWithModel:viewModel];
easeConvsVC.delegate = self;
[self addChildViewController:easeConvsVC];
[self.view addSubview:easeConvsVC.view];
[easeConvsVC.view mas_makeConstraints:^(MASConstraintMaker *make) {
    make.size.equalTo(self.view);
}];
```

### Address Book Quick Build

Importing EaseIMKit headers

``` java
 ### import <EaseIMKit/EaseIMKit.h>
```

You can embed the EaseIMKit conversation list page inside your own chat controller. To create an address book instance, and you must pass in the address book view data model EaseContactsViewModel instance to build the address book UI interface.

``` java
EaseContactsViewModel *model = [[EaseContactsViewModel alloc] init];
EaseContactsViewController *contactsVC = [[EaseContactsViewController alloc] initWithModel:model];
//Address book header function area (add friend/group chat/chat room entrance)
contactsVC.customHeaderItems = [self items];
contactsVC.delegate = self;
[self addChildViewController:contactsVC];
[self.view addSubview:contactsVC.view];
[contactsVC.view mas_makeConstraints:^(MASConstraintMaker *make) {
    make.size.equalTo(self.view);
}];
//Example of setting the function area of address book header (valid for EaseIM APP).
- (NSArray<EaseUserDelegate> *)items {
    EMContactModel *newFriends = [[EMContactModel alloc] init];
    newFriends.huanXinId = @"newFriend";
    newFriends.nickname = @"New Friends";
    newFriends.avatar = [UIImage imageNamed:@"newFriend"];
    EMContactModel *groups = [[EMContactModel alloc] init];
    groups.huanXinId = @"groupList";
    groups.nickname = @"Group chat";
    groups.avatar = [UIImage imageNamed:@"groupchat"];
    EMContactModel *chatooms = [[EMContactModel alloc] init];
    chatooms.huanXinId = @"chatroomList";
    chatooms.nickname = @"Chat Room";
    chatooms.avatar = [UIImage imageNamed:@"chatroom"];
    return (NSArray<EaseUserDelegate> *)@[newFriends, groups, chatooms];
}
```

## Set the style

### Chat conversation style configuration

The chat conversation can be configured with the following parameters.

``` java
@property (nonatomic, strong) UIColor *chatViewBgColor; //chat page background color
@property (nonatomic, strong) UIColor *chatBarBgColor; //input area background color
@property (nonatomic, strong) EaseExtFuncModel *extFuncModel; //input area extended function data model
@property (nonatomic, strong) UIColor *msgTimeItemBgColor; //timeline background color
@property (nonatomic, strong) UIColor *msgTimeItemFontColor; //timeline font color
@property (nonatomic, strong) UIImage *receiveBubbleBgPicture; //the bubble of the received message
@property (nonatomic, strong) UIImage *sendBubbleBgPicture; //the bubble of the message sent
@property (nonatomic, strong) UIColor *contentFontColor; //text message font color
@property (nonatomic) CGFloat contentFontSize; //text message font size
@property (nonatomic) UIEdgeInsets bubbleBgEdgeInset; //the message bubble background image protection area
@property (nonatomic) EaseInputBarStyle inputBarStyle; //input area type: (all functions, no voice, no expression, no expression and voice, plain text)
@property (nonatomic) EaseAvatarStyle avatarStyle; //avatar style
@property (nonatomic) CGFloat avatarCornerRadius; //Avatar corner size Default: 0 (only avatar types with rounded corners are in effect)
//Only group chat can be set
@property (nonatomic) EaseAlignmentStyle msgAlignmentStyle; //Chat area message alignment
```

the parameters inside: EaseExtFuncModel
The input area extended function data configuration model (chat conversation page camera, photo album, audio/video communication, etc. area)  which contains the following parameters that can be configured.

```
@property (nonatomic, strong) UIColor *iconBgColor;// background color of the view where the icon is located
@property (nonatomic, strong) UIColor *viewBgColor;//view background color
@property (nonatomic, strong) UIColor *fontColor;//font color
@property (nonatomic, assign) CGFloat fontSize;//font size
@property (nonatomic, assign) CGSize collectionViewSize;//view size
```

the parameter: inputBarStyle (input area) contains five types.

```
typedef NS_ENUM(NSInteger, EaseInputBarStyle) {
    EaseInputBarStyleAll = 1, 					//all functions
    EaseInputBarStyleNoAudio, 					//no voice
    EaseInputBarStyleNoEmoji, 					//no emoji
    EaseInputBarStyleNoAudioAndEmoji, 			//no expressions and voice
    EaseInputBarStyleOnlyText, 					//Plain text
};
```

The parameter: EaseAlignmentStyle (message alignment, only available for group chat) contains two 

```
typedef enum {
    EaseAlignmentNormal = 1, //left-right alignment
    EaseAlignmentlLeft, //left-aligned
} EaseAlignmentStyle;
```

The instantiated chat controller can refresh the page by resetting the view UI configuration model

```
//reset the chat controller
- (void)resetChatVCWithViewModel:(EaseChatViewModel *)viewModel;
```

Chat page background color. example of input area color configuration .

![background-color, input-area-color](/im/ios/other/chatui.png){width="200"}

Example of chat conversation input area type parameters  configuration.

![all functions, voice call not available, emoticons not available, voice call and emoticons not available, plain text](/im/ios/other/inputbar.png){width="200"}

Example of input area extended function parameters configuration .

![input-area extension](/im/ios/other/inputbarext.jpg){width="200"}

Chat conversation group chat message with same left alignment, timeline background color, time font color configuration example: 

![group chat message with left alignment, timeline background color, time font color](/im/ios/other/aligmentleft.jpg){width="200"}

### conversation list style configuration

The conversation list can be configured with the following parameters.

``` java
@property (nonatomic) EaseAvatarStyle avatarType; // avatar style
@property (nonatomic, strong) UIImage *defaultAvatarImage; // default avatar image
@property (nonatomic) CGSize avatarSize; // avatar size
@property (nonatomic) UIEdgeInsets avatarEdgeInsets; // avatar position
@property (nonatomic, strong) UIFont *nameLabelFont; // nickname font
@property (nonatomic, strong) UIColor *nameLabelColor; // nickname color
@property (nonatomic) UIEdgeInsets nameLabelEdgeInsets; // nickname position
@property (nonatomic, strong) UIFont *detailLabelFont; // detail font
@property (nonatomic, strong) UIColor *detailLabelColor; // detail font color
@property (nonatomic) UIEdgeInsets detailLabelEdgeInsets; // detail position
@property (nonatomic, strong) UIFont *timeLabelFont; // time font
@property (nonatomic, strong) UIColor *timeLabelColor; // time font color
@property (nonatomic) UIEdgeInsets timeLabelEdgeInsets; // time position
@property (nonatomic) EMUnReadCountViewPosition badgeLabelPosition; // unread count display style
@property (nonatomic, strong) UIFont *badgeLabelFont; // unread count font
@property (nonatomic, strong) UIColor *badgeLabelTitleColor; // Unreadable number color
@property (nonatomic, strong) UIColor *badgeLabelBgColor; // Unreadable background color  
@property (nonatomic) CGFloat badgeLabelHeight; // Unreadable corner label height
@property (nonatomic) CGVector badgeLabelCenterVector; // unread center offset
@property (nonatomic) int badgeMaxNum; // Unreadable display limit, will display xx+ when exceeded
```

The conversation list and contact list share the following configurable parameters with their parent classes.

``` java
@property (nonatomic) BOOL canRefresh;  // Whether it can be refreshed by pulling down
@property (nonatomic, strong) UIView *bgView;   // tableView Background image
@property (nonatomic, strong) UIColor *cellBgColor;  // UITableViewCell Background color
@property (nonatomic) UIEdgeInsets cellSeparatorInset;  // UITableViewCell Underline position
@property (nonatomic, strong) UIColor *cellSeparatorColor;  // UITableViewCell Underline color
```

### Address book style configuration

The address book can be configured with the following parameters.

``` java
@property (nonatomic) EaseAvatarStyle avatarType;   // Avatar style
@property (nonatomic, strong) UIImage *defaultAvatarImage;  // Default avatar
@property (nonatomic) CGSize avatarSize;    // Avatar size
@property (nonatomic) UIEdgeInsets avatarEdgeInsets;    // Avatar Location
@property (nonatomic, strong) UIFont *nameLabelFont;    // Nickname font
@property (nonatomic, strong) UIColor *nameLabelColor;  // Nickname color
@property (nonatomic) UIEdgeInsets nameLabelEdgeInsets;  // Nickname Location
@property (nonatomic, strong) UIFont *sectionTitleFont;  // section title Fonts
@property (nonatomic, strong) UIColor *sectionTitleColor;   // section titleColor
@property (nonatomic, strong) UIColor *sectionTitleBgColor;  // section titleBackground
@property (nonatomic) UIEdgeInsets sectionTitleEdgeInsets;  // section title Position
// section title label Height (section title view = sectionTitleLabelHeight + sectionTitleEdgeInsets.top + sectionTitleEdgeInsets.bottom)
@property (nonatomic) CGFloat sectionTitleLabelHeight;
```

and the configuration of parameters shared by the address book and conversation list

``` java
@property (nonatomic) BOOL canRefresh;  // Whether it can be refreshed by pulling down
@property (nonatomic, strong) UIView *bgView;   // tableView Background image
@property (nonatomic, strong) UIColor *cellBgColor;  // UITableViewCell Background color
@property (nonatomic) UIEdgeInsets cellSeparatorInset;  // UITableViewCell Underline position
@property (nonatomic, strong) UIColor *cellSeparatorColor;  // UITableViewCell Underline color
```

Address book add header function area: new friends, group chat, chat room schematic.

![Header ribbon: new friends, group chat, chat room and contact list](/im/ios/other/contact.png){width="200"}

## Custom Functionality Extensions

### Chat conversation Custom Functionality Extensions

After instantiating the EaseChatViewController, you can choose to implement the EaseChatViewControllerDelegate protocol (chat controller callback proxy) to receive EaseChatViewController callbacks and make further customizations.

EaseChatViewControllerDelegate

#### pull down to load more messages callback

pull down to load more messages callback (can get current first message id as reference id for load more messages in next time; current message list)

``` java
/**
 * pull down load more messages callback 
 *
 * @param firstMessageId 			first message id
 * @param messageList 				The list of current messages
 */
- (void)loadMoreMessageData:(NSString *)firstMessageId currentMessageList:(NSArray<EMMessage *> *)messageList;
```

#### custom cell

Get a custom message cell by implementing a chat control callback, based on the messageModel. User determines whether to display custom messages cell. If nil is returned, the default will be displayed.
if it returns cell, it will show the custom message cell.

``` java
/*!
 @method
 @brief Get message custom cell
 @discussion User determines whether to display custom cell based on messageModel; returns nil to display default cell, otherwise displays custom cell
 @param tableView 			The tableView of the current message view
 @param messageModel 		The data model of the message
 @result 					return custom cell
 */
- (UITableViewCell *)cellForItem:(UITableView *)tableView messageModel:(EaseMessageModel *)messageModel;
```

Example of creating a specific custom Cell.

``` java
//Customized call log cell
- (UITableViewCell *)cellForItem:(UITableView *)tableView messageModel:(EaseMessageModel *)messageModel
{
    //When the message is a single chat audio/video call message, EaseIM customizes the call log cell
    if (messageModel.type == EMMessageTypePictMixText) {
        EMMessageCell *cell = [[EMMessageCell alloc]initWithDirection:messageModel.direction type:messageModel.type];
        cell.model = messageModel;
        cell.delegate = self;
        return cell;
    }
    return nil;
}
```

The effect image of single chat audio and video call records is displayed  by custom cells.

![Custom cell to show single chat audio/video call history](/im/ios/other/callrecord.png){width="200"}

#### Callbacks for selected messages

Callback for selected message (EaseIMKit has no selected event callback for custom cells, you need to customize to implement the selected response)

``` java
/*!
 @method
 @brief 			Message click event
 @discussion 		user to customize the message selection time based on messageModel. Returns NO for custom processing, YES for default processing
 @param message 	The current clicked message
 @param userData 	The user information carried by the currently clicked message
 @result 			whether to use the default event processing
*/
- (BOOL)didSelectMessageItem:(EMMessage*)message userData:(id<EaseUserDelegate>)userData;
```

EaseIMKit selected is the message bubble, custom cell click events need to be customized to achieve, example: EaseIM Single chat call record cell click event to initiate the call again

``` java
- (void)messageCellDidSelected:(EMMessageCell *)aCell
{
    //Initiate a call by using the 'Notification' method, where the defined macros are only valid in the EaseIM APP
    NSString *callType = nil;
    NSDictionary *dic = aCell.model.message.ext;
    if ([[dic objectForKey:EMCOMMUNICATE_TYPE] isEqualToString:EMCOMMUNICATE_TYPE_VOICE])
        callType = EMCOMMUNICATE_TYPE_VOICE;
    if ([[dic objectForKey:EMCOMMUNICATE_TYPE] isEqualToString:EMCOMMUNICATE_TYPE_VIDEO])
        callType = EMCOMMUNICATE_TYPE_VIDEO;
    if ([callType isEqualToString:EMCOMMUNICATE_TYPE_VOICE])
        [[NSNotificationCenter defaultCenter] postNotificationName:CALL_MAKE1V1 object:@{CALL_CHATTER:aCell.model.message.conversationId, CALL_TYPE:@(EMCallTypeVoice)}];
    if ([callType isEqualToString:EMCOMMUNICATE_TYPE_VIDEO])
        [[NSNotificationCenter defaultCenter] postNotificationName:CALL_MAKE1V1 object:@{CALL_CHATTER:aCell.model.message.conversationId,   CALL_TYPE:@(EMCallTypeVideo)}];
}
```

#### User Profile Callback

User profile callbacks (avatar nickname, etc.)

``` java
/*!
 @method
 @brief 			return user profile
 @discussion 		User matches the corresponding user profile in their own user system based on huanxinID and returns the corresponding information, otherwise the default implementation
 @param huanxinID 	Easemob id
 @result 			Return the user profile associated with the current huanxin id
 */
- (id<EaseUserDelegate>)userData:(NSString*)huanxinID;
```

#### Callback for user selected avatar

``` java
/*!
 @method
 @brief 			Click on message avatar
 @discussion 		Get callback for user clicked avatar
 @param userData 	The user profile of the currently clicked avatar
 */

- (void)avatarDidSelected:(id<EaseUserDelegate>)userData;
```

Sample callback to get user selected avatar.

``` java
//Avatar clicked
- (void)avatarDidSelected:(id<EaseUserDelegate>)userData
{
    //EMPersonalDataViewController 	user custom personal information view
    //The sample logic is to select the message avatar and go to the personal information page of the message sender
    if (userData && userData.caseId) {
        EMPersonalDataViewController *controller = [[EMPersonalDataViewController alloc]initWithNickName:userData.caseId];
        [self.navigationController pushViewController:controller animated:YES];
    }
}
```

#### Callback for user long press on avatar

``` java
/*!
 @method
 @brief 			Click on the message avatar
 @discussion 		Get callback for user long press avatar
 @param userData 	The user profile that the current long pressed avatar refers to
 */

- (void)avatarDidLongPress:(id<EaseUserDelegate>)userData;
```

#### group notification return details

``` java
/*!
 @method
 @brief 			group notification receipt details
 @discussion 		Get the callback of the group notification which the user clicked on  (only in the group chat and for the user who clicked on the group owner)
 @param message 			Current group notification message
 @param groupId 			The group ID of the current message
 */

- (void)groupMessageReadReceiptDetail:(EMMessage*)message groupId:(NSString*)groupId;
```

Sample example to get the details of a user clicking on a group notification acknowledgement.

``` java
//group notification read receipt details
- (void)groupMessageReadReceiptDetail:(EMMessage *)message groupId:(NSString *)groupId
{
    //EMReadReceiptMsgViewController user custom group notification read receipt detail page (valid in EaseIM APP)
    EMReadReceiptMsgViewController *readReceiptControl = [[EMReadReceiptMsgViewController alloc] initWithMessage:message groupId: groupId];
    readReceiptControl.modalPresentationStyle = 0;
    [self presentViewController:readReceiptControl animated:YES completion:nil];
}
```

#### Input area callback

Current conversation input extended area data model group (UI configuration can be set in the chat view configuration data model)

``` java
/*!
 @method
 @brief 			Current conversation input extension data model group
 @param defaultInputBarItems 		Default function data model group (default order: album, camera, location, file)
 @param conversationType 			Current conversation type: single chat, group chat, chat room
 @result 			returns a set of input bar extension function
 */

- (NSMutableArray<EaseExtMenuModel*>*)inputBarExtMenuItemArray:
                (NSMutableArray<EaseExtMenuModel*>*)defaultInputBarItems 
                conversationType:(EMConversationType)conversationType;
```

Example of callback for the current conversation input extension area data model group (valid for EaseIM APP).

``` java
- (NSMutableArray<EaseExtMenuModel *> *)inputBarExtMenuItemArray:(NSMutableArray<EaseExtMenuModel *> *)defaultInputBarItems conversationType:(EMConversationType)conversationType
{
NSMutableArray<EaseExtMenuModel *> *menuArray = [[NSMutableArray<EaseExtMenuModel *> alloc]init];
//Albums
[menuArray addObject:[defaultInputBarItems objectAtIndex:0]];
//Camera
[menuArray addObject:[defaultInputBarItems objectAtIndex:1]];
//Audio and Video call
__weak typeof(self) weakself = self;
if (conversationType != EMConversationTypeChatRoom) {
    EaseExtMenuModel *rtcMenu = [[EaseExtMenuModel alloc]initWithData:[UIImage imageNamed:@"video_conf"] funcDesc:@"Audio and Video call" handle:^(NSString * _Nonnull itemDesc, BOOL isExecuted) {
        if (isExecuted) {
            [weakself chatSealRtcAction];
        }
    }];
    [menuArray addObject:rtcMenu];
}
//Position
[menuArray addObject:[defaultInputBarItems objectAtIndex:2]];
//Documents
[menuArray addObject:[defaultInputBarItems objectAtIndex:3]];
//Group Reply
if (conversationType == EMConversationTypeGroupChat) {
    if ([[EMClient.sharedClient.groupManager getGroupSpecificationFromServerWithId:_conversation.conversationId error:nil].owner isEqualToString:EMClient.sharedClient.currentUsername]) {
        EaseExtMenuModel *groupReadReceiptExtModel = [[EaseExtMenuModel alloc]initWithData:[UIImage imageNamed:@"pin_readReceipt"] funcDesc:@"group receipt" handle:^(NSString * _Nonnull itemDesc, BOOL isExecuted) {
            //Group receipt Send Message Page
            [weakself groupReadReceiptAction];
        }];
        [menuArray addObject:groupReadReceiptExtModel];
    }
}
return menuArray;
}
```

#### keyboard input change callback

``` java
/*!
 @method
 @brief 		input change in the input area keyboard callback 	Example: @group member
 @result 		return whether the content of input is valid
 */

- (BOOL)textView:(UITextView *)textView shouldChangeTextInRange:(NSRange)range replacementText:(NSString *)text;
```

Example of input area keyboard callback (valid for EaseIM APP).

``` java
//@GroupMember
- (BOOL)textView:(UITextView *)textView shouldChangeTextInRange:(NSRange)range replacementText:(NSString *)text
{
    //User can customize to implement
    if ([text isEqualToString:@"@"] && self.conversation.type == EMConversationTypeGroupChat)
    {
        [self _willInputAt:textView];
    }
    return YES;
}
```

#### Callback on the status of the other party's input

The other party is typing the status callback (valid for single chat)

``` java
/**
 The other party is typing
*/
- (void)beginTyping;
```

End of input callback for the other party (valid for single chat)

``` java
/**
 End input for the other party
*/
- (void)endTyping;
```

#### Message long press event callback

Default long press of message cell callback

``` java
/*!
 @method
 @brief 	Default message cell long press callback
 @param defaultLongPressItems 			Default long press extension function data model group (default total: copy, delete, withdraw send message less than 2 minutes from current time)
 @param message 			The current long press message
 @result 					return the default message long press extension function group
 */

- (NSMutableArray<EaseExtMenuModel*>*)messageLongPressExtMenuItemArray:(NSMutableArray<EaseExtMenuModel*>*)defaultLongPressItems message:(EMMessage*)message;
```

Example of default message cell long press callback (valid for EaseIM APP).

``` java
//Add a forwarding message
- (NSMutableArray<EaseExtMenuModel *> *)messageLongPressExtMenuItemArray:(NSMutableArray<EaseExtMenuModel *> *)defaultLongPressItems message:(EMMessage *)message
{
    NSMutableArray<EaseExtMenuModel *> *menuArray = [[NSMutableArray<EaseExtMenuModel *> alloc]initWithArray:defaultLongPressItems];
    //Forwarding
    __weak typeof(self) weakself = self;
    if (message.body.type == EMMessageBodyTypeText || message.body.type == EMMessageBodyTypeImage || message.body.type == EMMessageBodyTypeLocation || message.body.type == EMMessageBodyTypeVideo) {
        EaseExtMenuModel *forwardMenu = [[EaseExtMenuModel alloc]initWithData:[UIImage imageNamed:@"forward"] funcDesc:@"转发" handle:^(NSString * _Nonnull itemDesc, BOOL isExecuted) {
            //User customizable forwarding CallBack
            if (isExecuted) {
                [weakself forwardMenuItemAction:message];
            }
        }];
        [menuArray addObject:forwardMenu];
    }
    return menuArray;
}
```

#### custom cell long press callback

User-defined message cell long press event callback

``` java
/*!
 @method
 @brief 				The extension data model group of the custom cell currently being long pressed
 @param defaultLongPressItems 				Default long press extension function data model group Default total: copy, delete, withdraw (send message less than 2 minutes from current time))
 @param customCell 		The custom cell of the current long press
 @result 				Return the default message long press extension function group
 */
/**
 * 
 *
 * @param defaultLongPressItems 		Default Long Press Extensions data model group Default total: Copy, Delete, Withdraw (send message less than 2 minutes from current time))
 * @param customCell 					The current custom cell for long presses
 */
- (NSMutableArray<EaseExtMenuModel*>*)customCellLongPressExtMenuItemArray:(NSMutableArray<EaseExtMenuModel*>*) defaultLongPressItems customCell:(UITableViewCell*)customCell;
```

### conversation list custom function extension

Instantiate EaseConversationsViewController. After that, optionally implement the EaseConversationsViewControllerDelegate protocol (conversation list callback proxy) to receive
EaseConversationsViewController callbacks and make further customizations.

EaseConversationsViewControllerDelegate

#### custom cell

Get a custom message cell by implementing a conversation list callback.
if nil is returned, it will display the default; if cell is returned, the user-defined cell will be displayed.

``` java
/*!
 @method
 @brief 			Get message custom cell
 @discussion 		return nil to show default cell, otherwise show user-defined cell
 @param tableView 	The tableView of the current message view
 @param indexPath 	The indexPath of the current cell to be displayed
 @result 			return user-defined cell
 */
- (UITableViewCell *)easeTableView:(UITableView *)tableView cellForRowAtIndexPath:(NSIndexPath *)indexPath;
```

#### conversation list cell selection callback

``` java
/*!
 @method
 @brief 			conversation list cell selection callback
 @param tableView 	The tableView of the current message view
 @param indexPath 	The indexPath of the current cell to be displayed
 */
- (void)easeTableView:(UITableView *)tableView didSelectRowAtIndexPath:(NSIndexPath *)indexPath;
```

conversation list cell selection callback example (valid for EaseIM APP).

``` java
- (void)easeTableView:(UITableView *)tableView didSelectRowAtIndexPath:(NSIndexPath *)indexPath
{
    EaseConversationCell *cell = (EaseConversationCell*)[tableView cellForRowAtIndexPath:indexPath];
    //System Notification  
    if ([cell.model.easeId isEqualToString:@"emsystemnotificationid"]) {
       //This example is only for EaseIM APP to show system notification
        EMNotificationViewController *controller = [[EMNotificationViewController alloc] initWithStyle:UITableViewStylePlain];
        [self.navigationController pushViewController:controller animated:YES];
        return;
    }
    //Skip to chat page
    [[NSNotificationCenter defaultCenter] postNotificationName:CHAT_PUSHVIEWCONTROLLER object:cell.model];
}
```

#### conversation list user profile callback

``` java
/*!
 @method
 @brief c					coversation list user profile callback
 @discussion 				can return the corresponding user profile dataset based on conversationId or type
 @param conversationId 		The conversation id of the current conversation list cell
 @param type 				The conversation type of the current conversation list cell
 */
- (id<EaseUserDelegate>)easeUserDelegateAtConversationId:(NSString *)conversationId
                                        conversationType:(EMConversationType)type;
```

conversation list user profile callback example (valid for EaseIM APP)

``` objc
- (id<EaseUserDelegate>)easeUserDelegateAtConversationId:(NSString *)conversationId conversationType:(EMConversationType)type
{
    //EMConversationUserDataModel 			is a custom user profile data model that implements the EaseUserDelegate interface to return parameters
    //@property (nonatomic, copy, readonly) NSString *easeId; 		// Easemob id
    //@property (nonatomic, copy, readonly) UIImage *defaultAvatar; // default avatar display
    EMConversationUserDataModel *userData = [[EMConversationUserDataModel alloc]initWithEaseId:conversationId conversationType:type];
    return userData;
}
```

#### conversation list cell side slide item callback

``` java
/*!
 @method
 @brief 				conversation list cell side slider callback
 @param tableView 		The tableView of the current message view
 @param indexPath 		The indexPath of the current sideload cell
 @param actions 		Return a collection of side-slider items
 */
- (NSArray<UIContextualAction *> *)caseTableView:(UITableView *)tableView
           trailingSwipeActionsForRowAtIndexPath:(NSIndexPath *)indexPath
                                         actions:(NSArray<UIContextualAction *> *)actions;
````

conversation list cell side-slide item callback example (valid for EaseIM APP)

``` java
- (NSArray<UIContextualAction *> *)easeTableView:(UITableView *)tableView trailingSwipeActionsForRowAtIndexPath:(NSIndexPath *)indexPath actions:(NSArray<UIContextualAction *> *)actions
{
    NSMutableArray<UIContextualAction *> *array = [[NSMutableArray<UIContextualAction *> alloc]init];
    __weak typeof(self) weakself = self;
    UIContextualAction *deleteAction = [UIContextualAction contextualActionWithStyle:UIContextualActionStyleNormal
                                                                               title:@"delete"
                                                                             handler:^(UIContextualAction * _Nonnull action, __kindof UIView * _Nonnull sourceView, void (^ _Nonnull completionHandler)(BOOL))
    {
        //Delete operation
    }];
    deleteAction.backgroundColor = [UIColor redColor];
    [array addObject:deleteAction];
    //The returned actions which are ordered: Delete, Top
    [array addObject:actions[1]];
    return [array copy];
}
```

### Address Book Customization Extension

#### Get user's contact information

Get the user's own contact list to fill in the EaseIMKit address book

``` java
- (void)setContacts:(NSArray<EaseUserDelegate> * _Nonnull)contacts;
```

Instantiating the EaseContactsViewController. After that, you can optionally implement the EaseContactsViewControllerDelegate protocol (address book proxy) to receive
EaseContactsViewController callbacks and do further customization of the implementation.

EaseConversationsViewControllerDelegate

#### is about to refresh the address book to fill the data

``` java
- (void)willBeginRefresh;
```

Example of upcoming refresh address book filling the data (valid for EaseIM APP).

``` java
- (void)willBeginRefresh {
    //Get the contact list of the currently logged-in account from the server
    [EMClient.sharedClient.contactManager getContactsFromServerWithCompletion:^(NSArray *aList, EMError *aError) {
        if (!aError) {
            self->_contancts = [aList mutableCopy];
            NSMutableArray<EaseUserDelegate> *contacts = [NSMutableArray<EaseUserDelegate> array];
            for (NSString *username in aList) {
                EMContactModel *model = [[EMContactModel alloc] init];
                model.huanXinId = username;
                [contacts addObject:model];
            }
            //fill the contact list collection into the EaseIMKit address book instance
            [self->_contactsVC setContacts:contacts];
        }
        [self->_contactsVC endRefresh];
    }];
}
```

#### address book custom cell

``` java
/*!
@method
@brief 					Get address book custom cell
@discussion 			return nil to show default cell, otherwise show user-defined cell
@param tableView 		The tableView of the current message view
@param contact 			The data model of the current cell to be displayed
@result 				return user-defined cell
*/
- (UITableViewCell *)easeTableView:(UITableView *)tableView cellForRowAtContactModel:(EaseContactModel *) contact;
```

#### Address Book cell entry selection callback

``` java
/*!
@method
@brief 						Address Book cell item selection callback
@param tableView 			The tableView of the current message view
@param contact 				The data model owned by the currently selected cell
@result 					Returns a user custom cell
*/
- (void)easeTableView:(UITableView *)tableView didSelectRowAtContactModel:(EaseContactModel *) contact;
```

Example callback for selected address book cell entry.

``` java
- (void)easeTableView:(UITableView *)tableView didSelectRowAtContactModel:(EaseContactModel *)contact {
    //Jump to friend page
    if ([contact.easeId isEqualToString:@"newFriend"]) {
        EMInviteFriendViewController *controller = [[EMInviteFriendViewController alloc] init];
        [self.navigationController pushViewController:controller animated:YES];
        return;
    }
    //Jump to group list page
    if ([contact.easeId isEqualToString:@"groupList"]) {
        [[NSNotificationCenter defaultCenter] postNotificationName:GROUP_LIST_PUSHVIEWCONTROLLER object:@{NOTIF_NAVICONTROLLER:self.navigationController}];
        return;
    }
    //Jump to the chat room list page
    if ([contact.easeId isEqualToString:@"chatroomList"]) {
        [[NSNotificationCenter defaultCenter] postNotificationName:CHATROOM_LIST_PUSHVIEWCONTROLLER object:@{NOTIF_NAVICONTROLLER:self.navigationController}];
        return;
    }
    //Jump to friend profile page
    [self personData:contact.easeId];
}
```

#### address book cell side slide callback

``` java
/*!
 @method
 @brief 			conversation list cell side-slide item callback
 @param tableView 	The tableView of the current message view
 @param contact 	The contact data of the current sideloaded cell
 @param actions 	Return a collection of side-slider items
 */
- (NSArray<UIContextualAction *> *)caseTableView:(UITableView *)tableView
        trailingSwipeActionsForRowAtContactModel:(EaseContactModel *) contact
                                         actions:(NSArray<UIContextualAction *> * __nullable)actions;
```

Contacts cell side-swipe callback example.

``` java
- (NSArray<UIContextualAction *> *)easeTableView:(UITableView *)tableView trailingSwipeActionsForRowAtContactModel:(EaseContactModel *)contact actions:(NSArray<UIContextualAction *> *)actions
{
    //Disable side-swipe for non-contact list in Contacts header
    if ([contact.easeId isEqualToString:@"newFriend"] || [contact.easeId isEqualToString:@"groupList"] || [contact.easeId isEqualToString:@"chatroomList"]) {
        return nil;
    }
    __weak typeof(self) weakself = self;
    UIContextualAction *deleteAction = [UIContextualAction contextualActionWithStyle:UIContextualActionStyleDestructive
                                                                               title:@"delete"
                                                                             handler:^(UIContextualAction * _Nonnull action, __kindof UIView * _Nonnull sourceView, void (^ _Nonnull completionHandler)(BOOL))
    {
        //Delete contact operation
    }];
    return @[deleteAction];
}
```

------------------------------------------------------------------------

\<WRAP group> \<WRAP half column> Previous: [APNs
Content parsing](/im/ios/apns/content) \</WRAP

\<WRAP half column>
Next：[Private cloud SDK integration configuration](/im/ios/other/privatecloud) \</WRAP
\</WRAP



