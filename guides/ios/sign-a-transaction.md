---
description: >-
  Want to implement your own style, but with all the functionality Portal
  offers? Use these functions to implement your own custom web3 UI for your
  users.
---

# Sign a transaction

This example shows how the **Portal Provider** interacts with **the blockchain**.&#x20;

### Signing an ethereum transaction

The **Provider** has a `address` of our **MPC wallet**.  The **Provider** then proxies the request for balance to the configured RPC url and returns the result.

{% hint style="info" %}
Ensure you have set the gateway URL correctly with [Infura](https://www.infura.io/) or [Alchemy](https://www.alchemy.com/) when you initialize the portal class.
{% endhint %}

Below is an example of how to use Portal to send a USDC transaction on the Ethereum Sepolia testnet:

```swift
// Ethereum Sepolia
let chainId = "eip155:11155111" // CAIP-2 format.

// Define the transaction parameters
let transactionParam = BuildTransactionParam(
    to: "0xDestinationAddress", // Replace with the recipient's address
    token: "USDC", // Token to send (USDC in this case)
    amount: "1" // Amount of USDC to send
)

// Build the transaction using Portal
let txDetails = try await portal.buildEip155Transaction(
  chainId: chainId,
  params: transactionParam
)
    
// Sign and send the transaction
let response = try await portal.request(
    chainId,
    withMethod: .eth_sendTransaction,
    andParams: [txDetails.transaction]
)

// Obtain the transaction hash.
guard let txHash = response.result as? String else {
    // Handle a bad response here
    return
}

print("✅ Transaction hash: \(txHash)")
```

This example demonstrates how to use **Portal's SDK** to send a transaction on the Ethereum Sepolia testnet. The transaction involves sending **1 USDC** to a specified recipient address. Here's a breakdown of the process:

1. **Chain ID**: The transaction is configured for the Ethereum Sepolia testnet using the CAIP-2 format (`eip155:11155111`).
2. **Transaction Parameters**: The `BuildTransactionParam` struct is used to define the transaction details:
   * `to`: The recipient's address.
   * `token`: The token to send (in this case, `USDC`).
   * `amount`: The amount of the token to send (e.g., `1` USDC).
3. **Building the Transaction**: The `portal.buildEip155Transaction` method constructs the transaction object using the specified parameters.
4. **Signing and Sending**: The `portal.request` method is used to sign and send the transaction. The method takes the chain ID, the `eth_sendTransaction` method, and the transaction details as parameters.
5. **Transaction Hash**: Once the transaction is successfully sent, the transaction hash is returned and printed to the console.

However, there's many other `PortalRequestMethod`s available for you to use, which [you can find in the SDK](https://github.com/portal-hq/portalswift).

## Estimating Gas

By default, Portal will estimate and populate the `gas` property in a `transaction` object if the property is `undefined`.

To estimate the `gas` value manually, you'll want to use the `eth_estimateGas` RPC call and pass in your transaction as the parameter.

```swift
// Ethereum Sepolia
let chainId = "eip155:11155111" // CAIP-2 format.

// Obtain the eip155 address of the user's wallet.
guard let eip155Address = await portal.getAddress(chainId) else {
  throw PortalExampleAppError.addressNotFound()
}

// Create the transaction object.
let transaction = [
  "from": eip155Address, // Replace with the sender's address
  "to": "0xRecipientAddress", // Replace with the recipient's address
  "value": "0x10", // Native token amount to send (WEI for eip155:11155111).
]

// Make the eth_estimateGas request with the transaction object.
let requestResponse = try await portal.request(
  chainId,
  withMethod: .eth_estimateGas,
  andParams: [transaction]
)

// Retrieve the estimated gas value.
guard let estimatedGas = requestResponse.result as? String else {
  throw PortalExampleAppError.invalidResponseTypeForRequest()
}

// ✅ Nice! You just retrieved the gas estimate.
```

Here's a breakdown of the process:

1. **Chain ID**: The transaction is configured for the Ethereum Sepolia testnet using the CAIP-2 format (`eip155:11155111`).
2. **Sender Address**: The `portal.getAddress` method retrieves the user's wallet address for the specified chain.
3. **Transaction Object**: A transaction object is created with the following fields:
   * `from`: The sender's address.
   * `to`: The recipient's address.
   * `value`: The amount of native token (ETH) to send, specified in WEI.
4. **Gas Estimation**: The `portal.request` method is used to call the `eth_estimateGas` method, which estimates the gas required for the transaction.
5. **Result Handling**: The estimated gas value is retrieved from the response and printed to the console.

### Signing Solana Transactions

To construct your own advanced transaction for solana simply follow that example:

```swift
// Solana Devnet
let chainId = "solana:EtWTRABZaYq6iMfeYKouRu166VU2xqa1" // CAIP-2 format.
​
// Define the transaction parameters
let transactionParam = BuildTransactionParam(
    to: "DestinationAddress", // Replace with the recipient's address
    token: "USDC", // Token to send (USDC in this case)
    amount: "1" // Amount of USDC to send
)
​
// Build the transaction using Portal
let txDetails = await portal.buildSolanaTransaction(
  chainId: chainId,
  params: transactionParam
)
​
// Sign and send the transaction
let response = try await portal.request(
    chainId,
    withMethod: .sol_signAndSendTransaction,
    andParams: [txDetails.transaction]
)
​
// Obtain the transaction hash.
guard let txHash = response.result as? String else {
    // Handle a bad response here
    return
}
​
print("✅ Transaction hash: \(txHash)")
```

This example demonstrates how to use **Portal's SDK** to send a transaction on the **Solana Devnet**. The transaction involves sending **1 USDC** to a specified recipient address. Here's a breakdown of the process:

1. **Chain ID**: The transaction is configured for the Solana Devnet using the CAIP-2 format (`solana:EtWTRABZaYq6iMfeYKouRu166VU2xqa1`).
2. **Transaction Parameters**: The `BuildTransactionParam` struct is used to define the transaction details:
   * `to`: The recipient's address.
   * `token`: The token to send (in this case, `USDC`).
   * `amount`: The amount of the token to send (e.g., `1` USDC).
3. **Building the Transaction**: The `portal.buildSolanaTransaction` method constructs the transaction object using the specified parameters.
4. **Signing and Sending**: The `portal.request` method is used to sign and send the transaction. The method takes the chain ID, the `sol_signAndSendTransaction` method, and the transaction details as parameters.
5. **Transaction Hash**: Once the transaction is successfully sent, the transaction hash is returned and printed to the console.



### Raw sign <a href="#raw-sign" id="raw-sign"></a>

You can now utilize our SDK to generate raw signatures that are generated using the underlying key share without adding any chain specific formatting to the signature. This effectively unlocks your ability to use the Portal SDK with any chain that uses `SECP256K1` or `ED25519`.

<pre class="language-swift"><code class="lang-swift"><strong>  do {
</strong>      let response = try await portal.rawSign(
          message: "74657374", // Must be in hex format, e.g. "test" is "74657374" in hex.
          chainId: "eip155:11155111" // choose the chainId eip155 = secp256k1, solana = ed25519
      )
      
      if let signature = response.result as? String  {
          print("✅ Successfully signed message: \(signature)")
      }
      
  } catch {
      print("❌ Error signing message: \(error)")
  }
</code></pre>

### Enabling the Enclave Signer

Executing MPC operations requires computation on the client device. Depending on the CPU of the client device this can take variable amounts of time, leading to inconsistent signing times across users.

To solve this, you can leverage the **Enclave MPC API** from your SDK to execute MPC operations server-side which leads to consistent (and often faster) signing speeds.&#x20;

This feature leverages the Enclave MPC API by sending the user's key share to a Trusted Execution Environment (TEE) which runs the MPC code in a secure AWS Nitro Enclave with the same non-custodial guarantees as client-side MPC.

{% hint style="warning" %}
By enabling the `useEnclaveMpcApi` feature flag the client key share will be transmitted from the user device, but it is **never** stored.
{% endhint %}

TEEs in Nitro Enclaves work by **encrypting memory** and **verifying execution.** Encrypted memory means that all of the data being processed on the enclave can’t be accessed by anything other than the running application. Portal employees can’t even read the data on there! Verified execution means that a user can cryptographically verify that their request was handled in a secure enclave. When a user sends an API request to the enclave, Portal returns a set of signed “measurements” that can be verified by the enclave’s public key to ensure that the request was processed on an AWS Nitro Enclave.

To learn more check out our blog post introducing[ the Enclave MPC API](https://www.portalhq.io/post/introducing-the-enclave-mpc-api).

Here’s how to enable it:

```swift
import PortalSwift

// Initialize Portal with the Enclave MPC API enabled
let portal = try Portal(
  "CLIENT_API_KEY_OR_CLIENT_SESSION_TOKEN", 
   featureFlags: FeatureFlags(
     useEnclaveMPCApi: true
   )
)
```

By setting `useEnclaveMPCApi` to `true`, the Portal instance will use the Enclave MPC API for signing transactions, ensuring faster computation and consistent performance across client devices.

***

And now you are signing transactions with Portal! 🙌 🚀 Next, we'll explore how to simulate a transaction so that you can create smoother experiences for your users.



**Related Documentation**

* [buildEip155Transaction function reference](https://docs.portalhq.io/reference/ios/buildeip155transaction)
* [buildSolanaTransaction function reference](https://docs.portalhq.io/reference/ios/buildsolanatransaction)
* [sendSol function reference](https://docs.portalhq.io/reference/ios/sendsol)
