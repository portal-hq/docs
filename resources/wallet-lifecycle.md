---
description: >-
  A Portal Wallet keeps track of the status of its signing shares and backup
  shares.
---

# Wallet lifecycle

There are 4 core MPC operations that can take place with your wallet:

* Generate
* Backup
* Recover
* Sign

The first 3 (Generate, Backup, Recover) are all _state changing_ operations that affect either the set of **signing shares** or **backup shares**. Let's explore the details of each one.

## **Generate**

Creates new signing shares from randomly generated values.

{% hint style="info" %}
This only affects **signing shares.**
{% endhint %}

### Steps

When you call `portal.createWallet()` two steps happen under the hood.

1. **Create Signing Shares -** After this completes the custodian signing share will have been successfully stored in Portal's database and the client signing share is stored on the Portal client's device. This updates the signing share status to `STORED_DATABASE`.
2. **Store Client Signing Share to Keychain -** After the WebSocket successfully closes the client _signing_ share is written to the device keychain and the SDK notifies the server that it succeeded. This updates the _signing_ shares status to `STORED_CLIENT`.

## **Backup**

Creates new backup shares using the signing shares.

{% hint style="info" %}
This only affects **backup shares.**
{% endhint %}

### Steps

These are the steps of what happens when you call `portal.backupWallet()`.

1. **Create Backup Shares -** After this completes the custodian backup share is saved in Portal's backend (if you are using [Self-Managed Backups](self-managed-backups.md), the custodian backup share is then saved in your backend via the configured webhook). This updates the backup share pair's status to `STORED_CUSTODIAN_BACKUP_SHARE`.
2. **Encrypt Client Backup Share** - The client backup share is encrypted on the client-side and the encryption key is uploaded to the client's cloud storage.
3. **Store Client Backup Share** - By default, Portal will store the encrypted client backup share for you. (If you are using Self-Managed Backups, you are required to store the encrypted client backup share and, if successful, call `portal.api.storedClientBackupShare()` to notify our backend that the client backup share was successfully stored.) This updates the backup shares status to `STORED_CLIENT_BACKUP_SHARE`.

{% hint style="danger" %}
If backup fails you are expected to handle the error (see the [Error Reference](error-codes/)) and re-run backup after handling it. Recover cannot be run until Backup succeeds.
{% endhint %}

## **Recover**

Creates new signing shares using the backup shares.&#x20;

{% hint style="info" %}
This only affects **signing shares.**
{% endhint %}

### Steps

These are the steps of what happens when you call `portal.recoverWallet()`.

1. **Create Signing Shares -** After this completes the custodian signing share will have been successfully updated in Portal's database and the client signing share is available on the client's device.
2. **Store Client Signing Share to Keychain -** After the WebSocket successfully closes the client signing share is written to the client's device's keychain. The SDK notifies the server that it succeeded. This updates the signing shares status to `STORED_CLIENT`.

{% hint style="danger" %}
If recovery fails you are expected to handle the error (see the [Error Reference](error-codes/)) and re-run recovery after handling it. Signing functionality will not work until a successful recover on a device that does not have a signing share in its keychain.
{% endhint %}
