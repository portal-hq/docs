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

Your Portal clients can create a passkey to authenticate and manage the private encryption key within a secure enclave.&#x20;

#### Implementation Requirements

1. Initialize the `Portal` class with a passkey object. &#x20;
2. Call backup with the Passkey backup method argument.

{% tabs %}
{% tab title="Portal-Managed Backups (Default)" %}
```typescript
const BackupButton: React.FC = () => {
  const handleBackup = async () => {
    // Create a passkey backup of the wallet.
    await portal.backupWallet(BackupMethods.Passkey)
  }
  
  return (
    <button onClick={handleBackup}>Back up your wallet</button>
  )
}

export default BackupButton
```
{% endtab %}

{% tab title="Self-Managed Backups" %}
```typescript
const BackupButton: React.FC = () => {
  const handleBackup = async () => {
    // Get an encryped user backup share from running backup.
    const cipherText = await portal.backupWallet(BackupMethods.Passkey)
    
    try {
      // Send the backup share to your API and store it.
      await axios.post('{your_server}/users/[userId]/user-backup-share', {
        data: { backupMethod: "PASSKEY", cipherText }
      })

      // ✅ Notify Portal that the user backup share was stored! 🙌
      await portal.storedClientBackupShare(true, "PASSKEY")
    } catch (error) {
      // ❌ Notify Portal that the user backup share was not stored.
      await portal.storedClientBackupShare(false, "PASSKEY")
    }
  }
  
  return (
    <button onClick={handleBackup}>Back up your wallet</button>
  )
}

export default BackupButton
```
{% endtab %}
{% endtabs %}

### Password/PIN

Your Portal clients can create a password/PIN. They can either remember the password or store it in a password storage manager.

#### Implementation Requirements

1. Create a UI for password input.&#x20;
2. Enforce password requirements. Customer can choose between password, PIN code, passcode, or any other text-based input.
3. If the user forgets their password, there are no additional recovery options.

{% tabs %}
{% tab title="Portal-Managed Backups" %}
```typescript
import axios from 'axios'
import React, { FC } from 'react'
import { BackupMethods, usePortal } from '@portal-hq/core'

const BackupButton: FC = () => {
  const [password, setPassword] = useState<string>('')
  const portal = usePortal()
  
  const handleBackup = async () => {
    // Create a password backup for the wallet.
    await portal.backupWallet(BackupMethods.Password, { passwordStorageConfig: { pasword } })
  }
  
  return (
    <div>
      <input
        onChange={setPassword}
        placeholder="Password/Pin"
        type="password"
        value={password}
      />
      <button onClick={handleBackup}>Back up your wallet</button>
    </div>
  )
}

export default BackupButton
```
{% endtab %}

{% tab title="Self-Managed Backups" %}
```typescript
import axios from 'axios'
import React, { FC } from 'react'
import { BackupMethods, usePortal } from '@portal-hq/core'

const BackupButton: FC = () => {
  const [password, setPassword] = useState<string>('')
  const portal = usePortal()
  
  const handleBackup = async () => {
    // Get an encryped client backup share from running backup.
    const cipherText = await portal.backupWallet(BackupMethods.Password, { passwordStorageConfig: { pasword } })
    
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
    <div>
      <input
        onChange={setPassword}
        placeholder="Password/Pin"
        type="password"
        value={password}
      />
      <button onClick={handleBackup}>Back up your wallet</button>
    </div>
  )
}

export default BackupButton
```
{% endtab %}
{% endtabs %}

### Google Drive

See the docs on how to [gdrive.md](../../resources/backup-options/gdrive.md "mention").
