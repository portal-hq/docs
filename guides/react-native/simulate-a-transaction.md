---
description: >-
  Before committing to a transaction, it's often useful to simulate its outcome
  to understand the potential changes and detect any errors preemptively.
---

# Simulate a transaction

Portal provides the function **`portal.api.simulateTransaction`**, which gives you insights into what will happen upon executing a transaction.

Here's how it works:

```typescript
const handleSimulateTransaction = async () => {
  // First, construct a transaction.
  const transaction = {
    to: '0xRecipientAddressHere', // {string} The recipient address.
    value: '0x10DE4A2A',  // {?string} The value to be sent in Wei.
    data: undefined,  // {?string} Data for the transaction (for contract interactions).
    maxFeePerGas: undefined, // {?string} Maximum fee per gas.
    maxPriorityFeePerGas: undefined, // {?string} Maximum priority fee per gas.
    gas: undefined, // {?string} The gas limit.
    gasPrice: undefined // {?string} Gas price in Wei.
  };
  
  // Next, simulate the transaction.
  const simulatedResult = await portal.api.simulateTransaction(transaction);

  // Finally, you can handle or display the simulation results as needed.
  if (simulatedResult.error) {
    console.error("Transaction Error:", simulatedResult.error.message);
  } else if (simulatedResult.requestError) {
    console.error("Request Error:", simulatedResult.requestError.message);
  } else {
    console.log("Simulated Transaction Results:", simulatedResult.changes);
  }
}
```

This function will return a JSON object that breaks down the transaction simulation:

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
