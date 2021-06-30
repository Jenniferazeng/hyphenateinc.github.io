---
title: Common Error Codes of Server-side REST API
keywords: server
sidebar: server_sidebar
toc: true
permalink: server_error_codes.html
folder: server
---

## Example of HTTP return results

After the REST interface calling, HTTP will use status code and the standard JSON format to return the  result. The specific error can be determined by the error field in the return data.

-   HTTP status return code 200 (success)
-   HTTP status return code 4xx (Request Error). These status codes indicate that the request may have error and interfere the processing of server.
-   HTTP status return code 5xx (Server Error) These status codes indicate that an internal error occurred while the server was attempting to process the request.

#### Return Example
![](/images/server_return_error_example.png)

It is recommended to do fault tolerance for the Chat REST API results called by APP's own server side. For example, It should catch the exception returned by the interface calling, and the timeout errors should be retried. For system errors or errors after retries, they should be recorded in the system log and provide the alert to DevOps staff to do remedial measures, such as manual retransmission.

## Index Error Status Code

<table border="1" cellspacing="0" bordercolor="#000000">
  <tr>
    <th>Status Code</th>
    <th>Description</th>
  </tr>
  <tr>
    <td>400</td>
    <td>(Error request) The server does not understand the syntax of the request.</td>
  </tr>
  <tr>
    <td>401</td>
    <td>(Unauthorized) requests require authentication. For interfaces that require a token, the server may return this response.</td>
  </tr>
  <tr>
    <td>403</td>
    <td>(Forbidden) The server rejects the request. For group/chat room services, it represents that this call does not follow the correct operation logic of group/chat room, such as adding the member who is already in the group, or removing a member who does not exist in the chat room.</td>
  </tr>
  <tr>
    <td>404</td>
    <td>(Not found) The server could not find the interface to request.</td>
  </tr>
  <tr>
    <td>405</td>
    <td>(Wrong request method) Please follow the interface instructions on the official website of  Chat and use the interface GET, POST and other request methods correctly.</td>
  </tr>
  <tr>
    <td>408</td>
    <td>(Request timeout) A timeout occurred while the server was waiting for the request.</td>
  </tr>
  <tr>
    <td>413</td>
    <td>(Request body is too large) If the request body is more than 5kb, please split it into smaller requests and retry.</td>
  </tr>
  <tr>
    <td>415</td>
    <td>The type of request body is not supported.</td>
  </tr>
  <tr>
    <td>429</td>
    <td>(Service unavailable) Requesting interface in a frequency which exceeds limit or exceed the community edition limit. Please contact business if needed.</td>
  </tr>
  <tr>
    <td>500</td>
    <td>(Internal server error) The server encountered an error and could not complete the request.</td>
  </tr>
  <tr>
    <td>501</td>
    <td>(Not yet implemented) The server does not have the function to complete the request. For example, the server may return this code if it cannot recognize the request method.</td>
  </tr>
  <tr>
    <td>502</td>
    <td>(Error Gateway) The server acts as a gateway or proxy and receives an invalid response from the uplink server.</td>
  </tr>
  <tr>
    <td>503</td>
    <td>(Server timeout) Service Unavailable.</td>
  </tr>
  <tr>
    <td>504</td>
    <td>(Gateway timeout) The server acts as a gateway or proxy and does not receive the request from the uplink server timely.</td>
  </tr>
</table>

## Index Error result description

