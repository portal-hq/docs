---
description: >-
  Checks whether a wallet exists with completed signing shares. This method
  verifies the presence of a wallet by checking for completed signing shares.
---

# doesWalletExist

**Function Signature**

```swift
public func doesWalletExist(
    _ forChainId: String? = nil
) async throws -> Bool
```

**Parameters**

* `forChainId`: Optional chain identifier (e.g., "eip155:1" for Ethereum mainnet, "solana:5eykt4UsFv8P8NJdTREpY1vzqKqZKvdp" for Solana mainnet). If nil, checks for wallets across all chains.

**Returns**

A boolean value indicating whether a wallet exists:

* When chainId is provided:
  * Returns `true` if a wallet exists for the specified chain and has at least one completed signing share
  * Returns `false` if no wallet exists or if no signing shares are completed
* When chainId is nil:
  * Returns `true` if any wallet exists and has at least one completed signing share
  * Returns `false` if no wallets exist or if no signing shares are completed

**Throws**

* `PortalClassError.clientNotAvailable` if the client is not initialized

**Example Usage**

```swift
// Check for any existing wallet
do {
    let exists = try await portal.doesWalletExist()
    if exists {
        print("Wallet exists with completed signing shares")
    } else {
        print("No wallet found or no completed signing shares")
    }
} catch {
    print("Error checking wallet existence: \(error)")
}

// Check for specific chain wallets
do {
    // Check Ethereum wallet
    let ethExists = try await portal.doesWalletExist("eip155:1")
    if ethExists {
        print("Ethereum wallet exists")
    }
    
    // Check Solana wallet
    let solExists = try await portal.doesWalletExist("solana:5eykt4UsFv8P8NJdTREpY1vzqKqZKvdp")
    if solExists {
        print("Solana wallet exists")
    }
} catch {
    print("Error checking chain-specific wallets: \(error)")
}

// Complete example with wallet creation flow
do {
    let exists = try await portal.doesWalletExist()
    if !exists {
        // Create new wallet if none exists
        let newWallet = try await portal.createWallet { status in
            print("Creation status: \(status)")
        }
        print("Created new wallet with ETH address: \(newWallet.ethereum)")
        
        // Backup the wallet
        let backup = try await portal.backupWallet(.iCloud) { status in
            print("Backup status: \(status)")
        }
        try await backup.storageCallback()
    } else {
        // Wallet exists, fetch addresses
        let addresses = try await portal.getAddresses()
        if let ethAddress = addresses[.eip155] {
            print("Existing ETH address: \(ethAddress ?? "Not found")")
        }
    }
} catch {
    print("Error in wallet management flow: \(error)")
}
```

**Implementation Notes**

* The method checks for the presence of signing shares with a status of `.completed`
* Incomplete or pending signing shares are not considered when determining wallet existence
* The check can be performed for a specific blockchain or across all supported chains
* Useful for determining whether to trigger wallet creation or recovery flows

**Usage in App Flow**

1.  Initial App Launch:

    ```swift
    if try await portal.doesWalletExist() {
        // Navigate to main wallet interface
    } else {
        // Show wallet creation/recovery options
    }
    ```
2.  Chain-Specific Features:

    ```swift
    if try await portal.doesWalletExist("eip155:1") {
        // Enable Ethereum-specific features
    } else {
        // Prompt for Ethereum wallet creation
    }
    ```

**Related Documentation**

For more information about wallet management, see:

* [Wallet Lifecycle](../../resources/wallet-lifecycle.md)
