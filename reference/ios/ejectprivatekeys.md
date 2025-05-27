---
description: Extracts all private keys from the wallet using a specified backup method.
---

# ejectPrivateKeys

**Function Signature**

```swift
public func ejectPrivateKeys(
    _ method: BackupMethods,
    withCipherText: String? = nil,
    andOrganizationBackupShare: String? = nil,
    andOrganizationSolanaBackupShare: String? = nil
) async throws -> [PortalNamespace: String]
```

**Parameters**

* `method`: The backup method to use. Supported methods:
  * `.GoogleDrive`: Authenticate using Google Drive backup
  * `.iCloud`: Authenticate using iCloud backup
  * `.Password`: Authenticate using password
  * `.Passkey`: Authenticate using passkey
  * `.local`: Authenticate using local backup
* `withCipherText`: Optional cipher text from a previous backup
* `andOrganizationBackupShare`: Optional backup share provided by the organization for EVM chains
* `andOrganizationSolanaBackupShare`: Optional backup share provided by the organization specifically for Solana

**Returns**

A dictionary mapping `PortalNamespace` to private keys, where:

* `.eip155` key contains the Ethereum/EVM private key
* `.solana` key contains the Solana private key

**Throws**

Various backup method-specific errors if authentication fails

**Warning**

{% hint style="danger" %}
Providing the custodian backup share to the client device puts both MPC shares on a single device, removing the multi-party security benefits of MPC. This operation should only be done for users who want to move off of MPC and into a single private key. **Use `portal.ejectPrivateKeys()` at your own risk!**
{% endhint %}

**Example Usage**

```swift
do {
    let privateKeys = try await portal.ejectPrivateKeys(
        .iCloud,
        withCipherText: backupCipherText,
        andOrganizationBackupShare: orgBackupShare,
        andOrganizationSolanaBackupShare: orgSolanaBackupShare
    )
    
    if let ethPrivateKey = privateKeys[.eip155] {
        print("Ethereum private key: \(ethPrivateKey)")
    }
    
    if let solanaPrivateKey = privateKeys[.solana] {
        print("Solana private key: \(solanaPrivateKey)")
    }
} catch {
    print("Error ejecting private keys: \(error)")
}
```
