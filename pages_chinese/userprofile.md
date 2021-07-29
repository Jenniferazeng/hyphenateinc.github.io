# 用户属性



## 功能描述


用户属性指的是 app 用户的信息，包括昵称、头像、年龄、手机号等。

SDK 提供用户属性功能，可以保存包括昵称、头像 URL、邮箱、电话、性别、签名、生日等字段以及扩展字段，支持设置及查询用户属性。

<div class="alert note">用户属性为可选服务，如果不希望敏感信息存储在环信服务器，你可以自行维护。</div>



## 实现方法


设置和查询用户属性的主要步骤如下：

1. 调用 `userInfoManager` 获取用户属性模块；

2. 调用 `EMUuserInfoManage` 类的方法修改和查询用户属性信息。
   - 调用 `updateOwnInfo` 修改自己的用户信息；
   - 调用 `updateOwnInfoByAttribute` 修改自己用户信息中的某个属性；
   - 调用 `fetchUserInfoByUserId` 根据环信 ID 获取用户信息；
   - 调用 `fetchUserInfoByAttribute` 根据环信 ID 用户属性获取用户信息。

// TODO：不太理解 `fetchUserInfoByUserId`和 `fetchUserInfoByAttribute` 的区别，需要确认。


### 获取用户属性模块

示例代码如下：

// TODO：示例代码缺失，需要补充内容。

### 设置用户属性

你设置用户属性时，可以设置所有的属性，也可以只设置某一项属性。

#### 设置用户所有属性

示例代码如下：

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

#### 设置指定属性

示例代码如下（以修改用户头像为例）：

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


#### 获取用户所有属性

<div class="alert note">以下获取用户属性的接口中用户 ID 数组长度不能超过 100。</div>

示例代码如下：

```java
String[] userId = new String[1];
userId[0] = username;
EMClient.getInstance().userInfoManager().fetchUserInfoByUserId(userId, new EMValueCallBack<Map<String, EMUserInfo>>() {}
```

#### 获取用户指定某项或某几项属性

示例代码如下：

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

用户属性功能不提供文件存储服务，只存储头像文件的 URL 地址，不存储头像文件，因此你需要使用阿里云、腾讯云等第三方文件存储服务。

实现流程如下：

1. 将头像文件上传到第三方文件存储服务；
2. 将上传后得到的 URL 地址设置为用户属性的头像 URL 字段；
3. 显示头像时，通过调用 `fetchUserInfoByUserId` 或者 `fetchUserInfoByAttribute` 获取头像 URL ，然后在本地 UI 显示头像。

// TODO：不太理解 `fetchUserInfoByUserId`和 `fetchUserInfoByAttribute` 的区别，需要确认。



### 名片消息

实现方法：

你可以设置自定义消息的 `event` 为 `"userCard"` ，并在 `ext` 中添加展示名片所需要的环信 ID 、昵称和头像等字段。

发送名片消息的示例代码如下：

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

可参考[示例项目](https://www.easemob.com/download/im) 中的以下类：
- ChatUserCardAdapterDelegate
- ChatUserCardAdapterDelegate
- ChatRowUserCard

