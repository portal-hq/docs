---
description: Creates a backup of the wallet using the specified backup method.
---

# backupWallet

**Function Signature**

```swift
public func backupWallet(
    _ method: BackupMethods,
    usingProgressCallback: ((MpcStatus) -> Void)? = nil
) async throws -> (cipherText: String, storageCallback: () async throws -> Void)
```

**Parameters**

* `method`: The backup method to use. Supported methods:
  * `.GoogleDrive`: Back up to Google Drive
  * `.iCloud`: Back up to iCloud
  * `.Password`: Back up with password protection
  * `.Passkey`: Back up with passkey authentication
  * `.local`: Back up to local storage
*   `usingProgressCallback`: Optional callback to track backup progress.&#x20;

    Receives an `MpcStatus` object containing:

    * `status`: Current operation being performed:
      * `.readingShare`: Reading the stored share
      * `.generatingShare`: Generate the share
      * `.parsingShare`: Parsing the share data
      * `.encryptingShare`: Encrypt the share data
      * `.storingShare`: Saving the recovered share
      * `.done`: Process completed
    * `done`: Boolean indicating whether the operation is complete

**Returns**

A tuple containing:

* `cipherText`: The encrypted backup data
* `storageCallback`: A callback function that must be called to complete the backup process

**Throws**

Various backup method-specific errors if the backup process fails

**Example Usage**

```swift
do {
    let backup = try await portal.backupWallet(.iCloud) { status in
        switch status.status {
        case .readingShare:
            print("Reading share...")
        case .generatingShare:
            print("Creating backup share...")
        case .encryptingShare:
            print("Encrypting data...")
        case .storingShare:
            print("Storing backup...")
        case .done:
            print("Backup completed!")
        default:
            break
        }
    }
    
    // Complete the backup process
    try await backup.storageCallback()
    print("Backup cipher text: \(backup.cipherText)")
} catch {
    print("Error backing up wallet: \(error)")
}
```
