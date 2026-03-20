> ## Documentation Index
> Fetch the complete documentation index at: https://raw.githubusercontent.com/Tencent-RTC/docs/main/en/llms.txt
> Use this file to discover all available pages before exploring further.

---
product: 'Chat'
tech_stack: 'prouduct'
framework: 'common'
title: 'General Knowledge Base for Product Strategy'
keywords: ['Purchase & Billing', 'Message/Group Related', 'Push Notification Related', 'Intelligent Customer Service']
---

---

---
product: 'Chat'
tech_stack: 'server'
framework: 'common'
title: 'Common Issues Knowledge Base'
keywords: ['console', 'server-side userSig generation', 'restapi', 'pre-callback', 'post-callback', 'nodejs/go/java/python and other server-side language userSig generation example code']
---

# Common Issues Knowledge Base

## UserSig Solutions

### Q: How to generate userSig on the server side?
**A:** For specific methods of generating userSig on the server side, please refer to: https://trtc.io/document/34385?product=chat&menulabel=uikit&platform=react. The documentation provides solutions for multiple server-side languages.

### Q: What's the difference in message format between pre-send and post-send callbacks in Chat? How to distinguish system messages from user messages?
**A:** 
1. Group message pre-send callback content does not include MsgSeq, MsgTime and other information. For details, please refer to the following documentation:
- One-to-one pre-send callback: https://trtc.io/document/34364?product=chat&menulabel=uikit&platform=react
- One-to-one post-send callback: https://trtc.io/document/34365?product=chat&menulabel=uikit&platform=react
- Group pre-send callback: https://trtc.io/document/34374?product=chat&menulabel=uikit&platform=react
- Group post-send callback: https://trtc.io/document/34375?product=chat&menulabel=uikit&platform=react
2. Normal message format: https://trtc.io/document/33527?product=chat&menulabel=uikit&platform=react

### Q: What are group message priorities and group message frequency controls?
**A:** Group messages are divided into 3 priorities (`High`, `Normal`, `Low`). If messages in a group exceed the frequency limit, the backend will prioritize delivering high-priority messages. Therefore, users should choose an appropriate priority based on the importance of the message. Total message frequency control refers to the limit on the maximum number of messages that can be sent per second across all endpoints for a single group. The default value is 40 messages/second, using average frequency limiting per second. When the number of messages exceeds the limit, the backend prioritizes delivering messages with relatively higher priority, and messages with the same priority are randomly ordered.

### Q: How to intercept messages sent by users?
**A:** Business servers can intercept through pre-send callbacks. For specific documentation, please refer to:
- One-to-one pre-send callback: https://trtc.io/document/34364?product=chat&menulabel=uikit&platform=react
- Group pre-send callback: https://trtc.io/document/34375?product=chat&menulabel=uikit&platform=react

### Q: How to delete and manage accounts?
**A:** REST API can be used to delete, import, and query accounts. For specific documentation, please refer to:
- Delete account: https://trtc.io/document/34955?product=chat&menulabel=uikit&platform=react
- Import single account: https://trtc.io/document/34953?product=chat&menulabel=uikit&platform=react
- Import multiple accounts: https://trtc.io/document/34954?product=chat&menulabel=uikit&platform=react
- Query account: https://trtc.io/document/34956?product=chat&menulabel=uikit&platform=react

### Q: How to handle when the number of accounts exceeds the limit?
**A:** When importing accounts, if the number of accounts exceeds the limit and you need to create more than 100 accounts, you need to upgrade your application to the Professional Edition. For specific operation instructions, please refer to: https://trtc.io/document/55096.

### Q: How to delete conversations on the server side?
**A:** Server-side conversation deletion can be done using REST API. For specific documentation, please refer to: https://trtc.io/document/34348?product=chat&menulabel=uikit&platform=react.

---

---
product: 'Chat'
tech_stack: 'server'
framework: 'common'
title: 'Server Error Code Reference'
keywords: ['error code', 'error', 'login failure', '70001']
---

# Server Error Code Reference

## Server Error Codes (Topic: Server)

## Error Code 70001
**Cause:** UserSig has expired.
**Solution:** Please regenerate the UserSig. It is recommended to set the UserSig validity period to no less than 24 hours.

## Error Code 70003
**Cause:** Invalid UserSig.
**Solution:** Please regenerate the UserSig. Refer to the official documentation for generation methods: https://trtc.io/document/34385?platform=android&product=chat&menulabel=core%20sdk.

## Error Code 70009
**Cause:** UserSig verification failed. The UserSig may have been generated using a key or private key from a different SDKAppID.
**Solution:** Please verify that the requested SDKAppID matches the key or private key used to generate the UserSig.

## Error Code 70013
**Cause:** The userID in the request does not match the userID used when generating the UserSig.
**Solution:** Please check if the userID used to login to IM matches the userID used to generate the UserSig.

## Error Code 70014
**Cause:** The SDKAppID in the request does not match the SDKAppID used when generating the UserSig.
**Solution:** You can verify the UserSig using the IM Console > Development Tools > UserSig Generation & Verification tool: https://console.trtc.io/usersig.

## Error Code 20003
**Cause:** Invalid or non-existent userID for message sender or receiver.
**Solution:** Please confirm that the userID has been logged into IM under your SDKAppID.

## Error Code 20009
**Cause:** Sender and receiver are not friends, sending is prohibited.
**Solution:** The sender and receiver are not friends. Sending is prohibited when friend relationship verification is configured for one-to-one messages. To send messages to non-friends, you need to configure this in the console or add them as friends first.

## Error Code 20010
**Cause:** Sender is not in the receiver's friend list, sending is prohibited.
**Solution:** When sending one-to-one messages, the sender is not in the receiver's friend list (one-way relationship), sending is prohibited.

## Error Code 3122
**Cause:** Your current package does not support this feature.
**Solution:** You can upgrade to the Ultimate Edition package: https://console.trtc.io/subscription/buy/chat?packType=pro.

## Error Code 60020
**Cause:** Cloud search feature is not enabled for your current package.
**Solution:** You can enable Cloud Search: https://console.trtc.io/chat/plugin/TUICloudSearch.

## Error Code 60018
**Cause:** Cloud search request exceeded backend rate limit. Each user is allowed 2 search requests per second.
**Solution:** Please adjust the request frequency and try again later.

## Error Code 80004
**Cause:** Sensitive content detected in message or profile text.
**Solution:** Please check if the message or profile contains sensitive information.

## Error Code 10021
**Cause:** Group ID is already in use.
**Solution:** Please choose a different group ID.

## Error Code 10007
**Cause:** Common reasons include:
- The group type does not support this operation by default. Please check the current group type.
- The operation account lacks sufficient permissions (e.g., in mute operations, an admin can mute regular members but not the group owner).
- The account calling the REST API is not an admin account for the SDKAppID.
**Solution:** Please check the group type or group member role. If the error occurs when calling REST API, verify that the account is an admin account for the corresponding SDKAppID.

## Error Code 60010
**Cause:** Request requires App admin privileges.
**Solution:** Please verify that the requesting account is an admin for the SDKAppID.
