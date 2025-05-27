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
2. Call the `portal.ejectPrivateKey()` function from your application

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

```kotlin

val privateKey = portal.ejectPrivateKey(
    null, // No input backup shares are required
    null, // No input backup shares are required
    
    // Provide the appropriate backup method for your client's wallet
    backupMethod = BackupMethods.Password,
    backupConfigs = backupConfigs,
)
```

This function will perform the combining of shares from our server and the cryptographic operation required to build a private key for the wallet. The return value is the string literal of the private key.

### With Backups from your server

To eject the private keys for your users' wallets, their two matching backup shares need to be combined. This requires providing the following two shares to `portal.eject()`:

* **User Backup Share** - Encrypted backup share received from `portal.backup()`.
* **Custodian Backup Share** - Raw backup share received from the `POST /backup` webhook.
* **Custodian Backup Solana Share** - Raw backup share received from the `POST /backup` webhook.

Here's an example implementation:

```kotlin
// Create a backup config for portal.eject().
val backupConfig = BackupConfigs(PasswordStorageConfig(password = "USERS_PASSWORD"))

// Fetch the user's backup share from your API.
val userBackupShare = yourAPI.fetchUserBackupShare(userId)

// Fetch the custodian backup share for the user from your API.
val custodianBackupShare = yourAPI.fetchCustodianBackupShare(userId)

// Fetch the custodian backup share for the user from your API.
val custodianBackupSolanaShare = yourAPI.fetchCustodianBackupSolanaShare(userId)

// Obtain the private key for the user's SECP256K1 and ED25519 wallet.
val privateKeys = portal.ejectPrivateKeys(
  userBackupShare,
  custodianBackupShare,
  custodianBackupSolanaShare,
  BackupMethods.Password,
  backupConfig,
)
```

{% hint style="info" %}
**`SECP256K1`** is the curve used by Ethereum and **ED25519** is the curve used by solana.

You can learn more about signing algorithms and curves [here](http://ethanfast.com/top-crypto.html).
{% endhint %}

And that's it!&#x20;
