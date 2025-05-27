---
description: >-
  Checks whether wallet shares are present in the device's keychain. This method
  verifies if the signing shares required for wallet operations are stored in
  the device's local keychain.
---

# isWalletOnDevice

**Function Signature**

```swift
public func isWalletOnDevice(
    _ forChainId: String? = nil
) async throws -> Bool
```

**Parameters**

* `forChainId`: Optional chain identifier (e.g., "eip155:1" for Ethereum mainnet, "solana:5eykt4UsFv8P8NJdTREpY1vzqKqZKvdp" for Solana mainnet). If nil, checks for any valid shares across all chains.

**Returns**

A boolean value indicating whether wallet shares exist on the device:

* When forChainId is provided:
  * Returns `true` if shares exist for the specified chain
  * Returns `false` if no shares are found for the chain
* When forChainId is nil:
  * Returns `true` if any valid share exists (has non-empty ID)
  * Returns `false` if no valid shares are found

**Throws**

* `PortalClassError.invalidChainId` if the provided chain ID format is invalid
* `PortalClassError.unsupportedChainId` if the chain's namespace is not supported
* Various keychain-related errors if share retrieval fails

**Example Usage**

```swift
// Check for any wallet on device
do {
    let isOnDevice = try await portal.isWalletOnDevice()
    if isOnDevice {
        print("Wallet shares found on device")
    } else {
        print("No wallet shares on device")
    }
} catch {
    print("Error checking wallet presence: \(error)")
}

// Check for specific chain wallets
do {
    // Check Ethereum wallet
    let ethOnDevice = try await portal.isWalletOnDevice("eip155:1")
    if ethOnDevice {
        print("Ethereum wallet shares found on device")
    }
    
    // Check Solana wallet
    let solOnDevice = try await portal.isWalletOnDevice("solana:5eykt4UsFv8P8NJdTREpY1vzqKqZKvdp")
    if solOnDevice {
        print("Solana wallet shares found on device")
    }
} catch {
    print("Error checking chain-specific wallets: \(error)")
}

// Complete example with recovery flow
do {
    let isOnDevice = try await portal.isWalletOnDevice()
    if !isOnDevice {
        // Check if wallet exists and could be used to recover the wallet
        if try await portal.isWalletRecoverable() {
            // Recover wallet using available backup method
            let methods = try await portal.availableRecoveryMethods()
            if methods.contains(.iCloud) {
                let recovered = try await portal.recoverWallet(.iCloud)
                print("Recovered wallet with address: \(recovered.ethereum)")
            }
        } else {
            // Create new wallet if none exists
            let newWallet = try await portal.createWallet()
            print("Created new wallet with ETH address: \(newWallet.ethereum)")
        }
    } else {
        // Wallet is on device, verify it's working
        let addresses = try await portal.getAddresses()
        if let ethAddress = addresses[.eip155] {
            print("Active ETH address: \(ethAddress ?? "Not found")")
        }
    }
} catch {
    print("Error in wallet management flow: \(error)")
}
```

**Implementation Notes**

* This method checks for the physical presence of shares on the device, regardless of their status
* Different from `doesWalletExist()` which checks for completed shares on the server
* Useful for determining whether to trigger wallet recovery or creation flows
* The check is performed against the device's keychain storage

**Usage in App Flow**

1.  App Launch Check:

    ```swift
    func checkWalletStatus() async {
        if try await portal.isWalletOnDevice() {
            // Wallet is present, proceed to main app
            showMainInterface()
        } else {
            // No wallet on device
            if try await portal.isWalletRecoverable() {
                showRecoveryInterface()
            } else {
                showCreateWalletInterface()
            }
        }
    }
    ```
2.  Device Migration:

    ```swift
    func handleDeviceMigration() async {
        guard try await portal.isWalletBackedUp() else {
            throw MigrationError.walletNotBackedUp
        }
        
        if try await portal.isWalletOnDevice() {
            // Wallet already on this device
            return
        }
        
        // Proceed with recovery on new device
        let recovered = try await portal.recoverWallet(.iCloud)
        print("Migrated wallet to new device")
    }
    ```

**Related Documentation**

For more information about wallet storage and device management, see:

* [Cross-device sessions](../../guides/web/cross-device-sessions.md)
