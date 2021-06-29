---
title: REST interface flow limiting instructions
keywords: server
sidebar: server_sidebar
toc: true
permalink: server_rest_interface_flow_limiting_instructions.html
folder: server
---

# REST interface flow limiting instructions



Interface 		Description 																			Community Edition Enterprise Edition 

Enterprise Edition Supplementary Notes
IM user-related 					Single or bulk registration, get and deletion of users; reset user passwords; modify user information; view user online status Single APP call limit 10 times/second;  Single APP call limit 30 times/second; User ID length Maximum 64 characters; bulk registration maximum 60 IDs each time
IM friend relationship 			Single or batch IM friend addition; unfriend relationship; view friend information Single APP call limited to 10 times/sec Single APP call limited to 30 times/sec; User ID length Up to 64 characters
IM blacklist 							Single or batch add/remove blacklist; get specified user from blacklist; Single app call limited to 10 times/sec; Single app call limited to 30 times/sec    
User account blocking 				Block/unblock specified user accounts; force users to go offline Single APP call limited to 10 times/sec; Single APP call limited to 30 times/sec    
Offline messages 							Query the number of offline messages and specify the status of offline messages Single APP call limited to 10 times/sec;  Single APP call limited to 30 times/sec    
Download chat history				 [Download history message file interface (new)](/im/server/basics/chatrecord); Single APP call limited to 1 time/minute Single APP call limited to 1 time/minute   
Upload and download 					Upload/download images, voice, files; download thumbnails Single APP call limited to 10 times/s; Single APP call limited to 30 times/s; Single connection download limited to 500k/s; cluster download call limited to 300 times/s
Sending messages 							Sending text, picture, voice, video and transmissions (the limit of "daily system push messages" in the community version is 20,000 messages); Single APP call is limited to 10 times/s;  Single APP call is limited to 30 times/s;  Message + extended field length is limited to 4k bytes
Group related information				Paging to get group list; create, modify, delete groups; get group details and members; single/batch add or remove group members; group owner transfer; single/batch add or remove blacklist Single APP call limit 10 times/sec; Single APP call limit 30 times/sec Single user can join up to 500 groups;
Chat room related information		Create, modify, delete chat rooms; get chat room details and online user list; single/batch join or quit chat rooms Single APP call limited to 10 times/sec; Single APP call limited to 30 times/sec; Single user can join up to 500 chat rooms;

-------------- ------------------------------------------------------------------------------------------------------------------- ----------- --------------------- --------------------- -------------------------------------------------

------------------------------------------------------------------------

\<WRAP group> \<WRAP half column>
Previous page: [Real-time audio/video recording](/im/server/basics/recordfiledownload) \</WRAP

\<WRAP half column> Next page: [REST API Doc](http://api-docs.easemob.com/)
\</WRAP> \</WRAP
