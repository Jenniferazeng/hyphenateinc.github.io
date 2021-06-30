---
title: Contact
keywords: web
sidebar: web_sidebar
toc: true
permalink: web_relationship.html
folder: web
---


# Contact management 

Contact management gives you a better experience in chat functions. 
Easemob Web IM SDK supports friend system management. The operations related to contact are as follows:

-   Query contact list 

-   Monitor friend status events

-   Add contact

-   Handling friend requests

-   Delete contact 

-   Blacklist

Multiple friend system managing operations, which covers various integration scenarios.

------------------------------------------------------------------------

## Parameter explanation

  | Name | FieldName | DataType | Description |
  | ---- | --------- | -------- | ----------- |
  | User ID | username  |  String |  User ID is the only identification of Agora users, which is unique within the scope of AppKey |

## Query contact list 

Call getRoster to query the list of contact, the sample code is as follows: 

``` javascript
conn.getRoster().then( (res) => {
  console.log(res) // res.data > ['user1', 'user2']
});
```

------------------------------------------------------------------------

## Listen to friend status events

Register the following events in the SDK conn.listen() to monitor the status of your contact. The sample code is as follows: 

``` javascript
conn.listen({
  onContactInvited: function(msg){}, // Receive friend invitation
  onContactDeleted: function(){}, // Call back this method when it is deleted
  onContactAdded: function(){}, // Call back this method when a contact is added 
  onContactRefuse: function(){}, // Friend request denied
  onContactAgreed: function(){} // Friend request denied
}）
```

------------------------------------------------------------------------

## Add friend 

Call addContact to add a friend, the sample code is as follows:

``` javascript
let message = 'Nice to meet you here!';
conn.addContact('username', message);
```

------------------------------------------------------------------------

## Handling friend requests

When receiving a request of **"add friend"**, there are two methods: 

-   Agree to add as a friend

-   Refuse to add as a friend

The specific code implementation example is as follows:

### Agree to add as a friend

Call acceptInvitation method to agree to add contact, the sample code is as follows:

``` javascript
conn.acceptInvitation('username')
```

### Refuse to add as a friend

Call declineInvitation method to refuse to add a friend, the sample code is as follows:

``` javascript
conn.declineInvitation('username')
```

------------------------------------------------------------------------

## Delete friend 

Call deleteContact method to delete a friend, the code example is as follows: 

``` javascript
conn.deleteContact('username');
```

------------------------------------------------------------------------

## Blacklist

In the integrated blacklist operations, there are the following blacklist function operations: 

-   Add contact to blacklist

-   Get blacklist list 

-   Remove contact from the blacklist 

### Add contact to blacklist

After adding a friend to the blacklist, you can still be found on its friend list, but it cannot send you messages. 

``` javascript
// User ID, add one as a single user ID; batch add as an array of user IDs, such as ["user1","user2",...] 
conn.addToBlackList({
  name:['user1','user2']
});
```

------------------------------------------------------------------------

### Get blacklist list

Call the getBlacklist function to get the blacklist, the sample code is as follows: 

``` javascript
conn.getBlacklist().then((res)=>{
  console.log(res);  // res.data > ['user1', 'user2']
})
```

------------------------------------------------------------------------

### Remove contact from blacklist 

``` javascript
// Delete one as a single user ID; batch delete as an array of user IDs, such as ["user1","user2"] 
conn.removeFromBlackList({
  name: ['user1']
});
```
