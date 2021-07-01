---
title:  Error Code
keywords: ios
sidebar: ios_sidebar
toc: true
permalink: ios_error_code.html
folder: ios
---

## Common error codes of iOS API

- - -
The header file AgoraErrorCode.h in the SDK. Please determine the error based on its type, not the number.

The class of error codes in iOS is AgoraError.

For example, if the user returns an existing error during registration, it can be detected as follows: error.code

For details, see [iOS error Doc](https://hyphenateinc.github.io/ios_reference/_agora_error_code_8h_source.html).

- - -

| Definition | Description |
| :--------- | :---------- |
| AgoraErrorGeneral | Error |
| AgoraErrorNerworkUnavailable | Network Unavailable |
| AgoraErrorNotExist | Not exist |
| AgoraErrorInvalidAppkey | Invalid AppKey |
| AgoraErrorInvalidUsername | Username is invalid |
| AgoraErrorInvalidPassword | Password is invalid |
| AgoraErrorInvalidURL | URL is invalid |
| AgoraErrorUserAlreadyLogin | User logged in |
| AgoraErrorUserNotLogin | User is not logged in |
| AgoraErrorUserAuthenticationFailed | Failed to get token |
| AgoraErrorUserAlreadyExist | User already exists |
| AgoraErrorUserNotFound | User does not exist |
| AgoraErrorUserIllegalArgument | Invalid argument |
| AgoraErrorUserLoginOnAnotherDevice | The current user name is logged in on another device |
| AgoraErrorUserRemoved | The current user name is deleted on the server side |
| AgoraErrorUserRegisterFailed | User registration failed |
| AgoraErrorServerNotLogin | User has not login the server |
| AgoraErrorServerNotReachable | Server not connected |
| AgoraErrorServerTimeout | Connection to server timed out |
| AgoraErrorServerBusy | Server is busy |
| AgoraErrorServerUnknownError | Unknown server error |
| AgoraErrorFileNotFound | File not found |
| AgoraErrorFileInvalid | Invalid file |
| AgoraErrorFileUploadFailed | Failed to upload file |
| AgoraErrorFileDownloadFailed | Failed to download file |
| AgoraErrorMessageInvalid = 500 | Invalid message |
| AgoraErrorMessageIncludeIllegalSpeech | Message content contains sensitive information |
| AgoraErrorGroupInvalidId = 600 | Invalid group ID |
| AgoraErrorGroupAlreadyJoined | Joined the group |
| AgoraErrorGroupNotJoined | Not joined the group |
| AgoraErrorGroupPermissionDenied | No permission to execute this operation |
| AgoraErrorGroupMembersFull | The number of group members has reached the maximum |
| AgoraErrorChatroomInvalidId | Invalid chat room ID |
| AgoraErrorChatroomAlreadyJoined | Joined the chat room |
| AgoraErrorChatroomNotJoined | Not joined the chat room |
| AgoraErrorChatroomPermissionDenied | No permission to execute this operation |
| AgoraErrorChatroomMembersFull | The number of chat room members reaches the maximum |
| AgoraErrorCallInvalidId | Invalid live call ID |
| AgoraErrorCallBusy | Already in a real-time call |
| AgoraErrorCallRemoteOffline | The other party is not online |
| AgoraErrorCallConnectFailed | failed to establish connection for real-time call |
| AgoraErrorApnsBindDeviceTokenFailed | Failed to register device token |

- - -