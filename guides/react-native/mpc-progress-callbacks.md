---
description: This section describes how to check in on the status of each MPC operation
---

# MPC progress callbacks

## MPC Statuses

These are all the possible statuses that will be returned through the callbacks on the MPC operations.&#x20;

```typescript
generatingShare = "Generating share"
parsingShare = "Parsing share"
storingShare = "Storing share"
encryptingShare = "Encrypting share"
decryptingShare = "Decrypting share"
readingShare = "Reading share"
recoveringBackupShare = "Recovering backup share"
recoveringSigningShare = "Recovering signing share"
done = "Done"
```

### Generate Status Flow

```swift
{"done": false, "status": "Generating share"}
{"done": false, "status": "Parsing share"}
{"done": false, "status": "Storing share"}
{"done": true, "status": "Done"}
```

### Backup Status Flow

```swift
{"done": false, "status": "Reading share"}
{"done": false, "status": "Generating share"}
{"done": false, "status": "Parsing share"}
{"done": false, "status": "Encrypting share"}
{"done": false, "status": "Storing share"}
{"done": true, "status": "Done"}
```

### Recover Status Flow

```swift
{"done": false, "status": "Reading share"}
{"done": false, "status": "Decrypting share"}
{"done": false, "status": "Parsing share"}
{"done": false, "status": "Recovering signing share"}
{"done": false, "status": "Generating share"}
{"done": false, "status": "Parsing share"}
{"done": false, "status": "Storing share"}
{"done": false, "status": "Recovering backup share"}
{"done": false, "status": "Generating share"}
{"done": false, "status": "Parsing share"}
{"done": false, "status": "Encrypting share"}
{"done": false, "status": "Storing share"}
{"done": true, "status": "Done"}
```
