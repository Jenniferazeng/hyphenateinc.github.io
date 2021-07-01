---
title: Connect To Chat Server
keywords: android
sidebar: android_sidebar
toc: true
permalink: android_connect_to_chat_server.html
folder: android
---

## Introduction to asynchronous/synchronous methods in SDK

-   Synchronization method: most of the methods in the SDK are synchronous methods. After this method is executed, the following code will be executed.
-   Asynchronous method: Methods where callbacks and API comments state that they are asynchronous methods, you don't need to wait for the method to finish, the code behind it is already executing, and you get the result of the method execution through the callback.

## Initialize the SDK

**Required the initialization in the application's `oncreate` method**，Need to pass in the configured options when initializing.

``` java
ChatOptions options = new ChatOptions();
// Set your appkey
options.setAppKey("Your appkey");

// By default, verification is not required when adding friends. Change to need verification
options.setAcceptInvitationAlways(false);
// Whether automatically upload message attachments to the chat server. The default value is True, which means to upload and download using the chat server. If it is set to false, the developer needs to handle the upload and download of the attachments.
options.setAutoTransferMessageAttachments(true);
// Whether to automatically download thumbnails of attachment messages, etc. The default is true. This part is similar to the above parameter.
options.setAutoDownloadThumbnail(true);
...
//initialization
ChatClient.getInstance().init(applicationContext, options);
//Turn off the debug mode when doing packaging obfuscation to avoid consuming unnecessary resources.
ChatClient.getInstance().setDebugMode(true);
```

**Note：** If a third-party service is started in your APP, please Add the following code front of the initialization SDK（`ChatClient.getInstance().init(applicationContext, options)`） method (the corresponding code can refer to the application of Demo). 

``` java
appContext = this;
int pid = android.os.Process.myPid();
String processAppName = getAppName(pid);
// If the remote service is enabled in the APP, application:onCreate will be called twice
// In order to prevent chat SDK from being initialized twice, adding this judgment will ensure that the SDK is initialized once
// The default APP will run under the default process name with the package name. If the process name found is not the process name of the APP, it will return immediately.

if (processAppName == null ||!processAppName.equalsIgnoreCase(appContext.getPackageName())) {
    Log.e(TAG, "enter the service process!");
    
    // This application::onCreate is called by the service and returns directly
    return;
}
```

For how to get `processAppName`, please refer to the following method.

``` java
private String getAppName(int pID) {
    String processName = null;
    ActivityManager am = (ActivityManager) this.getSystemService(ACTIVITY_SERVICE);
    List l = am.getRunningAppProcesses();
    Iterator i = l.iterator();
    PackageManager pm = this.getPackageManager();
    while (i.hasNext()) {
        ActivityManager.RunningAppProcessInfo info = (ActivityManager.RunningAppProcessInfo) (i.next());
        try {
            if (info.pid == pID) {
                processName = info.processName;
                return processName;
            }
        } catch (Exception e) {
            // Log.d("Process", "Error>> :"+ e.toString());
        }
    }
    return processName;
}
```

## Register

There are two registration modes, open registration and authorized registration. The client can register only when the it is open registration .

-   The open registration is for test . It is not recommended to use this method to register a chat account in a formal environment.
-   The authorization registration process should be that the server registers through the Platform API provided by Agora, and then saves it to your server or returns it to the client.

Registered user names will be automatically converted to lowercase letters. It is recommended that all user names are registered in lowercase. (It is strongly recommended that developers call the Platform API in the backend to register the chat user ID. The client registration method is not recommended.)

``` java
//If registration fails, ChatException will be thrown.
ChatClient.getInstance().createAccount(username, pwd);//Synchronization method
```

## Login

**note：**

* Except for registering for monitoring, all other SDK operations can only be executed after logging in.
      
* After successful login, you need to call `ChatClient.getInstance().chatManager().loadAllConversations();` and `ChatClient.getInstance().groupManager().loadAllGroups();`。
      
* The above two methods are to ensure that the local conversation and group are loaded after entering the main page.
      
* If you have logged in before, when you enters the APP which have been stayed in the background for a long time, the groups and conversations loaded into the memory may be null. You can also add these two lines of code in oncreate on the main page. Of course, a better way is to put it on the opening page of the program, you can refer to the SplashActivity of Demo.

``` java
ChatClient.getInstance().login(userName,password,new CallBack() {
    @Override
    public void onSuccess() {
        ChatClient.getInstance().groupManager().loadAllGroups();
        ChatClient.getInstance().chatManager().loadAllConversations();
        Log.d("main", "Login to the chat server successfully!");        
    }

    @Override
    public void onProgress(int progress, String status) {

    }

    @Override
    public void onError(int code, String message) {
        Log.d("main", "Failed to log in to the chat server!");
    }
});
```

