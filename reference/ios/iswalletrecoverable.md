---
description: >-
  Checks whether the wallet can be recovered using any available backup methods.
  This method verifies if there are any completed backup methods that could be
  used to recover the wallet.
---

# isWalletRecoverable

**Function Signature**

```swift
public func isWalletRecoverable(
    _ forChainId: String? = nil
) async throws -> Bool
```

**Parameters**

* `forChainId`: Optional CAIP-2 chain identifier (e.g., "eip155:1" for Ethereum mainnet, "solana:5eykt4UsFv8P8NJdTREpY1vzqKqZKvdp" for Solana mainnet). If nil, checks recovery options across all chains.

**Returns**

A boolean value indicating whether the wallet can be recovered:

* Returns `true` if at least one completed backup method is available
* Returns `false` if no completed backup methods are found

**Throws**

* `PortalClassError.clientNotAvailable` if the client is not initialized
* `PortalClassError.unsupportedChainId` if the chain's namespace is not supported
* `PortalClassError.noWalletFoundForChain` if no wallet exists for the specified chain

**Example Usage**

```swift
// Check if any wallet is recoverable
do {
    let isRecoverable = try await portal.isWalletRecoverable()
    if isRecoverable {
        print("Wallet can be recovered")
    } else {
        print("No recovery methods available")
    }
} catch {
    print("Error checking recovery status: \(error)")
}

// Check specific chain wallet recovery
do {
    // Check Ethereum wallet
    let ethRecoverable = try await portal.isWalletRecoverable("eip155:1")
    if ethRecoverable {
        print("Ethereum wallet can be recovered")
    }
    
    // Check Solana wallet
    let solRecoverable = try await portal.isWalletRecoverable("solana:5eykt4UsFv8P8NJdTREpY1vzqKqZKvdp")
    if solRecoverable {
        print("Solana wallet can be recovered")
    }
} catch {
    print("Error checking chain-specific recovery: \(error)")
}

// Complete example with recovery flow
do {
    if try await portal.isWalletRecoverable() {
        // Get available recovery methods
        let methods = try await portal.availableRecoveryMethods()
        
        // Try each available method
        for method in methods {
            do {
                let recovered = try await portal.recoverWallet(method) { status in
                    print("Recovery status: \(status)")
                }
                print("Successfully recovered wallet using \(method)")
                print("Ethereum address: \(recovered.ethereum)")
                if let solanaAddress = recovered.solana {
                    print("Solana address: \(solanaAddress)")
                }
                break // Exit after successful recovery
            } catch {
                print("Failed to recover using \(method): \(error)")
                continue // Try next method
            }
        }
    } else {
        print("No recovery methods available, need to create new wallet")
        let newWallet = try await portal.createWallet()
        print("Created new wallet")
    }
} catch {
    print("Error in recovery process: \(error)")
}
```

**Implementation Notes**

* This method uses `availableRecoveryMethods()` internally to determine if any completed backup methods exist
* Only considers backup methods that have been fully completed and verified
* Useful for determining whether to show recovery options to users
* Should be checked before attempting wallet recovery

**Usage in App Flow**

1.  Initial Setup Check:

    <pre class="language-swift"><code class="lang-swift"><strong>func checkWalletStatus() async {
    </strong>    if try await portal.isWalletOnDevice() {
            showMainInterface()
        } else if try await portal.isWalletRecoverable() {
            showRecoveryInterface()
        } else {
            showCreateWalletInterface()
        }
    }
    </code></pre>
2.  Recovery Process:

    ```swift
    func handleRecovery() async {
        guard try await portal.isWalletRecoverable() else {
            throw WalletError.notRecoverable
        }
        
        let methods = try await portal.availableRecoveryMethods()
        showRecoveryOptions(methods)
    }
    ```

**Related Documentation**

For more information about wallet recovery, see:

* [Recover a wallet](../../guides/web/recover-a-wallet.md)
* [Cross-device sessions](../../guides/web/cross-device-sessions.md)
