---
title: ios Error Code
keywords: ios
sidebar: ios_sidebar
toc: true
permalink: ios_error_code.html
folder: ios
---
# iOS SDK's Introduction and import

------------------------------------------------------------------------

## DEMO（EaseIM App） experience

Download link：[download page](http://www.easemob.com/download/im)

The class of error codes in iOS is EMError.

For example, if the user returns an existing error during registration, it can be detected as follows: error.code

 Definition                                 Description
------------------------------------ -------------------------------
	EMErrorGeneral                       Error
	EMErrorNerworkUnavailable            Network Unavailable
	EMErrorNotExist                      Not exist
	EMErrorInvalidAppkey                 Invalid AppKey
	EMErrorInvalidUsername               Username is invalid
	EMErrorInvalidPassword               Password is invalid
	EMErrorInvalidURL                    URL is invalid
	EMErrorUserAlreadyLogin              User logged in
	EMErrorUserNotLogin                  User is not logged in
	EMErrorUserAuthenticationFailed      Failed to get token
	EMErrorUserAlreadyExist              User already exists
	EMErrorUserNotFound                  User does not exist
	EMErrorUserIllegalArgument           Invalid argument
	EMErrorUserLoginOnAnotherDevice      The current user name is logged in on another device
	EMErrorUserRemoved                   The current user name is deleted on the server side
	EMErrorUserRegisterFailed            User registration failed
	EMErrorServerNotLogin                User has not login the server
	EMErrorServerNotReachable            Server not connected
	EMErrorServerTimeout                 Connection to server timed out
	EMErrorServerBusy                    Server is busy
	EMErrorServerUnknownError            Unknown server error
	EMErrorFileNotFound                  File not found
	EMErrorFileInvalid                   Invalid file
	EMErrorFileUploadFailed              Failed to upload file
	EMErrorFileDownloadFailed            Failed to download file
	EMErrorMessageInvalid = 500          Invalid message
	EMErrorMessageIncludeIllegalSpeech   Message content contains sensitive information
	EMErrorGroupInvalidId = 600          Invalid group ID
	EMErrorGroupAlreadyJoined            Joined the group
	EMErrorGroupNotJoined                Not joined the group
	EMErrorGroupPermissionDenied         No permission to execute this operation
	EMErrorGroupMembersFull              The number of group members has reached the maximum
	EMErrorChatroomInvalidId             Invalid chat room ID
	EMErrorChatroomAlreadyJoined         Joined the chat room
	EMErrorChatroomNotJoined             Not joined the chat room
	EMErrorChatroomPermissionDenied      No permission to execute this operation
	EMErrorChatroomMembersFull           The number of chat room members reaches the maximum
	EMErrorCallInvalidId                 Invalid live call ID
	EMErrorCallBusy                      Already in a real-time call
	EMErrorCallRemoteOffline            The other party is not online
	EMErrorCallConnectFailed             failed to establish connection for real-time call
	EMErrorApnsBindDeviceTokenFailed     Failed to register device token

------------------------------------------------------------------------


