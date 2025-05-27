---
description: >-
  This guide will walk you through how to create a backup of a Portal client's
  wallet.
---

# Back up a wallet

## Portal-Managed Backups

Portal lets you securely back up your users' MPC wallets so they can recover their wallets even if their device is lost or damaged. By default, Portal encrypts and stores both backup shares ("Portal-Managed Backups"):

1. The **client backup share** is encrypted on the user's device, with the encryption key stored using their chosen backup method (Google Drive, iCloud, Password, or Passkey). The encrypted share is then stored by Portal.
2. The **custodian backup share** is encrypted and stored by Portal, with the encryption key stored in our KMS infrastructure.

{% hint style="info" %}
By default, Portal manages storing both the encrypted client backup share and the custodian backup share for you. If you prefer to store and manage the backup shares in your own infrastructure instead of using Portal-Managed Backups, see our [Self-Managed Backups](../../resources/self-managed-backups.md) guide.
{% endhint %}

Both the client backup share and the custodian backup share are necessary to recover a Portal wallet.

## Backup Methods

You can choose one or more backup methods for storing the encryption key for the client backup share.

### Passkey + Enclave

Allow customers to create a native passkey on their device that is used to authenticate into a secure enclave that holds the encryption key for the user. Customer's passkeys are backed up to the native cloud storage for their device.&#x20;

#### Implementation Requirements

1. Initialize passkey storage as a backup option in the Portal Config Object.&#x20;
2. Configuring the relying party

#### Use Portal as your relying party&#x20;

1. Add `portalhq.io` as a web credential domain in your app.
2. Share your app bundle id with the Portal Team.

#### Use your own domain as the relying party

Ensure you have set up your associate domain correctly in your app and that you are serving an aasa file from whatever your relying party domain is set to. You will need to be sure you have the `webcredential` field set properly for your app in your aasa file.&#x20;

Resources from apple:

