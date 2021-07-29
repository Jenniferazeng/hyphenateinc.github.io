# 用户属性


## 功能描述

用户属性指的是用户的信息，包括：昵称、头像、年龄、手机号等。 用户属性为可选服务，如果不希望敏感信息存储在环信服务器，用户可以自行维护。

使用用户属性功能可以获得以下能力：

- 标准的用户属性的读写能力，包括：昵称、头像url、邮箱、电话、性别、签名、生日；
- 自定义用户属性的读写能力，提供自定义扩展，可以通过JSON字符串添加多个属性。


## 实现方法

SDK提供了用户属性功能，可供用保存头像地址、昵称、电话等字段以及扩展字段。

用户属性主要操作：

1. 调用 `userInfoManager` 获取用户属性模块；
2. 调用 `updateOwnInfo` 修改自己的用户信息；
3. 调用 `updateOwnInfoByAttribute` 修改自己用户信息中的某个属性；
4. 调用 `fetchUserInfoByUserId` 根据环信 ID 获取用户信息；
5. 调用 `fetchUserInfoByAttribute` 根据环信 ID 用户属性获取用户信息；

示例代码

### 设置用户属性

用户可以设置自己的用户属性,设置用户属性时,可以设置所有的属性，也可以只设置某一项属性。

- 设置用户所有属性

调用实例过程如下：

```java
EMUserInfo userInfo = new EMUserInfo();
userInfo.setUserId(EMClient.getInstance().getCurrentUser());
userInfo.setNickName("jian");
userInfo.setAvatarUrl("url");
userInfo.setBirth("2000.10.10");
userInfo.setSignature("坚持就是胜利");
userInfo.setPhoneNumber("1343444");
userInfo.setEmail("9892029@qq.com");
userInfo.setGender(1);
EMClient.getInstance().userInfoManager().updateOwnInfo(userInfo, new EMValueCallBack<String>() {
            @Override
            public void onSuccess(String value) {     
            }
            @Override
            public void onError(int error, String errorMsg) {
            }
  });
```

- 设置某个或者某几个属性

调用实例如下 （修改用户头像为例）：

```java
String url = "https://download-sdk.oss-cn-beijing.aliyuncs.com/downloads/IMDemo/avatar/Image1.png";
 EMClient.getInstance().userInfoManager().updateOwnInfoByAttribute(EMUserInfoType.AVATAR_URL, url, new EMValueCallBack<String>() {
              @Override
              public void onSuccess(String value) {
                        
              }

              @Override
              public void onError(int error, String errorMsg) {    
              }
   });
```



### 获取用户属性

获取用户属性时，可以获取用户所有的属性，也可以获取某一项或其中某几项，如只获取昵称或头像。

- 获取用户所有属性

<div class="alert note">以下获取用户属性的接口中用户ID数组长度不能超过100。</div>


调用实例如下：

```java
String[] userId = new String[1];
userId[0] = username;
EMClient.getInstance().userInfoManager().fetchUserInfoByUserId(userId, new EMValueCallBack<Map<String, EMUserInfo>>() {}
```

- 获取用户指定某项或某几项属性

调用实例如下：

```java
String[] userId = new String[1];
userId[0] = EMClient.getInstance().getCurrentUser();
EMUserInfoType[] userInfoTypes = new EMUserInfoType[2];
userInfoTypes[0] = EMUserInfoType.NICKNAME;
userInfoTypes[1] = EMUserInfoType.AVATAR_URL;
EMClient.getInstance().userInfoManager().fetchUserInfoByAttribute(userId, userInfoTypes,
           new EMValueCallBack<Map<String, EMUserInfo>>() {}
```



## 开发注意事项

### 用户头像管理

用户属性功能不提供文件存储服务，只存储头像文件的 URL 地址，不存储头像文件，因此用户需要使用阿里云、腾讯云等第三方文件存储服务。

实现方法：

1. 将头像文件上传到第三方文件存储服务；
2. 将上传后得到的 URL 地址设置为用户属性的头像 URL 字段；
3. 显示头像时，通过调用 SDK 获取用户属性方法，获取到头像 URL ，然后在本地 UI 显示头像。



### 名片消息

SDK 没有名片类型的消息，但是可以通过自定义消息类型实现名片功能。

实现方法：

设置自定义消息的 `event` 为 `“userCard”` ，并在 `ext` 中添加展示名片所需要的环信 ID 、昵称和头像等字段。

名片消息发送过程如下：

```java
EMMessage message = EMMessage.createSendMessage(EMMessage.Type.CUSTOM);
                EMCustomMessageBody body = new EMCustomMessageBody(DemoConstant.USER_CARD_EVENT);
                Map<String,String> params = new HashMap<>();
                params.put(DemoConstant.USER_CARD_ID,userId);
                params.put(DemoConstant.USER_CARD_NICK,user.getNickname());
                params.put(DemoConstant.USER_CARD_AVATAR,user.getAvatar());
                body.setParams(params);
                message.setBody(body);
                message.setTo(toUser);
 EMClient.getInstance().chatManager().sendMessage(message);
```

如果需要在名片中展示更丰富的信息，可以在 `ext` 中增加更多字段。

名片的具体实现可参考[示例项目](https://www.easemob.com/download/im) 中的ChatUserCardAdapterDelegate ChatUserCardViewHolder chatRowUserCard等类。