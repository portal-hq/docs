---
description: This section describes how to check in on the status of each MPC operation
---

# MPC progress callbacks

## MPC Statuses

These are all the possible statuses that will be returned through the callbacks on the MPC operations.&#x20;

```swift
case generatingShare = "Generating share"
case parsingShare = "Parsing share"
case storingShare = "Storing share"
case encryptingShare = "Encrypting share"
case decryptingShare = "Decrypting share"
case readingShare = "Reading share"
case recoveringBackupShare = "Recovering backup share"
case recoveringSigningShare = "Recovering signing share"
case done = "Done"
```

### Generate Status Flow

```swift
case generatingShare = "Generating share"
case parsingShare = "Parsing share"
case storingShare = "Storing share"
case done = "Done"
```

### Backup Status Flow

```swift
case readingShare = "Reading share"
case generatingShare = "Generating share"
case parsingShare = "Parsing share" 
case encryptingShare = "Encrypting share"
case storingShare = "Storing share"
case done = "Done"
```

### Recover Status Flow

```swift
case readingShare = "Reading share"
case decryptingShare = "Decrypting share"
case parsingShare = "Parsing share" 
case generatingShare = "Generating share"
case storingShare = "Storing share"
case done = "Done"
```

### Generate Solana Wallet Status Flow

```swift
case generatingShare = "Generating share"
case parsingShare = "Parsing share"
case storingShare = "Storing share"
case done = "Done"
```

