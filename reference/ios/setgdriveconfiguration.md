---
description: Configures Google Drive settings for the SDK with a specified backup option.
---

# setGDriveConfiguration

**Function Signature**

```swift
public func setGDriveConfiguration(
    clientId: String,
    backupOption: GDriveBackupOption
) throws
```

**Parameters**

* `clientId`: The client ID for the Google Drive integration. Obtained from Google Cloud Console.
* `backupOption`: An option from the `GDriveBackupOption` enum that specifies the backup/recover storage type:
  * `.appDataFolder`: Stores backups in the hidden, app-specific "App Data Folder" in Google Drive. This folder is not visible to the user.
  * `.appDataFolderWithFallback`: Attempts to store backups and recover using the "App Data Folder". If recovery fails, it automatically falls back to a user-visible Google Drive folder.
  * `.gdriveFolder(folderName: String)`: Stores backups in a user-visible folder in Google Drive with the specified `folderName`.

**Important Notes**

* The `appDataFolder` and `appDataFolderWithFallback` options are supported starting from SDK version 4.2.0
* Those options cannot be used with an earlier SDK version, backups stored in the App Data Folder will be lost
* Choose the appropriate backup option based on your application's requirements:
  * Use `appDataFolder` to keep wallet backups hidden from users (recommended)
  * Use `gdriveFolder` to allow wallet backups to be seen and modified by the user

**Example Usage**

```swift
// Example 1: Using App Data Folder (recommended)
do {
    try portal.setGDriveConfiguration(
        clientId: "your-google-client-id",
        backupOption: .appDataFolder
    )
} catch {
    print("Error configuring Google Drive with App Data Folder: \(error)")
}

// Example 2: Using visible folder with custom name
do {
    try portal.setGDriveConfiguration(
        clientId: "your-google-client-id",
        backupOption: .gdriveFolder(folderName: "MyAppBackups")
    )
} catch {
    print("Error configuring Google Drive with custom folder: \(error)")
}

// Example 3: Using App Data Folder for backup and recover with fallback to GDrive Folder if recover fails
do {
    try portal.setGDriveConfiguration(
        clientId: "your-google-client-id",
        backupOption: .appDataFolderWithFallback
    )
} catch {
    print("Error configuring Google Drive with fallback: \(error)")
}
```

**Setting Up Google Drive Integration**

See the docs on how to [Configure GDrive storage](https://docs.portalhq.io/resources/backup-options/cloud-storage/configure-gdrive-storage).

\
**Related Documentation**

For more information about Google Drive integration, see:

* [Google Drive Backup Method](https://docs.portalhq.io/guides/ios/back-up-a-wallet/backup-methods#google-drive)
* [App Data Folder vs. GDrive Files](https://docs.portalhq.io/guides/ios/back-up-a-wallet/backup-methods#app-data-folder-vs.-gdrive-files)
* [Back up a wallet](https://docs.portalhq.io/guides/ios/back-up-a-wallet)
