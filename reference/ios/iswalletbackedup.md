---
description: >-
  Checks whether a wallet has completed backups. This method verifies if backup
  shares have been successfully created and stored.
---

# isWalletBackedUp

**Function Signature**

```swift
public func isWalletBackedUp(
    _ forChainId: String? = nil
) async throws -> Bool
```

**Parameters**

* `forChainId`: Optional chain identifier (e.g., "eip155:1" for Ethereum mainnet, "solana:5eykt4UsFv8P8NJdTREpY1vzqKqZKvdp" for Solana mainnet). If nil, checks backup status across all chains.

**Returns**

A boolean value indicating whether the wallet is backed up:

* When forChainId is provided:
  * Returns `true` if a wallet exists for the specified chain and has at least one completed backup share
  * Returns `false` if no wallet exists or if no backup shares are completed
* When forChainId is `nil`:
  * Returns `true` if any wallet exists and has at least one completed backup share
  * Returns `false` if no wallets exist or if no backup shares are completed

**Throws**

* `PortalClassError.clientNotAvailable` if the client is not initialized

**Example Usage**

```swift
// Check backup status for all wallets
do {
    let isBackedUp = try await portal.isWalletBackedUp()
    if isBackedUp {
        print("At least one wallet is backed up")
    } else {
        print("No wallets are backed up")
    }
} catch {
    print("Error checking backup status: \(error)")
}

// Check backup status for specific chains
do {
    // Check Ethereum wallet backup
    let ethBackedUp = try await portal.isWalletBackedUp("eip155:1")
    if ethBackedUp {
        print("Ethereum wallet is backed up")
    }
    
    // Check Solana wallet backup
    let solBackedUp = try await portal.isWalletBackedUp("solana:5eykt4UsFv8P8NJdTREpY1vzqKqZKvdp")
    if solBackedUp {
        print("Solana wallet is backed up")
    }
} catch {
    print("Error checking chain-specific backups: \(error)")
}

// Complete example with backup flow
do {
    let isBackedUp = try await portal.isWalletBackedUp()
    if !isBackedUp {
        // Perform backup if not already done
        let backup = try await portal.backupWallet(.iCloud) { status in
            switch status.status {
            case .readingShare:
                print("Reading share...")
            case .encryptingShare:
                print("Encrypting share...")
            case .storingShare:
                print("Storing backup...")
            case .done:
                print("Backup completed!")
            default:
                break
            }
        }
        
        // Complete the backup process
        try await backup.storageCallback()
        
        // Verify backup was successful
        let verifyBackup = try await portal.isWalletBackedUp()
        print("Backup verification: \(verifyBackup ? "Success" : "Failed")")
    } else {
        print("Wallet is already backed up")
    }
} catch {
    print("Error in backup process: \(error)")
}
```

**Implementation Notes**

* The method only considers backup shares with a status of `.completed`
* Incomplete or pending backups are not considered when determining backup status
* Useful for ensuring wallet security and prompting users to complete backup
* Should be checked before performing critical operations

**Usage in App Flow**

1.  After Wallet Creation:

    ```swift
    // Check backup status after creating wallet
    let wallet = try await portal.createWallet()
    if !(try await portal.isWalletBackedUp()) {
        // Prompt user to backup their wallet
        showBackupPrompt()
    }
    ```
2.  Security Checks:

    ```swift
    // Ensure wallet is backed up before large transactions
    func performTransaction() async throws {
        guard try await portal.isWalletBackedUp() else {
            throw CustomError.walletNotBackedUp
        }
        // Proceed with transaction
    }
    ```

**Related Documentation**

For more information about wallet backups, see:

* [Back up a wallet](broken-reference)
* [Backup methods](../../guides/web/back-up-a-wallet.md)
* [Backup options](../../guides/web/legacy-documentation/back-up-a-wallet/backup-options.md)
