---
description: Creates a new wallet and generates addresses for supported chains.
---

# createWallet

**Function Signature**

```swift
public func createWallet(
    usingProgressCallback: ((MpcStatus) -> Void)? = nil
) async throws -> PortalCreateWalletResponse
```

**Parameters**

* **`usingProgressCallback`**: An optional callback for tracking the progress of wallet creation. It receives an `MpcStatus` object with the following&#x20;
  * `statuses`:
    * **`generatingShare`**: Creating the initial share
    * **`parsingShare`**: Processing the share data
    * **`storingShare`**: Saving the encrypted share
    * **`done`**: Process completed
  * `done`: Boolean indicating whether the whole operation is complete

**Returns**

*   **`PortalCreateWalletResponse`**: Contains the following:

    * **`ethereum`**: Generated Ethereum address
    * **`solana`**: Generated Solana address

    **Throws**

    * `PortalClassError.cannotCreateWallet`: If the wallet creation process fails.

    **Example Usage**

    ```swift
    do {
        let wallet = try await portal.createWallet { status in
            switch status.status {
            case .generatingShare:
                print("Generating share...")
            case .parsingShare:
                print("Processing share data...")
            case .storingShare:
                print("Saving share...")
            case .done:
                print("Wallet creation completed!")
            default:
                break
            }
        }
        
        print("Ethereum address: \(wallet.ethereum)")
        print("Solana address: \(wallet.solana)")
    } catch {
        print("Error creating wallet: \(error)")
    }
    ```



