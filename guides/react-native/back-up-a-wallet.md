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

1. Install `react-native-passkeys` and `@portal-hq/passkey-storage`
2. Initialize passkey storage as a backup option in the Portal Config Object.&#x20;
3. Configuring the relying party

#### Relying party

A relying party is a trusted domain that is tied to the public key credentials of your users for their passkey . We offer the option to use `portalhq.io` as your relying party domain. It requires you to add `portalhq.io` as an Associated Domain in your iOS application and share your team id + application bundle id. If you already have your domain as a `webcredential` for your application then you can simply pass in your domain as the relying party and everything should work.&#x20;

#### Use Portal as your relying party

{% tabs %}
{% tab title="iOS" %}
1. Add `portalhq.io` as a web credential domain in your app.
2. Share your app bundle id and team id with the Portal Team.
{% endtab %}

{% tab title="Android" %}
1. Add `portalhq.io` as an included domain in your manifest file.
2. Share your applications SHA fingerprint and namespace with the Portal Team.
{% endtab %}
{% endtabs %}

#### Use your own relying party

{% tabs %}
{% tab title="iOS" %}
Ensure you have set up your associate domain correctly in your app and that you are serving an aasa file from your relying party domain. You will need to be sure you have the `webcredential` field set properly for your app in your aasa file.&#x20;

Resources from apple:

* [Configuring an associated domain](https://developer.apple.com/documentation/xcode/configuring-an-associated-domain#Provide-an-Apple-App-Site-Association-file)
* [Supporting associated domains](https://developer.apple.com/documentation/xcode/supporting-associated-domains#Add-the-associated-domain-file-to-your-website)
{% endtab %}

{% tab title="Android" %}
Ensure you have set up your digital asset links url correctly in your app and that you are serving a digital asset links file from the URL you specified in the asset statement in your manifest file.

Read more about setting this up from the android docs: [Configuring your Digital Asset Links file](https://developer.android.com/training/sign-in/passkeys#add-support-dal)

### Relying Party Origins

Regardless of the relying party decision you make above, you will need to set the relying party origin to:&#x20;

```
android:apk-key-hash:<sha256_hash-of-apk-signing-cert>
```

_read more info_ [_here_](https://developer.android.com/training/sign-in/passkeys#verify-origin) _on how to get the SHA256 hash of the apk signing cert._
{% endtab %}
{% endtabs %}

### Password/PIN

Allow users to create a password/pin. Users can either remember the password or store it in a password storage manager.

#### Implementation Requirements

1. Create a UI for password input.&#x20;
2. Enforce password requirements. Customer can choose between password, PIN code, passcode, or any other text-based input.
3. If user forgets password there are no additional recovery options.

```typescript
import axios from 'axios'
import React, { FC } from 'react'
import { BackupMethods, usePortal } from '@portal-hq/core'
import { Button, View } from 'react-native'

const BackupButton: FC = () => {
  const [password, setPassword] = useState<string>('')
  const portal = usePortal()
  
  const handleBackup = async () => {
    // Get an encryped client backup share from running backup.
    const cipherText = await portal.backupWallet(
      BackupMethods.Password,
      (status) => { console.log('Backup Status: ', status) },
      { passwordStorage: { password: password } },
    )
    
    try {
      // Send the backup share to your API and store it.
      await axios.post('{your_server}/users/[userId]/user-backup-shares', {
        data: { backupMethod: "PASSWORD", cipherText }
      })

      // ✅ Notify Portal that the user backup share was stored! 🙌
      await portal.api.storedClientBackupShare(true, "PASSWORD")
    } catch (error) {
      // ❌ Notify Portal that the user backup share was not stored.
      await portal.api.storedClientBackupShare(false, "PASSWORD")
    }
  }
  
  return (
    <View>
        <TextInput
          autoCapitalize="none"
          autoCorrect={false}
          onChangeText={setPassword}
          placeholder="Password/Pin"
          placeholderTextColor="#6B6E73"
          spellCheck={false}
          style={styles.input}
          value={password}
        />
      <Button onPress={handleBackup} title="Backup your wallet" />
    </View>
  )
}

export default BackupButton
```

### iCloud

See the docs on how to [icloud.md](../../resources/backup-options/icloud.md "mention").

### Google Drive

See the docs on how to [gdrive.md](../../resources/backup-options/gdrive.md "mention").
