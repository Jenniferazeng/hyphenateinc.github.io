---
title: Platform API interface flow limiting instructions
keywords: server
sidebar: server_sidebar
toc: true
permalink: server_rest_interface_flow_limiting_instructions.html
folder: server
---

<table border="1" cellspacing="0" bordercolor="#000000">
  <tr>
    <th>Interface</th>
    <th>Description</th>
    <th>Community Edition</th>
    <th>Enterprise Edition </th>
    <th>Enterprise Edition Supplementary Notes</th>
  </tr>
  <tr>
    <td>Chat user-related</td>
    <td> Single or bulk registration, get and deletion of users; reset user passwords; modify user information; view user online status</td>
    <td>Single APP call limit 10 times/second</td>
    <td>Single APP call limit 30 times/second</td>
    <td>User ID length Maximum 64 characters; bulk registration maximum 60 IDs each time</td>
  </tr>
  <tr>
    <td>Chat friend relationship</td>
    <td>Single or batch Chat friend addition; unfriend relationship; view friend information</td>
    <td>Single APP call limited to 10 times/sec</td>
    <td>Single APP call limited to 30 times/sec</td>
    <td>User ID length Up to 64 characters</td>
  </tr>
  <tr>
    <td>Chat blacklist</td>
    <td>Single or batch add/remove blacklist; get specified user from blacklist</td>
    <td>Single app call limited to 10 times/sec</td>
    <td>Single app call limited to 30 times/sec</td>
    <td></td>
  </tr>
  <tr>
    <td>User account blocking</td>
    <td>Block/unblock specified user accounts; force users to go offline</td>
    <td>Single APP call limited to 10 times/sec</td>
    <td>Single APP call limited to 30 times/sec</td>
    <td></td>
  </tr>
  <tr>
    <td>Offline messages </td>
    <td>Query the number of offline messages and specify the status of offline messages</td>
    <td>Single APP call limited to 10 times/sec</td>
    <td>Single APP call limited to 30 times/sec</td>
    <td></td>
  </tr>
  <tr>
    <td>Download chat history </td>
    <td><a href="/server/server_chat_log.html"> Download history message file interface (new) </a></td>
    <td>Single APP call limited to 1 time/minute</td>
    <td>Single APP call limited to 1 time/minute</td>
    <td></td>
  </tr>
  <tr>
    <td>Upload and download</td>
    <td>Upload/download images, voice, files; download thumbnails</td>
    <td>Single APP call limited to 10 times/s</td>
    <td>Single APP call limited to 30 times/s</td>
    <td>Single connection download limited to 500k/s; cluster download call limited to 300 times/s</td>
  </tr>
  <tr>
    <td>Sending messages</td>
    <td>Sending text, picture, voice, video and transmissions (the limit of "daily system push messages" in the community version is 20,000 messages)</td>
    <td>Single APP call is limited to 10 times/s</td>
    <td>Single APP call is limited to 30 times/s</td>
    <td>Message + extended field length is limited to 4k bytes</td>
  </tr>
  <tr>
    <td>Group related information</td>
    <td>Paging to get group list; create, modify, delete groups; get group details and members</td>
    <td>single/batch add or remove group members; group owner transfer; single/batch add or remove blacklist Single APP call limit 10 times/sec</td>
    <td>Single APP call limit 30 times/sec</td>
    <td>Single user can join up to 500 groups</td>
  </tr>
  <tr>
    <td>Chat room related information</td>
    <td>Create, modify, delete chat rooms; get chat room details and online user list<td>
    <td>single/batch join or quit chat rooms Single APP call limited to 10 times/sec</td>
    <td>Single APP call limited to 30 times/sec</td>
    <td>Single user can join up to 500 chat rooms</td>
  </tr>
</table>