<table border="1" cellspacing="0" bordercolor="#000000">
  <tr>
    <th>HTTP Status Code</th>
    <th>Error</th>
    <th>Error Description</th>
    <th>Possible reasons</th>
  </tr>
  <tr>
    <td>400</td>
    <td>invalid_grant</td>
    <td>invalid username or password </td>
    <td>User name or password input error</td>
  </tr>
  <tr>
    <td>400</td>
    <td>invalid_grant</td>
    <td>"client_id does not match" </td>
    <td>"client_id does not match" is the client_id input is incorrect. "client_secret does not match" is the client_secret input is incorrect. Please find the details of client_id and client_secret in the the corresponding application in management background. </td>
  </tr>
  <tr>
    <td>400</td>
    <td>json_parse</td>
    <td>"Unexpected character ('=' (code 61)): was expecting a colon to separate field name and value\n at [Source: java.io.BufferedInputStream\@170e3f35; line: 1, column: 23]"</td>
    <td>The request body does not follow the standard JSON format when sending the request, and the server cannot parse it correctly.</td>
  </tr>
  <tr>
    <td>400</td>
    <td>illegal_argument</td>
    <td>"Entity user requires a property named username"</td>
    <td>The user creation request body does not provide "username"</td>
  </tr>
  <tr>
    <td>400</td>
    <td>illegal_argument</td>
    <td>"password or pin must provided" </td>
    <td>The user creation request body does not provide "password" or provides a password but the value is null.</td>
  </tr>
  <tr>
    <td>400</td>
    <td>illegal_argument</td>
    <td>"newpassword is required" </td>
    <td>The request body to change the user's password does not provide the newpassword attribute</td>
  </tr>
  <tr>
    <td>400</td>
    <td>illegal_argument</td>
    <td>"oldPassword is required"</td>
    <td>This is caused by not adding the header of the administrator token, you need to add the argument of the administrator token to the header</td>
  </tr>
  <tr>
    <td>400</td>
    <td>illegal_argument</td>
    <td>"group member username1 doesn't exist"</td>
    <td>When adding groups in batch, the username of the new member to join the group does not exist.</td>
  </tr>
  <tr>
    <td>400</td>
    <td>illegal_argument</td>
    <td>"this is an invalid request."</td>
    <td>If the request is invalid, please check the url, header and body of the called interface to see if they meet the requirements of the called interface, you can use the curl command to test</td>
  </tr>
  <tr>
    <td>400</td>
    <td>illegal_argument</td>
    <td>"from can't be empty"</td>
    <td>from represents the sender of the message. If there is no field server will default to "from":"admin". If there is a from field but the value of the null string (""), the request fails, return 400</td>
  </tr>
  <tr>
    <td>400</td>
    <td>illegal_argument</td>
    <td> "target_type can only be 'users' or 'chatgroups' or 'chatrooms'"</td>
    <td> target_type can only be 'users' or 'chatgroups' or 'chatrooms'. for other strings, the request fails and returns 400</td>
  </tr>
  <tr>
    <td>400</td>
    <td>illegal_argument</td>
    <td>"username [hxtest1@chat.com] is not legal"</td>
    <td>he username used for registration is not legal, username (Chat user ID) rules can be found in <a href="/server/server_user_system_integration.html"> [documentation] </a></td>
  </tr>
  <tr>
    <td>400</td>
    <td>illegal_argument</td>
    <td>"This chatmessage request is not supported" </td>
    <td>It may be because the incoming time format is not correct, the correct format is: YYYYMMDDHH. For example, to get the chat logs from 12:00 to 13:00 on June 30, 2021, set it like this: 2021063012</td>
  </tr>
  <tr>
    <td>400</td>
    <td>illegal_argument</td>
    <td> "illegal arguments: appkey: chat-demo#chatdemoui, time: 2021063012, maybe chat message history is expired or unstored" </td>
    <td>Chat logs for the corresponding time have not been generated or have expired, and chat logs are currently saved for free for 3 days.</td>
  </tr>
  <tr>
    <td>400</td>
    <td>invalid_parameter</td>
    <td>"some of [groupid] are not valid fields"</td>
    <td>Modify group information currently only supports the modification of "group name", "group description", "group maximum number of people", this error is that the modifed parameters are not supported, such as modify grouppid.</td>
  </tr>
  <tr>
    <td>400</td>
    <td>required_property_not_found</td>
    <td>"Entity user requires a property named username"</td>
    <td>This is the caused by the username being reset not existing</td>
  </tr>
  <tr>
    <td>400</td>
    <td>duplicate_unique_property_exists</td>
    <td> "Application null Entity user requires that property named username be unique, value of hxtest1 exists"</td>
    <td>Registered username already exists, return 400. Note: In the situation of batch registration, if a call returns an ID which already exists, the other non-existent IDs registered in this call will not be registered, you need to remove the existing IDs from the array and call registration again</td>
  </tr>
  <tr>
    <td>401</td>
    <td>unauthorized</td>
    <td>"registration is not open, please contact the app admin"</td>
    <td>description of error_description is appkey used to authorize registration, you need header plus administrator token to have permission to register users. If add token, it returns 401. The token may be invalid, it is recommended to retrieve and retry.</td>
  </tr>
  <tr>
    <td>401</td>
    <td>unauthorized</td>
    <td>"Unable to authenticate due to expired access token"</td>
    <td>the token expiredor no token passed</td>
  </tr>
  <tr>
    <td>401</td>
    <td>auth_bad_access_token</td>
    <td>"Unable to authenticate due to corrupt access token"</td>
    <td>The token used to send the request was incorrect. Note: Not token expiration</td>
  </tr>
  <tr>
    <td>401</td>
    <td>auth_bad_access_token</td>
    <td>"Unable to authenticate"</td>
    <td>Invalid token, the format of the token is right, but the token is not generated by the system accepting the request, the system can not recognize the token</td>
  </tr>
  <tr>
    <td>403</td>
    <td>forbidden_op</td>
    <td>"can not join this group, reason:user: hxtest1 already in group: 40659491815425\n"</td>
    <td>Group additions return 403 indicating that the user is already in the group.</td>
  </tr>
  <tr>
    <td>403</td>
    <td>forbidden_op</td>
    <td>"users [hxtest100] are not members of this group!" </td>
    <td>when the group kicks people, returning 403 indicating that the kicked user is not in the group.</td>
  </tr>
  <tr>
    <td>403</td>
    <td>forbidden_op</td>
    <td>"user: username1 doesn't exist in group: 40659491815425"</td>
    <td>Transferring the ownership of a group returns 403. error_description stating that the user being transferred to is not a member of the group and the ownership cannot be transferred.</td>
  </tr>
  <tr>
    <td>403</td>
    <td>forbidden_op</td>
    <td>"new owner and old owner are the same"</td>
    <td>ransferring the ownership of a group returns 403. error_description indicates that the ownership transferation is already done.</td>
  </tr>
  <tr>
    <td>403</td>
    <td>forbidden_op</td>
    <td>"forbidden operation on group owner!"</td>
    <td>You cannot blacklist a group owner.</td>
  </tr>
  <tr>
    <td>403</td>
    <td></td>
    <td>"can not join this group, reason：user %s has joined too many groups/chatroom!"</td>
    <td>The number of users joining a group or chat room exceeds the maximum.</td>
  </tr>
   <tr>
    <td>403</td>
    <td></td>
    <td>"this appKey has create too many groups/chatrooms!"</td>
    <td>The number of groups or chat rooms created under appkey to reach the maximum.</td>
  </tr>
  <tr>
    <td>403</td>
    <td></td>
    <td></td>
    <td>Failure to call interface, because you didn't open audio and video value-added service </td>
  </tr>
  <tr>
    <td>404</td>
    <td>organization_application_not_found</td>
    <td>"Could not find application for hx/hxdeo2 from URI: hx/hxdeo2/token"</td>
    <td>hx/hxdeo2 this setting is incorrect or does not exist, or the baseurl group is set incorrectly (only for the vip cluster appkey), the correct one is orgname/appname, which the "#" of the appkey is replaced with "/".</td>
  </tr>
  <tr>
    <td>404</td>
    <td>service_resource_not_found</td>
    <td>"Service resource not found"</td>
    <td>The resource specified by the URL does not exist. the user related interface represents user does not exist, group related interface represents group does not exist, chat room related interface represents chat room does not exist.</td>
  </tr>
   <tr>
    <td>404</td>
    <td>service_resource_not_found</td>
    <td>"Service resource not found"</td>
    <td>The queried username does not exist. if the username exists in the user list, it is because there is dirty data, you can use uuid to replace username to delete the ID, and then use the username to create again.</td>
  </tr>
  <tr>
    <td>404</td>
    <td></td>
    <td></td>
    <td>The deleted username does not exist, if the username exists in the user list, it can be deleted using the user's uuid to replace username.</td>
  </tr>
  <tr>
    <td>404</td>
    <td>storage_object_not_found</td>
    <td>"Failed to find chat message history download url for appkey: hx#hxdemo2, time: 2021063012"</td>
    <td>There is no chat record for the corresponding time, if you are sure there is a chat record, please submit a work order to the Chat technical support team for confirmation.</td>
  </tr>
  <tr>
    <td>413</td>
    <td>Request Entity Too Large</td>
    <td>"Request Entity Too Large"</td>
    <td>The request body is too large, such as uploading a too large file; or sending a message when the message body is too large, the request body will cause a 413 error if it exceeds 5kb, and needs to be split into several smaller requests to retry. when use the length of the user message + extension field, please make sure the size of them is within 4k bytes.</td>
  </tr>
   <tr>
    <td>415</td>
    <td>web_application</td>
    <td>"Unsupported Media Type"</td>
    <td>The request body type is not supported, please check whether the header adds "Content-Type":"application/json", whether the body follow the standard JSON format, and then confirm whether there are parameters in the header that are not required by the interface, which can be discarded.</td>
  </tr>
  <tr>
    <td>429</td>
    <td>resource_limited</td>
    <td>"You have exceeded the limit of the community edition. Please upgrade to the enterprise edition."</td>
    <td>It represents that the community edition restriction is triggered, <a href="http://www.easemob.com/pricing/im"> Community Edition Restriction Introduction </a>. If you have opened the enterprise edition, please contact Chat Business to tackle it.</td>
  </tr>
  <tr>
    <td>429</td>
    <td>reach_limit</td>
    <td>"This request has reached api limit"</td>
    <td>If the number of interface calls per second is exceeded, increase the call interval or contact business to adjust the flow limit size, see <a href="http://docs.easemob.com/im/other/errorcode/restastrict"> Interface Flow Limit Criteria </a></td>
  </tr>
  <tr>
    <td>500</td>
    <td>no_full_text_index</td>
    <td>"Entity 'user' with property named 'username' is not full text indexed. You cannot use the 'contains' operand on this field"</td>
    <td>Username does not support full-text indexing and cannot operate "contain" for this field.</td>
  </tr>
   <tr>
    <td>500</td>
    <td>unsupported_service_operation</td>
    <td>"Service operation not supported"</td>
    <td>The request method is not supported by the URL of request.</td>
  </tr>
  <tr>
    <td>500</td>
    <td>web_application</td>
    <td>"javax.ws.rs.WebApplicationException"</td>
    <td>Wrong request, sent to an unprovided API</td>
  </tr>
</table>

