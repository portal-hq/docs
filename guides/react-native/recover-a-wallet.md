---
description: >-
  This guide will walk you through how to use your users' backups to recover
  their wallet.
---

# Recover a wallet

## Portal-Managed Recovery (default)

When using Portal-Managed Backups, you can simply call the `portal.recoverWallet()` function passing an empty string for the `cipherText` parameter. Portal will then fetch the `cipherText` from our backend and complete the recovery process.

```typescript
// With Password
await portal.recoverWallet("", BackupMethods.Password, undefined, { passwordStorage: { password: password } })

// With Google Drive
await portal.recoverWallet("", BackupMethods.GoogleDrive)

// With iCloud
await portal.recoverWallet("", BackupMethods.iCloud)

// With Passkeys
await portal.recoverWallet("", BackupMethods.Passkey)
```

{% hint style="danger" %}
**WARNING**: To recover a wallet with the Portal SDK, your device must be configured to use passcode authentication. Please note that if you disable your passcode authentication after executing the `recover` function, you will need to run the `recover` function again.
{% endhint %}

## Self-Managed Recovery

By default, Portal manages storing both the encrypted client backup share and the custodian backup share for you. If you prefer to store and manage the backup shares in your own infrastructure instead of using Portal-Managed Backups, see our [Self-Managed Backups](../../resources/self-managed-backups.md) guide.

Before recovering, you will need to retrieve the encrypted client backup share from your API. You will then provide the encrypted client backup share to `portal.recoverWallet`. Here's an example of how that might look in your code:

{% hint style="info" %}
Ensure you have created and connected the UI element in the storyboard to the action.
{% endhint %}

```typescript
// Get the encrypted user backup share from your API.
const encryptedClientBackupShare = await axios.get('/users/[userId]/user-backup-share?backupMethod=GDRIVE')

// Recover replaces your current signing shares (if there are any) with new ones.
await portal.recoverWallet(encryptedClientBackupShare, BackupMethods.Password, undefined, { passwordStorage: { password: password } })
```

{% hint style="danger" %}
**WARNING**: To recover a wallet with the Portal SDK, your device must be configured to use passcode authentication. Please note that if you disable your passcode authentication after executing the `recover` function, you will need to run the `recover` function again.
{% endhint %}

## Progress Callbacks

You can learn how to handle the progress callbacks for `portal.recoverWallet` [here](mpc-progress-callbacks.md).

## Next steps

Amazing! Your users can now easily recover their wallet. Next let's dive into handling sessions across multiple devices for your users.
