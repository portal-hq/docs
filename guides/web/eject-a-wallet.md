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

### With Portal-managed backups

To eject the private keys for your users' wallets, their two matching backup shares need to be combined. Since both of these shares are stored on the Portal backend encrypted at rest, we first need to verify that the upcoming request to eject the wallet is not an attack. In order to do this, we make this a two-step process:

1. Make a Custodian-authed `POST` request using your Custodian API Key (the same API key you use to create new users) to the `/api/v3/custodians/me/clients/${clientId}/prepare-eject` endpoint to allow for this client to perform an `eject` operation
2. Call the `portal.ejectWallet()` function from your application

#### Making the \`prepare-eject\` request

This endpoint requires a `walletId` in the `POST` body. This can be found in the `wallets` property of the `/api/custodians/me/clients/${clientId}` `GET` request.

{% hint style="info" %}
If you are planning on ejecting multiple wallets for a user, you'll need to make multiple separate requests to "unlock" all requires wallets.
{% endhint %}

```typescript
const response = await axios.post<string>(
  `https://api.portalhq.io/api/v3/custodians/me/clients/${clientId}/prepare-eject`,
    { walletId: 'YOUR_CLIENTS_WALLET_ID' },
    { 
      headers: {
        'Authorization': `Bearer ${YOUR_CUSTODIAN_API_KEY}`
      } 
    },
)
```

#### Ejecting the wallets

```typescript
// Provide the appropriate backup method for your client's wallet
const privateKeys = await portal.eject(BackupMethod.PASSWORD, {
  passwordStorage: {
    password,
  },
})
```

This function will perform the combining of shares from our server and the cryptographic operation required to build a private key for the wallet. The return value is the string literal of the private key.

### With Backups from your server

To eject the private keys for your users' wallets, their two matching backup shares need to be combined. This requires providing the following two shares to `portal.eject()`:

* **User Backup Share** - Encrypted backup share received from `portal.backup()`.
* **Custodian Backup Share** - Raw backup share received from the `POST /backup` webhook.

Note that a user can have multiple wallets (unique by curve). This means when the user calls `portal.eject()`, they can potentially receive multiple private keys. Here's an example implementation:

```typescript
import { BackupMethods } from '@portal-hq/web'
import { BackupConfigs } from '@portal-hq/web/types'

// Create a backup config for portal.eject().
const backupConfig: BackupConfigs = {
  passwordStorage: {
    password: 'USERS_PASSWORD',
  }
}

// Fetch the user's backup share from your API.
const userBackupShare = await fetchUserBackupShare(userId)

// Fetch the custodian backup share for the user from your API.
const custodianBackupShare = await fetchCustodianBackupShare(userId)

// Obtain the private keys for the user's various wallets.
const { SECP256K1, ED25519 } = await portal.eject(
    userBackupShare, 
    BackupMethods.password, 
    backupConfig, 
    custodianBackupShare,
)
```

{% hint style="info" %}
**`SECP256K1`** is the curve used by Ethereum.

**`ED25519`** is the curve used by Solana.

You can learn more about signing algorithms and curves [here](http://ethanfast.com/top-crypto.html).
{% endhint %}

And that's it! Now your users can obtain a private key to import their wallet into another wallet manager.