* [Configuring an associated domain](https://developer.apple.com/documentation/xcode/configuring-an-associated-domain#Provide-an-Apple-App-Site-Association-file)
* [Supporting associated domains](https://developer.apple.com/documentation/xcode/supporting-associated-domains#Add-the-associated-domain-file-to-your-website)

#### Relying party

A relying party is a trusted domain that is tied to the public key credentials of your users for their passkey . We offer the option to use `portalhq.io` as your relying party domain. It requires you to add `portalhq.io` as an Associated Domain in your iOS application and share your team id + application bundle id. If you already have your domain as a `webcredential` for your application then you can simply pass in your domain as the relying party and everything should work.&#x20;

{% tabs %}
{% tab title="Portal-Managed Backups (default)" %}
```swift
  // Run backup.
  let (
    encryptedClientBackupShare,
    storageCallback
  ) = try await portal.backupWallet(.Passkey) { status in
    // (Optional) Create a progress indicator here in the progress callback.
  }
```
{% endtab %}

{% tab title="Self-Managed Backups" %}
```swift
  // Your API URL
  let yourApiUrl: String = "https://YOUR_API_URL.com"
      
  // Run backup.
  let (
    encryptedClientBackupShare,
    storageCallback
  ) = try await portal.backupWallet(.Passkey) { status in
    // (Optional) Create a progress indicator here in the progress callback.
  }
      
  // Obtain your API's URL for storing the encrypted user backup share.
  guard let url = URL(string: "\(yourApiUrl)/users/\(userId)/store-encrypted-user-backup-share") else {
    throw URLError(.badURL)
  }

  // Store the encrypted user backup share on your API.
  let requests = PortalRequests()
  try await requests.post(
    url,
    andPayload: [
      "backupMethod": withMethod.rawValue,
      "encryptedClientBackupShare": encryptedClientBackupShare,
    ]
  )

  // Call the storageCallback to notify Portal you stored the user backup share successfully.
  try await storageCallback()
  
  // ✅ The user has now backed up with their passkey successfully
```
{% endtab %}
{% endtabs %}

### Password/PIN

Allow customers to create a password/pin. Customers can either remember the password or store it in a password storage manager.

#### Implementation Requirements

1. Create a UI for password input.&#x20;
2. Enforce password requirements. Customer can choose between password, PIN code, passcode, or any other text-based input.
3. If user forgets password there are no additional recovery options.

{% tabs %}
{% tab title="Portal-Managed Backups" %}
```swift
  // Set the user's password.
  try portal.setPassword("THE-USER-PASSWORD")
  
  // Run backup.
  let (
    encryptedClientBackupShare,
    storageCallback
  ) = try await portal.backupWallet(.Password) { status in
    // (Optional) Create a progress indicator here in the progress callback.
  }
```
{% endtab %}

{% tab title="Self-Managed Backups" %}
```swift
  // Your API URL
  let yourApiUrl: String = "https://YOUR_API_URL.com"
    
  // Set the user's password.
  try portal.setPassword("THE-USER-PASSWORD")
      
  // Run backup.
  let (
    encryptedClientBackupShare,
    storageCallback
  ) = try await portal.backupWallet(.Password) { status in
    // (Optional) Create a progress indicator here in the progress callback.
  }
      
  // Obtain your API's URL for storing the encrypted user backup share.
  guard let url = URL(string: "\(yourApiUrl)/users/\(userId)/store-encrypted-user-backup-share") else {
    throw URLError(.badURL)
  }

  // Store the encrypted user backup share on your API.
  let requests = PortalRequests()
  try await requests.post(
    url,
    andPayload: [
      "backupMethod": withMethod.rawValue,
      "encryptedClientBackupShare": encryptedClientBackupShare,
    ]
  )

  // Call the storageCallback to notify Portal you stored the user backup share successfully.
  try await storageCallback()
  
  // ✅ The user has now backed up with their password successfully
```
{% endtab %}
{% endtabs %}

### iCloud

See the docs on how to configure [icloud.md](../../resources/backup-options/icloud.md "mention").

For the `iCloud` action handling:

{% tabs %}
{% tab title="Portal-Managed Backups" %}
```swift
  // Run backup.
  let (
    encryptedClientBackupShare,
    storageCallback
  ) = try await portal.backupWallet(.iCloud) { status in
    // (Optional) Create a progress indicator here in the progress callback.
  }
```
{% endtab %}

{% tab title="Self-Managed Backups" %}
```swift
  // Your API URL
  let yourApiUrl: String = "https://YOUR_API_URL.com"
    
  // Set the user's password.
  try portal.setPassword("THE-USER-PASSWORD")
      
  // Run backup.
  let (
    encryptedClientBackupShare,
    storageCallback
  ) = try await portal.backupWallet(.iCloud) { status in
    // (Optional) Create a progress indicator here in the progress callback.
  }
      
  // Obtain your API's URL for storing the encrypted user backup share.
  guard let url = URL(string: "\(yourApiUrl)/users/\(userId)/store-encrypted-user-backup-share") else {
    throw URLError(.badURL)
  }

  // Store the encrypted user backup share on your API.
  let requests = PortalRequests()
  try await requests.post(
    url,
    andPayload: [
      "backupMethod": withMethod.rawValue,
      "encryptedClientBackupShare": encryptedClientBackupShare,
    ]
  )

  // Call the storageCallback to notify Portal you stored the user backup share successfully.
  try await storageCallback()
  
  // ✅ The user has now backed up with their iCloud successfully
```
{% endtab %}
{% endtabs %}

### Google Drive

See the docs on how to configure [gdrive.md](../../resources/backup-options/gdrive.md "mention").

After initializing Portal, you will need to configure the following:

```swift
  // Set GDrive Configuration
  try portal.setGDriveConfiguration(
        clientId: "your-google-client-id",
        backupOption: .appDataFolder
  )
    
  // Set the GDrive presenting view
  try portal.setGDriveView(self)
```

`setGDriveConfiguration` function parameters:

* **clientId**: The client ID for the Google Drive integration.
* **backupOption**: An option from the `GDriveBackupOption` enum that specifies the backup storage type:
  * `appDataFolder`: Stores backups in the hidden, app-specific "App Data Folder" in Google Drive. This folder is not visible to the user.&#x20;
  * `appDataFolderWithFallback`: Attempts to store backups and recover using the "App Data Folder". If recover fails, it automatically falls back to a user-visible Google Drive.
  * `gdriveFolder(folderName: String)`: Stores backups in a user-visible folder in Google Drive with the specified `folderName`.&#x20;

`setGDriveView` function parameters:

* **view**: A `UIViewController` instance that will be used to present Google Drive UI components.

For the `GoogleDrive` action handling:

{% tabs %}
{% tab title="Portal-Managed Backups" %}
```swift
  // Run backup.
  let (
    encryptedClientBackupShare,
    storageCallback
  ) = try await portal.backupWallet(.GoogleDrive) { status in
    // (Optional) Create a progress indicator here in the progress callback.
  }
```
{% endtab %}

{% tab title="Self-Managed Backups" %}
```swift
// Your API URL
  let yourApiUrl: String = "https://YOUR_API_URL.com"
    
  // Set the user's password.
  try portal.setPassword("THE-USER-PASSWORD")
      
  // Run backup.
  let (
    encryptedClientBackupShare,
    storageCallback
  ) = try await portal.backupWallet(.GoogleDrive) { status in
    // (Optional) Create a progress indicator here in the progress callback.
  }
      
  // Obtain your API's URL for storing the encrypted user backup share.
  guard let url = URL(string: "\(yourApiUrl)/users/\(userId)/store-encrypted-user-backup-share") else {
    throw URLError(.badURL)
  }

  // Store the encrypted user backup share on your API.
  let requests = PortalRequests()
  try await requests.post(
    url,
    andPayload: [
      "backupMethod": withMethod.rawValue,
      "encryptedClientBackupShare": encryptedClientBackupShare,
    ]
  )

  // Call the storageCallback to notify Portal you stored the user backup share successfully.
  try await storageCallback()
  
  // ✅ The user has now backed up with their Google Drive successfully
```
{% endtab %}
{% endtabs %}

#### App Data Folder vs. GDrive Files

* **App Data Folder**: A hidden, app-specific storage area that is not visible to the user in their Google Drive interface. This is ideal for sensitive data or configurations that the user doesn't need to manage directly.
* **Google Drive Files**: A visible folder in the user's Google Drive, accessible and manageable by the user. This is suitable for backups the user might want to view, share, or organize manually.

{% hint style="danger" %}
The App Data Folder feature is supported starting from SDK version 4.2.0. If you enable this feature, ensure that you do not use any SDK version older than 4.2.0, as it will result in the loss of App Data Folder backups.
{% endhint %}

**Related Documentation**

* [Backup options](https://docs.portalhq.io/resources/backup-options)
* [registerBackupMethod function reference](https://docs.portalhq.io/reference/ios/registerbackupmethod)
* [setGDriveConfiguration function reference](https://docs.portalhq.io/reference/ios/setgdriveconfiguration)
* [setPasskeyConfiguration function reference](https://docs.portalhq.io/reference/ios/setpasskeyconfiguration)
* [setPasskeyAuthenticationAnchor function reference](https://docs.portalhq.io/reference/ios/setpasskeyauthenticationanchor)
* [setPassword function reference](https://docs.portalhq.io/reference/ios/setpassword)
* [isWalletBackedUp function reference](https://docs.portalhq.io/reference/ios/iswalletbackedup)

