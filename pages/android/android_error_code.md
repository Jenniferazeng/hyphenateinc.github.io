---
title: Android Error Code
keywords: android
sidebar: android_sidebar
toc: true
permalink: android_error_code.html
folder: android
---

In the Android SDK, the exception mechanism uses the error code to determine the specific error, and you can check the reason of the error according to the specific error code.

The class of error codes in Android is Error.

For example, if the user returns an existing error during registration, it can be detected as follows：

`Error.USER_ALREADY_EXISTS`

For details, see [Java Doc](https://hyphenateinc.github.io/android_reference/classio_1_1agora_1_1_error.html).



|  Error code  | Description    |
|  :----:      |  :----         |
|  0           |   EM_NO_ERROR  |
|  1           | GENERAL_ERROR
|  2           | NETWORK_ERROR
|  4           | EXCEED_SERVICE_LIMIT
|  5           | SERVICE_ARREARAGES
|  100      	  | INVALID_APP_KEY
|  101         | INVALID_USER_NAME
|  102         | INVALID_PASSWORD
|  103         | INVALID_URL
|  104         | INVALID_TOKEN
|  200         | USER_ALREADY_LOGIN
|  201         | USER_NOT_LOGIN
|  202      	  | USER_AUTHENTICATION_FAILED
|  203         | USER_ALREADY_EXIST
|  204         | USER_NOT_FOUND
|  205         | USER_ILLEGAL_ARGUMENT
|  206         | USER_LOGIN_ANOTHER_DEVICE
|  207         | USER_REMOVED
|  208         | USER_REG_FAILED
|  209         | USER_UPDATEINFO_FAILED
|  210      |USER_PERMISSION_DENIED
|  211      |USER_BINDDEVICETOKEN_FAILED
|  212      |USER_UNBIND_DEVICETOKEN_FAILED
|  213      |USER_BIND_ANOTHER_DEVICE
|  214      |USER_LOGIN_TOO_MANY_DEVICES
|  215      |USER_MUTED
|  216      |USER_KICKED_BY_CHANGE_PASSWORD
|  217      |USER_KICKED_BY_OTHER_DEVICE
|  300      |SERVER_NOT_REACHABLE
|  301      |SERVER_TIMEOUT
|  302      |SERVER_BUSY
|  303      |SERVER_UNKNOWN_ERROR
|  304      |SERVER_GET_DNSLIST_FAILED
|  305      |SERVER_SERVICE_RESTRICTED
|  400      |FILE_NOT_FOUND
|  401      |FILE_INVALID
|  402      |FILE_UPLOAD_FAILED
|  403      |FILE_DOWNLOAD_FAILED
|  404      |FILE_DELETE_FAILED
|  405      |FILE_TOO_LARGE
|  500      |MESSAGE_INVALID
|  501      |MESSAGE_INCLUDE_ILLEGAL_CONTENT
|  502      |MESSAGE_SEND_TRAFFIC_LIMIT
|  503      |MESSAGE_ENCRYPTION_ERROR
|  504      |MESSAGE_RECALL_TIME_LIMIT
|  505      |SERVICE_NOT_ENABLED
|  506      |MESSAGE_EXPIRED
|  507      |MESSAGE_ILLEGAL_WHITELIST
|  600      |GROUP_INVALID_ID
|  601      |GROUP_ALREADY_JOINED
|  602      |GROUP_NOT_JOINED
|  603      |GROUP_PERMISSION_DENIED
|  604      |GROUP_MEMBERS_FULL
|  605      |GROUP_NOT_EXIST
|  700      |CHATROOM_INVALID_ID
|  701      |CHATROOM_ALREADY_JOINED
|  702      |CHATROOM_NOT_JOINED
|  703      |CHATROOM_PERMISSION_DENIED
|  704      |CHATROOM_MEMBERS_FULL
|  705      |CHATROOM_NOT_EXIST
|  900      |PUSH_NOT_SUPPORT
|  901      |PUSH_BIND_FAILED
|  902      |PUSH_UNBIND_FAILED	          


------------------------------------------------------------------------