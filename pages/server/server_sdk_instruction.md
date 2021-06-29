---
title: SDK Instruction
keywords: server
sidebar: server_sidebar
toc: true
permalink: server_sdk_instruction.html
folder: server
---

## Server SDK

### Introduction

Server SDK is a encapsulation for Easemob IM [server-side API](http://docs-im.easemob.com/im/server/ready/intro)
This is to save time for server-side developers to connect to the Easemob API, and only need to configure their own appkey related information to use it.

### Functionality

Server SDK provides the management operation of users, messages, groups, chat rooms and other resources.

### Dependencies

- Java 1.8
- [Reactor](https://projectreactor.io)(io.projectreactor:reactor-bom:2020.0.4)

### Installation

If your project is built with Maven and spring boot is 2.4.3 or above, you can add the following code to pom.xml.

    <dependency>
        <groupId>com.easemob.im</groupId>
        <artifactId>im-sdk-core</artifactId>
        <version>0.2.5</version>
    </dependency>.

Example.

![](/im/server/ready/springboot2.4.3版本以上.jpg){width="400"}

If you are using spring-boot version 2.4.3 or below, you need to add :

        <dependencyManagement>
            <dependencies>
                <dependency>
                    <groupId>io.netty</groupId>
                    <artifactId>netty-bom</artifactId>
                    <version>4.1.59.Final</version>
                    <type>pom</type>
                    <scope>import</scope>
                </dependency>
                <dependency>
                    <groupId>io.projectreactor</groupId>
                    <artifactId>reactor-bom</artifactId>
                    <version>2020.0.4</version>
                    <type>pom</type>
                    <scope>import</scope>
                </dependency>
            </dependencies>
        </dependencyManagement>

示例：

![](/im/server/ready/依赖-1.jpg){width="400"}

If your project is built with Gradle, you can add the following code to build.grade.

    implementation 'com.easemob.im:im-sdk-core:0.2.5'

### Preparation

Before using Server SDK, you need to prepare Easemob appkey, Client ID, ClientSecre.

If you have a Easemob Management Background account and have created an application, please login to Easemob Management Background first, click [here](https://console.easemob.com/user/login), then go to \"application List\"
-> Click \"View\" to get the appkey, Client ID, ClientSecret.

If you don't have a Easemob management background account, please register your account first, click [here](https://console.easemob.com/user/register), after successful registration, please login, then click \"application request\", after successful addition, click \"View\" to get the appkey, Client ID, ClientSecret.

### Use

[EMService](https://easemob.github.io/easemob-im-server-sdk/com/easemob/im/server/EMService.html) is the entrance of all APIs, which can be initialized like this.

    EMProperties properties = EMProperties.builder()
            .setAppkey(cliProperties.getAppkey())
            .setClientId(cliProperties.getClientId())
            .setClientSecret(cliProperties.getClientSecret())
            .build();
    
    EMService service = new EMService(properties);

For details, please refer to [im-sdk-cli](https://github.com/easemob/easemob-im-server-sdk/tree/master/im-sdk-cli/src/main/java/com/easemob/im/cli) IMCliConfiguration in

Based on business resources, the APIs are classified as.

- [Attachment](https://easemob.github.io/easemob-im-server-sdk/com/easemob/im/server/api/attachment/AttachmentApi.html)
    For uploading and downloading attachments
- [Block](https://easemob.github.io/easemob-im-server-sdk/com/easemob/im/server/api/block/BlockApi.html)
    Used to restrict access
- [Contact](https://easemob.github.io/easemob-im-server-sdk/com/easemob/im/server/api/contact/ContactApi.html)
    Used to manage contacts
- [Group](https://easemob.github.io/easemob-im-server-sdk/com/easemob/im/server/api/group/GroupApi.html)
    Used to manage groups
- [Message](https://easemob.github.io/easemob-im-server-sdk/com/easemob/im/server/api/message/MessageApi.html)
    Used to send messages
- [User](https://easemob.github.io/easemob-im-server-sdk/com/easemob/im/server/api/user/UserApi.html)
    Used to manage users
- [Room](https://easemob.github.io/easemob-im-server-sdk/com/easemob/im/server/api/room/RoomApi.html)
    Used to manage chat rooms

Each business resource corresponds to a method, for example, the user-related API, which can be found in .user().

As an example, if we want to register a user, we can code like this

    EMService service;
    service.user().create(username, password).block();

The return value of the API is responsive. If you want to block, you can use block() in the above example.

### Demo

You can refer to [im-sdk-cli](https://github.com/easemob/easemob-im-server-sdk/tree/master/im-sdk-cli), which is a CLI program built using this SDK.

### Reference

The SDK api documentation is available [here](https://easemob.github.io/easemob-im-server-sdk/)

Server
The SDK open source address and document is available [here](https://github.com/easemob/easemob-im-server-sdk).

------------------------------------------------------------------------

### Easemob IM CLI

**Easemob IM CLI** is a console project based on the **Easemob IM Java SDK**, designed to provide developers with a convenient and fast command line interface to call the server-side API. It is also an example of how to use **Easemob IM Java SDK**

#### Installation

**1. Automatic installation**

(1) Execute the installation script (executed in the terminal)

    sh -c "$(curl -fsSL https://raw.githubusercontent.com/easemob/easemob-im-server-sdk/master/im-sdk-cli/install.sh)"

Or.

    sh -c "$(wget https://raw.githubusercontent.com/easemob/easemob-im-server-sdk/master/im-sdk-cli/install.sh -O -)"

\(2\) Add environment variables 	add the following command to `~/.zshrc` or  `~/.bashrc`(depending on the SHELL you are using).

    alias im='java -jar ~/.easemob/im-sdk-cli.jar'

\(3\) Refresh the environment variables

    source ~/.zshrc or ~/.bashrc

This will install the program to `~/.easemob/` and add the relevant commands to the system environment variables

**2. Manual installation**

\(1\) Compile and package

    git clone https://github.com/easemob/easemob-im-server-sdk.git
    cd /easemob-im-server-sdk
    mvn package -Dmaven.test.skip=true
    mkdir ~/.easemob
    cp im-sdk-cli/target/im-sdk-cli-x.x.x.jar ~/.easemob/im-sdk-cli.jar

\(2\) Add environment variables      add the following command  to `~/.zshrc` or `~/.bashrc`(depending on the SHELL you are using).

    alias im='java -jar ~/.easemob/im-sdk-cli.jar'

\(3\) Refresh the environment variables

    source ~/.zshrc or ~/.bashrc

**Configuration**

IM CLI will read the configuration file from `~/.easemob/config.properties`
You need to retrieve your application developer key from the [Easemob Communication Cloud management background](https://console.easemob.com) and fill it in here.

    vim ~/.easemob/config.properties
    
    im.appkey=your-appkey
    im.client-id=your-app-client-id
    im.client-secret=your-app-client-secret

**Experience**

    # Create a user. The user name is test-user and the password is test-password (the following command should be executed in the terminal)
    ❯ im create user test-user test-password
    
    done

Easemob IM CLI detailed usage documentation is available [here](https://github.com/easemob/easemob-im-server-sdk/blob/master/im-sdk-cli/README.md).

### Frequently Asked Questions

#### Integration issues

1. If you want to see the requests and responses of the Server SDK, you can add following code to configuration :

    ```
    logging.level.com.easemob.im.http=debug
    ```

### Caution

Server SDK is the encapsulation for Easemob IM[server-side API](http://docs-im.easemob.com/im/server/ready/intro) .but it does not encapsulate all the APIs, only the APIs commonly used by developers, click [here](http://docs-im.easemob.com/im/server/ready/sdk#%E4%BD%BF%E7%94%A8) to view the Server SDK API.

For the registered Easemob id rules, Server SDK has its own restrictions.
The regular rule is `^[a-z][0-9a-z-]{1,32}$`, which is different with Easemob id rules described in the [official website]( A7%84%E5%88%99), this is because the current Easemob id registration is more restricted. Server SDK is considered to narrowing the scope of restrictions on the Easemob id registration to make it more standardized.

### Interaction

Any questions you may have about integrating and using the Server SDK, you can join the QQ group for communication, to find the resolution of the problems you encounter.

Group number:`883941811`
