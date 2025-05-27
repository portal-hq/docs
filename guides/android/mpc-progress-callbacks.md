---
description: This section describes how to check in on the status of each MPC operation
---

# MPC progress callbacks

## MPC Statuses

These are all the possible statuses that will be returned through the callbacks on the MPC operations.&#x20;

```kotlin
DecryptingShare("Decrypting share")
EncryptingShare("Encrypting share")
GeneratingShare("Generating share")
ParsingShare("Parsing share")
ReadingShare("Reading share")
RecoveringBackupShare("Recovering backup share")
RecoveringSigningShare("Recovering signing share")
StoringShare("Storing share")
Done("Done")
```

### Generate Status Flow

```kotlin
GeneratingShare("Generating share")
ParsingShare("Parsing share")
StoringShare("Storing share")
Done("Done")
```

### Backup Status Flow

```kotlin
ReadingShare("Reading share")
GeneratingShare("Generating share")
ParsingShare("Parsing share")
EncryptingShare("Encrypting share")
StoringShare("Storing share")
Done("Done")
```

### Recover Status Flow

```kotlin
ReadingShare("Reading share")
DecryptingShare("Decrypting share")
RecoveringSigningShare("Recovering signing share")
GeneratingShare("Generating share")
ParsingShare("Parsing share")
StoringShare("Storing share")
RecoveringBackupShare("Recovering backup share")
GeneratingShare("Generating share")
ParsingShare("Parsing share")
EncryptingShare("Encrypting share")
StoringShare("Storing share")
Done("Done")
```
