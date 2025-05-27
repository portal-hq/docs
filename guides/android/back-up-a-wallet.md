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

### Passkey + Enclave Backup

Allow customers to create a native passkey on their device that is used to authenticate into a secure enclave that holds the encryption key for the user. Customer's passkeys are backed up to the native cloud storage for their device.&#x20;

#### Implementation Requirements

1. Initialize passkey storage as a backup option in the Portal Config Object with the correct origin&#x20;
2. Configuring the relying party

#### Relying Party Configuration

You have two options when it comes to relying party configurations. It is important that you understand the implications of this decision. A relying party is a trusted domain that is tied to the public key credentials of your users for their passkey. &#x20;

#### Use Portal as your relying party&#x20;

We offer the option to use `portalhq.io` as your relying party domain. It requires you to add `portalhq.io` as a trusted domain in your manifest file in your android application and share your applications SHA fingerprint and namespace with our team. We will add them to our own digital asset links file and that will allow your app to set `portalhq.io` as the relying party.&#x20;

1. Add `portalhq.io` as an included domain in your manifest file.
2. Share your applications SHA fingerprint and namespace with the Portal Team.

#### Use your own relying party

Ensure you have set up your digital asset links url correctly in your app and that you are serving a digital asset links file from the URL you specified in the asset statement in your manifest file.

Read more about setting this up from the android docs: [Configuring your Digital Asset Links file](https://developer.android.com/training/sign-in/passkeys#add-support-dal)

#### Relying Party Origins

Regardless of the relying party decision you make above, you will need to set the relying party origin to:&#x20;

```
android:apk-key-hash:<sha256_hash-of-apk-signing-cert>
```

_read more info_ [_here_](https://developer.android.com/training/sign-in/passkeys#verify-origin) _on how to get the SHA256 hash of the apk signing cert._

#### Example

Initializing the passkey storage class&#x20;

{% tabs %}
{% tab title="5.x.x" %}
```kotlin
portal.configurePasskeyStorage(
    relyingPartyId = "portalhq.io", 
    relyingPartyOrigins = listOf("android:apk-key-hash:SHA-256-HASH")
)
```
{% endtab %}

{% tab title="3.x.x" %}
```kotlin
val passkey = PasskeyStorage(
    YOUR_ACTIVITY, 
    relyingPartyId = "portalhq.io", 
    relyingPartyOrigins = listOf("android:apk-key-hash:SHA-256-HASH")
)
```
{% endtab %}
{% endtabs %}

Calling backup

{% tabs %}
{% tab title="Portal-Managed Backups" %}
```kotlin
// Get an encryped client backup share.
val backupShare = portal.backupWallet(
    backupMethod = BackupMethods.Passkey
)  { status ->
    // (Optional) Get status updates on the backup operation
}
```
{% endtab %}

{% tab title="Self-Managed Backups" %}
```kotlin
// Get an encryped client backup share.
val backupShare = portal.backupWallet(
    backupMethod = BackupMethods.Passkey
)  { status ->
    // (Optional) Get status updates on the backup operation
}

// Send the user backup share to your API and store it.
exchangeApi.storeCipherText(user.id, backupShare, "PASSKEY")

// ✅ Notify Portal that the user backup share was stored! 🙌
portal.api.storedClientBackupShare(true, "PASSKEY")
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
```kotlin
val backupConfigs = BackupConfigs(PasswordStorageConfig(password = PASSWORD))

// Get an encryped client backup share.
val backupShare = portal.backupWallet(
    backupMethod = BackupMethods.Password, 
    backupConfigs = backupConfigs
)  { status ->
    // (Optional) Get status updates on the backup operation
}
```
{% endtab %}

{% tab title="Self-Managed Backups" %}
```kotlin
val backupConfigs = BackupConfigs(PasswordStorageConfig(password = PASSWORD))

// Get an encryped client backup share.
val backupShare = portal.backupWallet(
    backupMethod = BackupMethods.Password, 
    backupConfigs = backupConfigs
)  { status ->
    // (Optional) Get status updates on the backup operation
}

// Send the user backup share to your API and store it.
storeCipherText(user.id, backupShare, "PASSWORD")

// ✅ Notify Portal that the user backup share was stored! 🙌
portal.api.storedClientBackupShare(true, "PASSWORD")
```
{% endtab %}
{% endtabs %}

### Google Drive

See the docs on how to [gdrive.md](../../resources/backup-options/gdrive.md "mention").

#### Configuring GDrive Backup

{% tabs %}
{% tab title="5.X.X" %}
<pre class="language-kotlin"><code class="lang-kotlin"><strong>val backupOption = GDriveBackupOption.AppDataFolderWithFallback
</strong><strong>portal.configureGoogleStorage(
</strong>      GDriveConfiguration(
        clientId = gdriveClientId, // the clientId of the Web Application credential like explained above
        signOutAfterUse = true,  //if you would like to keep Google signed in then set it to false
        gDriveBackupOption = backupOption // is explained below
      )
)
</code></pre>

#### GDrive Backup Options

We provide the following backup options for GDrive in our latest _V5+_ version of the Android Portal SDK.

1. **GDriveBackupOption.CustomFolder(optionalFolderName):** Use this if you would like the key to wallet backup shares to be stored in a custom folder of your choice. This folder will be visible to the user in their GDrive app and they can delete it at any time so this option is considered less safe and we don't recommend it.
2. **GDriveBackupOption.AppDataFolder:** Use this option if you would like the key to the wallet backup shares to be stored in a private [app-specific folder](https://developers.google.com/drive/api/guides/appdata) in GDrive. This folder is not visible to the user and they can not delete the key file unless they go to the GDrive settings and delete the app from the list of the apps connected. This option is safer and is recommended.
3. **GDriveBackupOption.AppDataFolderWithFallback:** Use this option if you are coming from v4 of the SDK and would like to use the _AppDataFolder_ option in a backward-compatible way. With this option, new backup keys are stored in the [app-specific folder ](https://developers.google.com/drive/api/guides/appdata)in GDrive but when reading if the key is not found in [app-specific folder](https://developers.google.com/drive/api/guides/appdata) then the SDK also checks in the GDrive overall to see if the key is available in a custom folder. This way the old backup keys are not lost when you upgrade from v4 to v5. Use this option only when you are upgrading from v3, v4 to V5 otherwise use _AppDataFolder_ option.
{% endtab %}

{% tab title="4.X.X" %}
```kotlin
portal.configureGoogleStorage(
      clientId = gdriveClientId, // the clientId of the Web Application credential like explained above
      signOutAfterUse = true // //if you would like to keep Google signed in then set it to false
)
```
{% endtab %}
{% endtabs %}

{% hint style="info" %}
If you do not pass in a backup option into `portal.backupWallet()` we will default to `GDRIVE` backup.
{% endhint %}
