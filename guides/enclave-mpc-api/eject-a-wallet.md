---
description: >-
  The eject feature allows a user to construct a private key that can be
  imported into another wallet manager, such as MetaMask.
---

# Eject a wallet

{% hint style="danger" %}
**Warning:** Providing the custodian backup share to the client device puts both MPC shares on a single device, removing the multi-party security benefits of MPC. This operation should only be done for users who want to move off of MPC and into a single private key.&#x20;
{% endhint %}

## Ejecting a Wallet

To eject the private keys for your wallets, the two matching backup shares need to be combined. This is done by fetching the backup shares and then inputing them into the eject function in the [@portal-hq/eject-js](https://github.com/portal-hq/eject-js#readme) NPM package.

### With Portal-Managed Backups

To eject the private keys for your wallet, the two matching backup shares need to be combined.

* **Client Backup Shares** - The client backup shares that are encrypted and stored with Portal.
* **Custodian Backup Shares** - The custodian backup shares that are encrypted and stored with Portal.

The general steps here are to:

1. Get the Portal client's details and derive their SECP256K1 and ED25519 wallets.
2. Retrieve the Portal client's client backup shares and custodian backup shares.
3. Eject the Portal client's wallets and get their private keys.

Below is an example of how you might do this using TypeScript.

```typescript
import axios from 'axios'
import eject from '@portal-hq/eject-js'

const clientId = 'example-client-id'

// Get the Portal client.
const { data: clientDetails } = await axios.get(`https://api.portalhq.io/api/v3/custodians/me/clients/${clientId}`)

// Get the Portal client's wallets.
const secp256k1Wallet = clientDetails.wallets.find((wallet) => wallet.curve === 'SECP256K1')
const ed25519Wallet = clientDetails.wallets.find((wallet) => wallet.curve === 'ED25519')

// Get the raw custodian backup shares.
const secp256k1Shares = await axios.get(`https://api.portalhq.io/api/v3/custodians/me/clients/${clientId}/wallets/${secp256k1Wallet.id}/ejectable-backup-shares`)
const ed25519Shares = await axios.get(`https://api.portalhq.io/api/v3/custodians/me/clients/${clientId}/wallets/${secp256k1Wallet.id}/ejectable-backup-shares`)

// Retrieve the client backup shares from your backend stored during backup.
const secp256k1ClientShare = await retrieveSecp256k1ClientBackupShare(clientId)
const ed25519ClientShare = await retrieveEd25519ClientBackupShare(clientId)

// Derive the custodian backup shares.
const secp256k1CustodianShare = secp256k1Shares.custodianBackupShare
const ed25519CustodianShare = ed25519Shares.custodianBackupShare

// Recover the SECP256K1 private key (EVM).
const secp256k1PrivateKey = await eject.recoverSecp256k1Key(secp256k1ClientShare, secp256k1CustodianShare)

// Recover the ED25519 private key (Solana)
const ed25519PrivateKey = await eject.recoverEd25519Key(ed25519ClientShare, ed25519CustodianShare)
```

{% hint style="info" %}
**`SECP256K1`** is the curve used by Ethereum and **ED25519** is the curve used by Solana.

You can learn more about signing algorithms and curves [here](http://ethanfast.com/top-crypto.html).
{% endhint %}

### With Self-Managed Backups

To eject the private keys for your wallet, the two matching backup shares need to be combined.

* **Client Backup Shares** - The client backup shares that you stored on your backend.
* **Custodian Backup Shares** - The custodian backup shares that you stored on your backend.

```javascript
import eject from '@portal-hq/eject-js'

// Fetch the client backup shares from your backend stored during backup.
const secp256k1ClientShare = await fetchSecp256k1ClientBackupShare()
const ed25519ClientShare = await fetchEd25519ClientBackupShare()

// Fetch the custodian backup share for the user from your API stored during backup.
const secp256k1CustodianShare = await fetchSecp256k1CustodianBackupShare()
const ed25519CustodianShare = await fetchSecp256k1CustodianBackupShare()

// Recover a secp256k1 private key (EVM)
const secp256k1PrivateKey = await eject.recoverSecp256k1Key(secp256k1ClientShare, secp256k1CustodianShare)

// Recover an ed25519 private key (Solana)
const ed25519PrivateKey = await eject.recoverEd25519Key(ed25519ClientShare, ed25519CustodianShare)
```

{% hint style="info" %}
**`SECP256K1`** is the curve used by Ethereum and **ED25519** is the curve used by Solana.

You can learn more about signing algorithms and curves [here](http://ethanfast.com/top-crypto.html).
{% endhint %}

And that's it!&#x20;
