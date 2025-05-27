---
description: >-
  Want to implement your own style, but with all the functionality Portal
  offers? Use these functions to implement your own custom web3 UI for your
  users.
---

# Sign a transaction

This example shows how the **Portal Provider** interacts with the **MPC wallet** and **the blockchain**.&#x20;

The **params** have a hardcoded address for the transaction of sending _1 wei_ from our **MPC wallet**.  The **Provider** then receives a _signed transaction_ from our **mobile MPC library** and submits that to chain using the configured RPC url.

Here is a quick example of how you can make requests using Portal's web3 provider:

{% hint style="info" %}
Ensure you have set the gateway URL correctly with [Infura](https://www.infura.io/) or [Alchemy](https://www.alchemy.com/) when you initialize the portal class.
{% endhint %}

```kotlin
// Imports...

class MainActivity : AppCompatActivity() {
    lateinit var portal: Portal

    private fun sendOneWei() {
        lifecycleScope.launch {
            val params = listOf(
              EthTransactionParam(
                from = address,
                to = toAddress,
                gas = "0x6000",
                gasPrice = null,
                maxFeePerGas = null,
                maxPriorityFeePerGas = null,
                value = "0x${BigInteger("1").toString(16)}",
                data = "",
              ),
            )
    
            Log.println(Log.INFO, "[PortalEx]", "Sending 1 wei: $params")
    
            try {
              Log.println(
                Log.INFO,
                "[PortalEx]",
                "Sending request for 'eth_SendTransaction' with params $params",
              )
    
              val result = portal.request(
                chainId = ethChainId,
                method = PortalRequestMethod.eth_sendTransaction,
                params = params
              )
    
              Log.println(Log.INFO, "[PortalEx]", "Transaction hash: ${result.result}")
              showTestResult(TestCase.Send1Wei, true, "Transaction hash: ${result.result}")
            } catch (err: Throwable) {
              Log.println(Log.WARN, "[PortalEx]", "Failed to send transaction: $err")
              showTestResult(TestCase.Send1Wei, false)
            } catch (e: Exception) {
              Log.println(Log.WARN, "[PortalEx]", "Failed to send transaction: $e")
              showTestResult(TestCase.Send1Wei, false)
            }
    }
}
```



## Estimating Gas

By default, Portal will estimate and populate the `gas` property in a `transaction` object if the property is undefined.

To estimate the `gas` value manually use the `eth_estimateGas` RPC call and pass in your transaction as the parameter.

```kotlin
suspend fun estimateGas() {
    try {
        // Create the transaction params.
        val params = listOf(TransactionParams(
            "",
            "0x9AeCB4DA6b438830b88C5F40b6Bf36EF3073B350",
            "0x${BigInteger("1").toString(16)}",
            "",
            portal.address
        ))

        // Attempt to send the transaction.
        val response = portal.provider.request(
            chainId = "your chain Id"
            method = PortalRequestMethod.eth_estimateGas,
            params = params
        )

        Log.d("Portal", "Estimated gas: ${response.result}")
    } catch (err: Exception) {
        // ❌ Handle errors sending the transaction.
    }
}
```

### Signing Solana Transactions

We offer a `portal.sendSol` function to make the process of sending sol very simple.&#x20;

```kotlin
suspend fun sendSolana() {
    kotlin.runCatching {
      portal.sendSol(
        chainId = SOLANA_DEV_NET_CHAIN_ID,
        lamports = 1,
        to = SOLANA_TEST_ADDRESS,
      )
    }.onSuccess { response ->
      Timber.i("✅ Send Solana response: $response")
    }.onFailure {
      Timber.e("❌ Send Solana error: $it")
    }
}
```

We also offer methods to build your Solana (and Eth) transactions.

```kotlin
suspend fun buildSolanaTransaction() {
    val buildTransactionParam = BuildTransactionParam(
      to = SOLANA_TEST_ADDRESS,
      token = "SOL",
      amount = "0.001"
    )
    portal.api.buildSolanaTransaction(SOLANA_DEV_NET_CHAIN_ID, buildTransactionParam).onSuccess { response ->
      if (response.error == null) {
        Timber.i("✅ Build Transaction response: ${response.transaction}")
      } else {
        Timber.i("❌ Build Transaction response: ${response.error}")
      }
    }.onFailure {
      Timber.e("❌ Build Transaction error: $it")
    }
}
```

If you would like to construct your own more advanced transaction for solana then here is how you can do it. Add [SolanaKt library](https://github.com/metaplex-foundation/SolanaKT) to your project as it will help you build the transaction. (You can also use any other library of your choice.)

```gradle
// In your settings.gradle
repositories {
    ...
    maven { url 'https://jitpack.io' }
}

// In your app/build.gradle
dependencies {
    ...
    implementation 'com.github.metaplex-foundation:SolanaKT:{version}'
}
```

Then build a Solana request and send it using `portal.request(chainId, PortalRequestMethod.sol_signAndSendTransaction, listOf(solanaRequest)`

```kotlin
// Here is how you can build the solana request using SolanaKt library
  suspend fun sendSolana(
    solanaChainId: String,
    fromAddress: String,
    toAddress: String,
    lamports: Long
  ): String {
    val recentBlockhashRpcResponse = portal.request(
      solanaChainId,
      PortalRequestMethod.sol_getLatestBlockhash,
      emptyList()
    ).result as PortalProviderRpcResponse
    val recentBlockhashResult = recentBlockhashRpcResponse.result
    val recentBlockhash = Gson().let {
      it.fromJson(it.toJson(recentBlockhashResult), SolGetLatestBlockhashResult::class.java)
    }

    val solanaRequest = prepareSolanaRequest(
      fromAddress,
      toAddress,
      lamports,
      recentBlockhash.value.blockhash
    )

    val transactionHash = portal.request(
      solanaChainId,
      PortalRequestMethod.sol_signAndSendTransaction,
      listOf(solanaRequest)
    ).result as String
    return transactionHash
  }

  suspend fun prepareSolanaRequest(
    fromAddress: String,
    toAddress: String,
    lamports: Long,
    recentBlockhash: String
  ): PortalSolanaRequest {
    val fromPublicKey = PublicKey(fromAddress)
    val toPublicKey = PublicKey(toAddress)

    val transferInstruction = SystemProgram.transfer(
      fromPublicKey,
      toPublicKey,
      lamports
    )

    Log.i("PortalSolana", "Transfer instruction: $transferInstruction")

    val transaction = Transaction()
    transaction.addInstruction(transferInstruction)
    transaction.recentBlockhash = recentBlockhash
    transaction.feePayer = fromPublicKey

    val message = transaction.compileMessage()

    Log.i("PortalSolana", "Compiled message: $message")

    val header = PortalSolanaHeader(
      numRequiredSignatures = message.header.numRequiredSignatures,
      numReadonlySignedAccounts = message.header.numReadonlySignedAccounts,
      numReadonlyUnsignedAccounts = message.header.numReadonlyUnsignedAccounts
    )

    val instructions = message.instructions.map { instruction ->
      PortalSolanaInstruction(
        instruction.programIdIndex,
        instruction.accounts,
        instruction.data
      )
    }

    val accountKeys = message.accountKeys.map { key -> key.toString() }

    return PortalSolanaRequest(
      message = PortalSolanaMessage(
        accountKeys = accountKeys,
        header = header,
        recentBlockhash = message.recentBlockhash,
        instructions = instructions
      )
    )
  }
```

***

### Raw sign <a href="#raw-sign" id="raw-sign"></a>

You can now utilize our SDK to generate raw signatures that are generated using the underlying key share without adding any chain specific formatting to the signature. This effectively unlocks your ability to use the Portal SDK with any chain that uses `SECP256K1` or `ED25519`.

```swift
    try {
      val result = portal.rawSign("74657374", chainId = "eip155:1")

      Log.println(
        Log.INFO,
        "[Portal]",
        "✅ Request: raw_sign, Result: $result",
      )
      
    } catch (err: Throwable) {
      Log.println(
        Log.WARN,
        "[Portal]",
        "❌ Failed to send request raw_sign: $err",
      )
    }
```

***

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

```kotlin
val portal = Portal(
    apiKey = "API_KEY_OR_SESSION_TOKEN",
    featureFlags = FeatureFlags(
        useEnclaveMpcApi = true
        // other feature flags...
    )
    // other configurations...
)
```

By setting `useEnclaveMpcApi` to `true`, the Portal instance will use the Enclave MPC API for signing transactions, ensuring faster computation and consistent performance across client devices.

***

And now you are signing transactions with Portal! 🙌 🚀 Next, we'll explore how to simulate a transaction so that you can create smoother experiences for your users.
