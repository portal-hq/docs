---
description: >-
  The eject feature allows a user to construct private keys that can be imported
  into another wallet manager, such as MetaMask.
---

# Eject a wallet

{% hint style="danger" %}
**Warning:** Providing the custodian backup share to the client device puts both MPC shares on a single device, removing the multi-party security benefits of MPC. This operation should only be done for users who want to move off of MPC and into a single private key. **Use `portal.eject()` at your own risk!**
{% endhint %}

### Ejecting Methods

You can eject wallets using either Portal-Managed Backups or Self-Managed Backups depending on your implementation.

### Portal-managed backups

To eject the private keys for your users' wallets, their two matching backup shares need to be combined. Since both of these shares are stored on the Portal backend encrypted at rest, we first need to verify that the upcoming request to eject the wallet is not an attack. In order to do this, we make this a two-step process:

**Implementation Requirements**

1. Make a Custodian-authed `POST` request using your Custodian API Key (the same API key you use to create new users) to the `/api/v3/custodians/me/clients/\(clientId)/prepare-eject` endpoint to allow for this client to perform an `eject` operation
2. Call the `portal.eject()` or `portal.ejectPrivateKeys()` function from your client application with the appropriate backup method

#### **Step 1: Prepare the Eject Operation**

Make a custodian-authenticated `POST` request using your Custodian API Key to authorize the ejection:

{% tabs %}
{% tab title="ETH & Solana" %}
```swift
guard let prepareEjectUrl = URL(string: "https://api.portalhq.io/api/v3/custodians/me/clients/\(clientId)/prepare-eject") else {
  throw URLError(.badURL)
}

// Prepare ETH wallet
let prepareEjectData = try await requests.post(
  prepareEjectUrl,
  withBearerToken: nil,
  andPayload: ["walletId": EthWalletId]
)

guard let prepareEthEjectResponse = String(data: prepareEjectData, encoding: .utf8) else {
  throw PortalExampleAppError.couldNotParseCustodianResponse("Unable to read prepare eject response.")
}

print("ETH Wallet ejectable until \(prepareEthEjectResponse)")

// Prepare Solana wallet
let prepareEjectData = try await requests.post(
  prepareEjectUrl,
  withBearerToken: nil,
  andPayload: ["walletId": SolanaWalletId]
)

guard let prepareEjectResponseEd25519 = String(data: prepareEjectData, encoding: .utf8) else {
  throw PortalExampleAppError.couldNotParseCustodianResponse("Unable to read prepare eject response.")
}

print("Solana Wallet ejectable until \(prepareEjectResponseEd25519)")
```
{% endtab %}

{% tab title="ETH" %}
```swift
guard let prepareEjectUrl = URL(string: "https://api.portalhq.io/api/v3/custodians/me/clients/\(clientId)/prepare-eject") else {
  throw URLError(.badURL)
}

let prepareEjectData = try await requests.post(
  prepareEjectUrl,
  withBearerToken: nil,
  andPayload: ["walletId": walletId]
)

guard let prepareEjectResponse = String(data: prepareEjectData, encoding: .utf8) else {
  throw PortalExampleAppError.couldNotParseCustodianResponse("Unable to read prepare eject response.")
}

print("ETH Wallet ejectable until \(prepareEjectResponse)")
```
{% endtab %}
{% endtabs %}

{% hint style="info" %}
**Note:** The `walletId` can be found in the `wallets` property of the `/api/custodians/me/clients/\(clientId)` `GET` request.
{% endhint %}

{% hint style="info" %}
If you are planning on ejecting multiple wallets for a user, you'll need to make multiple separate requests to "unlock" all requires wallets.
{% endhint %}

#### Step 2: Eject the Wallet

After preparation, call the `eject()` or `ejectPrivateKeys()` method with the appropriate backup method that was originally used to secure the wallet:

{% tabs %}
{% tab title="ETH & Solana" %}
```swift
// For Password backup method
let privateKey = try await portal.ejectPrivateKeys(.Password)

// For Passkey backup method
let privateKey = try await portal.ejectPrivateKeys(.Passkey)

// For iCloud backup method
let privateKey = try await portal.ejectPrivateKeys(.iCloud)

// For Google Drive backup method
let privateKey = try await portal.ejectPrivateKeys(.GoogleDrive)
```
{% endtab %}