## Automatic login

After the first login is successful, you do not need to call the login method again. The SDK will automatically log in for you the next time the APP starts. If your automatic login fails, you can also read the previous conversation information (the above situation will happen when you do not call logout).

The automatic login property in the SDK is enable by default. If automatic login is not required, call `options.setAutoLogin(false);` and set it to false to turn off when the SDK is initializing.

Automatic login will be cancelled in the following situations:

-   The user invoked the logout action of the SDK;
-   The user changes the password on another device, which causes failure of the automatic login on this device;
-   The user's account is deleted from the server;
-   The user logs in from another device to kick out the user logged in on the current device.

## Logout

Synchronization method

``` java
ChatClient.getInstance().logout(true);
```

Asynchronous method

``` java
ChatClient.getInstance().logout(true, new CallBack() {
            
    @Override
    public void onSuccess() {
        // TODO Auto-generated method stub
        
    }
    
    @Override
    public void onProgress(int progress, String status) {
        // TODO Auto-generated method stub
        
    }
    
    @Override
    public void onError(int code, String message) {
        // TODO Auto-generated method stub
        
    }
});
```

If third-party push such as FCM is integrated, the first argument in the method needs to be set to `true`. This will unbind the device token when exiting. Otherwise, It may happen that you quit and can still receive messages. \
Sometimes, you may encounter network problems leading to unbinding fails. When the app is processing, a prompt box will pop up to let the user choose whether to continue to exit (the pop-up box prompts the risk of keeping receiving a message when you continue to exit). If the user chooses to continue to exit, pass `false` and then call the logout method to exit successfully;\
You can also return to exit successfully after failure, and then start a thread in the background to continuously call the logout method until it succeeds. There is a risk: the user kills the app, and then the network is restored, the user will continue to receive messages.

Another note is that if you call the asynchronous exit method, the chat-related method such as login will be called after the onsucess callback is received.

## Reminders after users are banned

Users can be managed in [Chat Management Console](http://console.easemob.com), for example, users can be banned in the console.
After the user is banned, it will prompt the SDK to log in and return to SERVER_SERVING_DISABLED (305),
The corresponding prompt or processing can be carried out by user depending on the return value.

It should be noted that the above error code will also be returned when the app is banned entirely. Because apps are not banned generally, they can be used to notify the user to be banned.

## Registered connection status listening

The Android SDK will automatically reconnect when the connection is lost. You can know the connection status by registering the connection listener without any operations.

-   It is inevitable that you will encounter network problems during the chat. This SDK provide you with a network linstening interface to linstener in real time.

-   The reason can be determined according to the error returned by disconnect. If the parameter value returned by the server is `Error.USER_LOGIN_ANOTHER_DEVICE`, it is considered that the same account is logged in remotely; if the parameter value returned by the server is `Error.USER_REMOVED`, the account has been deleted in the background.

-   After an error such as being kicked offline or banned, the SDK will not try to reconnect. After that, you need to call `logout` to log out and then you can log in again.

``` java
//Register a listener to monitor the connection status
ChatClient.getInstance().addConnectionListener(new MyConnectionListener());

//Implement the ConnectionListener interface
private class MyConnectionListener implements ConnectionListener {
    @Override
    public void onConnected() {
    }
    @Override
        public void onDisconnected(int error) {
            EMLog.d("global listener", "onDisconnect" + error);
            if (error == Error.USER_REMOVED) {
                onUserException(Constant.ACCOUNT_REMOVED);
            } else if (error == Error.USER_LOGIN_ANOTHER_DEVICE) {
                onUserException(Constant.ACCOUNT_CONFLICT);
            } else if (error == Error.SERVER_SERVICE_RESTRICTED) {
                onUserException(Constant.ACCOUNT_FORBIDDEN);
            } else if (error == Error.USER_KICKED_BY_CHANGE_PASSWORD) {
                onUserException(Constant.ACCOUNT_KICKED_BY_CHANGE_PASSWORD);
            } else if (error == Error.USER_KICKED_BY_OTHER_DEVICE) {
                onUserException(Constant.ACCOUNT_KICKED_BY_OTHER_DEVICE);
            }
        }
}


 /**
     * user met some exception: conflict, removed or forbidden， goto login activity
     */
    protected void onUserException(String exception){
        EMLog.e(TAG, "onUserException: " + exception);
        Intent intent = new Intent(appContext, MainActivity.class);
        intent.addFlags(Intent.FLAG_ACTIVITY_NEW_TASK);
        intent.addFlags(Intent.FLAG_ACTIVITY_RESET_TASK_IF_NEEDED);
        intent.putExtra(exception, true);
        appContext.startActivity(intent);

        showToast(exception);
    }
```

------------------------------------------------------------------------
