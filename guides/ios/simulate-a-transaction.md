---
description: >-
  Before committing to a transaction, it's often useful to simulate its outcome
  to understand the potential changes and detect any errors preemptively.
---

# Simulate a transaction

Portal provides the function **`portal.api.simulateTransaction`**, which gives you insights into what will happen upon executing the transaction.

Here's how it works:

```swift
func handleSimulateTransaction() {
    // First, create a transaction.
    let transaction = SimulateTransactionParam(
        to: "0xRecipientAddressHere", // The recipient address.
        value: "0x10DE4A2A",          // The value to be sent in Wei.
        data: nil,                    // Data for the transaction (for contract interactions).
        maxFeePerGas: nil,            // Maximum fee per gas.
        maxPriorityFeePerGas: nil,    // Maximum priority fee per gas.
        gas: nil,                     // The gas limit.
        gasPrice: nil                 // Gas price in Wei.
    )

    // Next, simulate the transaction.
    portal?.api.simulateTransaction(transaction: transaction) {
        (result: Result<SimulatedTransaction>) in

        // Check for general errors.
        if let error = result.error {
            print("❌ Error simulating transaction:", error)
            return
        }

        // Safely unwrap the simulated result.
        guard let simulatedResult = result.data else {
            print("❌ Unexpected error: result data is nil.")
            return
        }

        // Finally, you can handle or display the simulation results as needed.
        if let requestError = simulatedResult.requestError {
            print("❌ Request error:", requestError.message)
        } else if let error = simulatedResult.error {
            print("✅ Transaction will have error:", error.message)
        } else {
            print("✅ Simulated transaction changes:", simulatedResult.changes)
        }
    }
}
```

This function will return the results of the transaction simulation:

* **changes**: An array detailing all the potential transaction outcomes. Each change has the following structure:
  * **amount**: The amount being transferred.
  * **assetType**: The type of asset being dealt with (`NATIVE` or `ERC20` or `ERC721` or `ERC1155` or `SPECIAL_NFT`).
  * **changeType**: The type of change (`APPROVE` or `TRANSFER`).
  * **contractAddress**: Address of the contract being interacted with (null for native transactions).
  * **decimals**: Decimals used in the asset.
  * **from**: The sender address.
  * **name**: The name of the asset.
  * **rawAmount**: The unformatted amount being transferred.
  * **symbol**: The asset's symbol.
  * **to**: The receiver's address.
  * **tokenId**: An identifier for tokens (null for assets that are not tokens).
* **gasUsed**: The gas used by the transaction.
* **error**: An object that contains the error message if the transaction would fail upon execution.
* **requestError**: An object that contains the error message if there was an issue with the request, such as a malformed transaction argument being provided.

By incorporating transaction simulations, you can provide your users with a preview of the transaction outcomes and preemptively detect and handle errors, ensuring a smoother user experience.

And now you are simulating transactions with Portal! 🙌 🚀 Next, we'll explore how to back up the wallet for recovery if the user loses device access.



**Related Documentation**

* [evaluateTransaction function reference](https://docs.portalhq.io/reference/ios/evaluatetransaction)