{% tab title="ETH" %}
```swift
// For Password backup method
let privateKey = try await portal.eject(.Password)

// For Passkey backup method
let privateKey = try await portal.eject(.Passkey)

// For iCloud backup method
let privateKey = try await portal.eject(.iCloud)

// For Google Drive backup method
let privateKey = try await portal.eject(.GoogleDrive)
```
{% endtab %}
{% endtabs %}

Those functions will:

* Retrieve and combine shares from Portal's server
* Perform the cryptographic operations to construct the private key/s
* Return the private key/s as a string that can be imported into other wallet managers

### Self-Managed Backup (from your server)

When using Self-Managed Backups, you store the backup shares in your own infrastructure. To eject a wallet, you'll need to provide both backup shares to the `portal.eject()` or `portal.ejectPrivateKeys()` function.

* **User Backup Share** - Encrypted backup share received from `portal.backup()`.
* **Custodian Backup Share** - Raw backup share received from the `POST /backup` webhook.

**Implementation Requirements**

1. Retrieve the encrypted user backup share from your storage
2. Retrieve the raw custodian backup share/s from your storage
3. Call the eject function with both shares

Example Implementation:

{% tabs %}
{% tab title="ETH & Solana" %}
```swift
// Fetch the encrypted user backup share from your API.
let encryptedUserBackupShare = try await yourAPI.fetchEncryptedUserBackupShare(
    userId,
    backupMethod: .Password
)

// Fetch the user's custodian backup share for Ethereum (SECP256k1)
let custodianBackupShare = try await yourAPI.fetchCustodianBackupShare(
    userId,
    backupMethod: .Password
)

// Fetch the user's custodian backup share for Solana (ED25519)
let custodianSolanaBackupShare = try await yourAPI.fetchCustodianSolanaBackupShare(
  userId,
  backupMethod: .Password
)

let privateKeys = try await portal.ejectPrivateKeys(
  .Password,
  withCipherText: cipherText,
  andOrganizationBackupShare: custodianBackupShare,
  andOrganizationSolanaBackupShare: custodianSolanaBackupShare
)

// ✅ ETH & Solana private keys are now available for export
print("ETH & Solana private keys: \(privateKeys)")
```
{% endtab %}

{% tab title="ETH" %}
```swift
// Fetch the encrypted user backup share from your API.
let encryptedUserBackupShare = try await yourAPI.fetchEncryptedUserBackupShare(
    userId,
    backupMethod: .Password
)

// Fetch the user's custodian backup share from your API.
let custodianBackupShare = try await yourAPI.fetchCustodianBackupShare(
    userId,
    backupMethod: .Password
)
   
let privateKey = try await portal.eject(
  .Password,
  withCipherText: cipherText,
  andOrganizationBackupShare: custodianBackupShare
)

// ✅ The ETH private key is now available for export
print("ETH private key: \(privateKey)")
```
{% endtab %}
{% endtabs %}



### Cryptographic Curves

{% hint style="info" %}
Portal supports multiple blockchain networks that use different cryptographic curves:

* **SECP256K1**: Used by Ethereum and most EVM-compatible blockchains
* **ED25519**: Used by Solana and several other blockchains

You can learn more about signing algorithms and curves [here](http://ethanfast.com/top-crypto.html).
{% endhint %}

When ejecting wallets, make sure to provide the appropriate backup shares for each curve your application supports.

### Best Practices

* Implement clear user confirmations before initiating the eject process
* Securely transmit and display private keys to users
* Educate users about the security implications of moving from MPC to single key wallets
* Consider implementing a cooling-off period before allowing wallet ejection

And that's it! Now your users can obtain a private key to import their wallet into another wallet manager.

**Related Documentation**

* [Backup options](https://docs.portalhq.io/resources/backup-options)
* [ejectPrivateKeys function reference](../../reference/ios/ejectprivatekeys.md)
