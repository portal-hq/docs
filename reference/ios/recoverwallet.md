---
description: Recovers a wallet using a specified backup method.
---

# recoverWallet

**Function Signature**

```swift
public func recoverWallet(
    _ method: BackupMethods,
    withCipherText: String? = nil,
    usingProgressCallback: ((MpcStatus) -> Void)? = nil
) async throws -> PortalRecoverWalletResponse
```

**Parameters**

* `method`: The backup method to use for recovery. Supported methods:
  * `.GoogleDrive`: Recover from Google Drive backup
  * `.iCloud`: Recover from iCloud backup
  * `.Password`: Recover using password
  * `.Passkey`: Recover using passkey
  * `.local`: Recover from local backup
* `withCipherText`: Optional cipher text from a previous backup. Required for the [Self-Managed Backups](../../resources/self-managed-backups.md).
* `usingProgressCallback`: Optional callback to track the recovery progress. Receives an `MpcStatus` object containing:
  * `status`: Current operation being performed:
    * `.readingShare`: Reading the stored share
    * `.decryptingShare`: Decrypting the share
    * `.parsingShare`: Parsing the share data
    * `.generatingShare`: Generating the share data
    * `.storingShare`: Saving the recovered share
    * `.done`: Process completed
  * `done`: Boolean indicating whether the operation is complete

**Returns**

`PortalRecoverWalletResponse` containing:

* `ethereum`: The recovered Ethereum address
* `solana`: The recovered Solana address, if available

**Throws**

* `PortalClassError.cannotRecoverWallet` if the Ethereum address cannot be recovered
* Various backup method-specific errors if recovery fails

**Example Usage**

```swift
do {
    let recoveredWallet = try await portal.recoverWallet(
        .iCloud,
        withCipherText: backupCipherText
    ) { status in
        switch status.status {
        case .readingShare:
            print("Reading backup share...")
        case .decryptingShare:
            print("Decrypting share...")
        case .parsingShare:
            print("Parsing share data...")
        case .generatingShare:
            print("Regenerating wallet...")
        case .storingShare:
            print("Saving recovered wallet...")
        case .done:
            print("Recovery completed!")
        default:
            break
        }
    }
    
    print("Recovered Ethereum address: \(recoveredWallet.ethereum)")
    if let solanaAddress = recoveredWallet.solana {
        print("Recovered Solana address: \(solanaAddress)")
    }
} catch {
    print("Error recovering wallet: \(error)")
}
```
