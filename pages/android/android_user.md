---
title: Android User
keywords: android
sidebar: android_sidebar
toc: true
permalink: android_user.html
folder: android
---
- # User attributes

  ## product description

  User attributes refer to user's information, including: nickname, avatar, age, mobile phone number, etc.
  User attributes are optional services. If users do not want sensitive information to be stored in the Agora server, they can maintain it by themselves.

  Use the user attribute function to get the following capabilities:

  - Reading and writing ability of standard user attributes, including: nickname, avatar url, email address, phone number, gender, signature, birthday;
  - the read and write capabilities of custom user attributes, provide custom extensions, and add multiple attributes through JSON strings.

## Integration overview

The SDK provides a user attribute function, which can be used to save fields such as avatar address, nickname, phone number, and extension fields. The related interfaces are introduced as follows:

The user attribute module is getted as follows:

     /**
         * \~chinese
         * Get UserInfoManager
         *
         * @return UserInfoManager
         *
         * \~english
         * get userInfo manager
         *
         * @return UserInfoManager
         */
        public UserInfoManager userInfoManager() {}

UserInfo is an encapsulation of all fields of user attributes. The fields are described as follows:

    * \~chinese
     * User attribute entity class, information about the user, the following attributes:
     * userId: Agora ID
     * nickName: Nickname (recommended not to exceed 64 bytes)
     * avatarUrl: Avatar URL (recommended not to exceed 256 bytes)
     * email: Email (recommended not to exceed 64 bytes)
     * phoneNumber: phone number (recommended not to exceed 32 bytes)
     * gender: gender (default is 0, 1 means male, 2 means female, others are invalid)
     * signature: signature (recommended not to exceed 256 bytes)
     * birthday: birthday (recommended not to exceed 64 bytes)
     * ext: extended field (users can extend it by themselves, it is recommended to encapsulate it into a JSON string, or it can be set to an null string)
     *
     * \~english
     * User attribute entity class, information about the user,
     * the following attributes:
     * nickName; avatarUrl; email; phoneNumber; isMale;
     * signature; birthday; userId;ext;
     *
     */
    public class UserInfo {
        private String nickName;
        private String avatarUrl;
        private String email;
        private String phoneNumber;
        private int gender = 0;
        private String signature;
        private String birth;
        private String userId;
        private String ext;
        public UserInfo(){}
     }

## Set user attributes

Users can set their own user attributes. When setting user attributes, they can set all attributes or only one attribute

-Set all attributes of the user

The interface for setting all attributes of the user is as follows

      /**
         * \~chinese
         * Modify your own user attributes
         *
         * @param userInfo user attributes to be modified
         * @param callBack result callback
         *
         * \~english
         * Update own userInfo.
         *
         * @param callBack result callback
         */
        public void updateOwnInfo(final UserInfo userInfo, final ValueCallBack<String> callBack) {}

The calling process is as follows:

    UserInfo userInfo = new UserInfo();
    userInfo.setUserId(ChatClient.getInstance().getCurrentUser());
    userInfo.setNickName("jian");
    userInfo.setAvatarUrl("url");
    userInfo.setBirth("2000.10.10");
    userInfo.setSignature("Persistence is victory");
    userInfo.setPhoneNumber("1343444");
    userInfo.setEmail("9892029@qq.com");
    userInfo.setGender(1);
    ChatClient.getInstance().userInfoManager().updateOwnInfo(userInfo, new ValueCallBack<String>() {
                @Override
                public void onSuccess(String value) {
                }
                @Override
                public void onError(int error, String errorMsg) {
                }
      });

-Set one or several attributes

The user attribute types that users can specify include:

    /**
          * Related user attributes The attribute types are defined as follows:
          * NICKNAME
          * AVATAR_URL Avatar
          * EMAIL mailbox
          * PHONE phone
          * GENDER gender
          * SIGN signature
          * BIRTH birthday
          * EXT extension field
          */
         public enum UserInfoType {
             NICKNAME(0,"nickname"),
             AVATAR_URL(1,"avatarurl"),
             EMAIL(2,"mail"),
             PHONE(3,"phone"),
             GENDER(4,"gender"),
             SIGN(5,"sign"),
             BIRTH(6,"birth"),
             EXT(100,"ext");
       }

