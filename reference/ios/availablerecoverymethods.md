---
description: >-
  Retrieves the list of available backup methods that can be used for wallet
  recovery.
---

# availableRecoveryMethods

**Function Signature**

```swift
public func availableRecoveryMethods(
    _ forChainId: String? = nil
) async throws -> [BackupMethods]
```

**Parameters**

* `forChainId`: Optional chain identifier (e.g., "eip155:1" for Ethereum mainnet, "solana:5eykt4UsFv8P8NJdTREpY1vzqKqZKvdp" for Solana mainnet). If nil, returns backup methods from all wallets.

**Returns**

An array of `BackupMethods` representing completed backups, which may include:

* `.GoogleDrive`: Google Drive backup
* `.iCloud`: iCloud backup
* `.Password`: Password-protected backup
* `.Passkey`: Passkey backup
* `.local`: Local backup

**Throws**

* `PortalClassError.clientNotAvailable` if the client is not initialized
* When a specific chainId is provided:
  * `PortalClassError.unsupportedChainId` if the chain's namespace is not supported
  * `PortalClassError.noWalletFoundForChain` if no wallet exists for the specified chain

**Example Usage**

```swift
// Check available recovery methods for all chains
do {
    let methods = try await portal.availableRecoveryMethods()
    print("Available recovery methods: \(methods)")
    
    // Check if specific backup method is available
    if methods.contains(.Password) {
        print("Password recovery is available")
    }
    if methods.contains(.GoogleDrive) {
        print("Google Drive recovery is available")
    }
} catch {
    print("Error checking recovery methods: \(error)")
}

// Check recovery methods for specific chain
do {
    // Check Ethereum mainnet
    let ethMethods = try await portal.availableRecoveryMethods("eip155:1")
    print("Available recovery methods for Ethereum: \(ethMethods)")
    
    // Check Solana mainnet
    let solMethods = try await portal.availableRecoveryMethods("solana:5eykt4UsFv8P8NJdTREpY1vzqKqZKvdp")
    print("Available recovery methods for Solana: \(solMethods)")
} catch {
    print("Error checking chain-specific recovery methods: \(error)")
}

// Complete example with recovery
do {
    let methods = try await portal.availableRecoveryMethods()
    
    for method in methods {
        switch method {
        case .Password:
            // Attempt password recovery
            try portal.setPassword("userPassword")
            let recovered = try await portal.recoverWallet(.Password)
            print("Recovered with password: \(recovered.ethereum)")
            
        case .GoogleDrive:
            // Attempt Google Drive recovery
            let recovered = try await portal.recoverWallet(.GoogleDrive)
            print("Recovered with Google Drive: \(recovered.ethereum)")
            
        case .iCloud:
            // Attempt iCloud recovery
            let recovered = try await portal.recoverWallet(.iCloud)
            print("Recovered with iCloud: \(recovered.ethereum)")
            
        default:
            print("Other recovery method available: \(method)")
        }
    }
} catch {
    print("Error in recovery process: \(error)")
}
```

**Implementation Notes**

* The method only returns backup methods where the corresponding backup share pairs have a status of `.completed`
* Methods with incomplete or pending backups are excluded
* For chain-specific queries, the method validates the chain ID format and namespace support
* The function is useful for determining which recovery options to present to users

**Related Documentation**

For more information about recovery methods and chain support, see:

* [Recover a wallet](../../guides/web/recover-a-wallet.md)
