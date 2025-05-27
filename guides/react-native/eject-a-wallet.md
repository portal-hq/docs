---
description: >-
  The eject feature allows a user to construct a private key that can be
  imported into another wallet manager, such as MetaMask.
---

# Eject a wallet

{% hint style="danger" %}
**Warning:** Providing the custodian backup share to the client device puts both MPC shares on a single device, removing the multi-party security benefits of MPC. This operation should only be done for users who want to move off of MPC and into a single private key. **Use `portal.eject()` at your own risk!**
{% endhint %}

## Ejecting Your User's Wallets

To eject the private keys for your users' wallets, their two matching backup shares need to be combined. This requires providing the following two shares to `portal.ejectPrivateKeys()`:

* **User Backup Share** - Encrypted backup share received from `portal.backup()`.
* **Custodian Backup Share** - Raw backup share received from the `POST /backup` webhook.

Here's an example implementation:

```typescript
// Create a backup config for portal.ejectPrivateKeys().
const backupConfig = {
  passwordStorage: {
    password: 'USERS_PASSWORD',
  }
}

// Fetch the user's backup share from your API.
const userBackupShares = await fetchUserBackupShare(userId)

// Fetch the custodian backup share for the user from your API.
const custodianBackupShares = await fetchCustodianBackupShare(userId)

// Obtain the private key for the user's SECP256K1 and ED25519 wallets.
const privateKeys = await portal.mpc.ejectPrivateKeys(
    userBackupShares, 
    BackupMethods.password, 
    backupConfig,
    custodianBackupShares,
)
```

{% hint style="info" %}
**`SECP256K1`** is the curve used by Ethereum and **ED25519** is the curve used by Solana.

You can learn more about signing algorithms and curves [here](http://ethanfast.com/top-crypto.html).
{% endhint %}

And that's it! Now your users can obtain a private key to import their wallet into another wallet manager.