The interface for setting the attributes of a specified user is as follows:

     /**
         * \~chinese
         * Modify an attribute in user information
         *
         * @param attribute user attribute field
         * @param value modified information (when the value is null, the set attribute will be deleted)
         * @param callBack result callback
         *
         * \~english
         * Update own userInfo.
         *
         * @param callBack result callback
         */
        public void updateOwnInfoByAttribute(final UserInfoType attribute, final String value, final ValueCallBack<String> callBack){}

The examples are as follows (an example to modify the user profile picture):

     String url = "https://download-sdk.oss-cn-beijing.aliyuncs.com/downloads/IMDemo/avatar/Image1.png";
     ChatClient.getInstance().userInfoManager().updateOwnInfoByAttribute(UserInfoType.AVATAR_URL, url, new ValueCallBack<String>() {
                  @Override
                  public void onSuccess(String value) {
                            
                  }
    
                  @Override
                  public void onError(int error, String errorMsg) {
                  }
       });

## Get user attributes

When getting user attributes, you can get all the attributes of the user, or you can get a specific item or some of them. If you only get a nickname or avatar:

-Get all attributes of the user

The interface for getting all attributes of the user is as follows:

     /**
          * \~chinese
          * get user information according to Agora ID
          *
          * @param userIds   list of user IDs
          * @param callBack   result callback
          *
          * \~english
          * Update own userInfo.
          *
          * @param callBack result callback
          */
         public void fetchUserInfoByUserId(final String[] userIds, final ValueCallBack<Map<String,UserInfo>> callBack) {}

The call is as follows

      String[] userId = new String[1];
     userId[0] = username;
     ChatClient.getInstance().userInfoManager().fetchUserInfoByUserId(userId, new ValueCallBack<Map<String, UserInfo>>() {}

-get a certain item or properties specified by the user

The interface for geting the attributes of a specified user is as follows:

      /**
          * \~chinese
          * get user information according to Agora ID user attributes
          *
          * @param userIds list of user IDs
          * @param attributes user attributes
          * @param callBack result callback
          *
          * \~english
          * Update own userInfo.
          *
          * @param callBack result callback
          */
         public void fetchUserInfoByAttribute(final String[] userIds, final UserInfoType[] attributes, ValueCallBack<Map<String,UserInfo>> callBack){}

The call is as follows:

     String[] userId = new String[1];
    userId[0] = ChatClient.getInstance().getCurrentUser();
    UserInfoType[] userInfoTypes = new UserInfoType[2];
    userInfoTypes[0] = UserInfoType.NICKNAME;
    userInfoTypes[1] = UserInfoType.AVATAR_URL;
    ChatClient.getInstance().userInfoManager().fetchUserInfoByAttribute(userId, userInfoTypes,
               new ValueCallBack<Map<String, UserInfo>>() {}

## User avatar management

The user attribute function does not provide file storage service and avatar file storage service, only the url address of the avatar file is stored. Therefore, users need to use third-party file storage services such as Alibaba Cloud and Tencent Cloud. When a user sets an avatar, he needs to upload the avatar file to a third-party file storage service first, and then set the url address getted after uploading as the avatar url field of the user attribute. When the avatar is displayed, the avatar url is getted by calling the SDK method to get user attributes, and then the avatar is displayed on the local UI.

## Card Message

The SDK does not have a card type of message, but the card function can be realized by customizing the message type. You can set the event of the custom message to \"userCard\", and add fields such as the Agora id, nickname and avatar needed to display the business card in the ext to realize the function of sending and receiving business cards.

The business card message sending process is as follows:

      ChatMessage message = ChatMessage.createSendMessage(ChatMessage.Type.CUSTOM);
                    CustomMessageBody body = new CustomMessageBody(DemoConstant.USER_CARD_EVENT);
                    Map<String,String> params = new HashMap<>();
                    params.put(DemoConstant.USER_CARD_ID,userId);
                    params.put(DemoConstant.USER_CARD_NICK,user.getNickname());
                    params.put(DemoConstant.USER_CARD_AVATAR,user.getAvatar());
                    body.setParams(params);
                    message.setBody(body);
                    message.setTo(toUser);
     ChatClient.getInstance().chatManager().sendMessage(message);

If you need to display more information in the card, you can add more fields in the ext.

For the specific implementation of business cards, please refer to ChatUserCardAdapterDelegate 
ChatUserCardViewHolder chatRowUserCard and other classes in Demo.


------------------------------------------------------------------------
