---
description: >-
  Here's exactly how you can send tokens from your Portal wallet to another
  address.
---

# Send tokens

## Fund your Wallet

Now that you have a wallet, the next step is to get test tokens for it. You can fund your wallet using **`portal.receiveTestnetAsset`**. If you are looking for a greater variety of test tokens, we recommend exploring our [faucets page](../../resources/testnet-faucets.md).

{% hint style="info" %}
The `chainId` will need to be a [CAIP-2](https://github.com/ChainAgnostic/CAIPs/blob/main/CAIPs/caip-2.md) compliant Chain ID. For more info on Chain ID formatting, see [this doc](../../resources/chain-id-formatting.md).
{% endhint %}

```kotlin
val chainId = "eip155:11155111" // Ethereum Sepolia

val params = FundParams(
  amount = "0.01", // You will receive 0.01 ETH
  token = "NATIVE" // Token, use "NATIVE" for the chain's native token
)

// Fund your Portal wallet
val response = portal.receiveTestnetAsset(chainId, params)

println("✅ Transaction hash: ${response.data.txHash}")
```

## Sending Tokens from your Wallet

Portal provides two ways to send transactions:

1. **`portal.sendAsset()`** - A simple method for sending tokens from your Portal wallet.
2. **`portal.provider.request()`** - Direct access to the underlying web3 provider for custom transactions. (You can learn more about this method [here](sign-a-transaction.md).)

For most use cases, we recommend using **`portal.sendAsset()`** as shown in the examples below.

### Submitting an EVM Transaction

```kotlin
val chainId = "eip155:11155111" // Ethereum Sepolia

val params = SendAssetParams(
  amount = "0.0001", // Sends 0.0001 ETH
  to = "0xDestinationAddress", // The recipient address
  token = "NATIVE" // Token, use "NATIVE" for the chain's native token
)

// Send the tokens
val txHash = portal.sendAsset(chainId, params)

println("✅ Transaction hash: ${txHash}")
```

{% hint style="warning" %}
If your Portal client is using [Account Abstraction](https://docs.portalhq.io/resources/account-abstraction), then **`txHash`** is actually a **User Operation hash**. You can manually look up the user operation hash [here](https://jiffyscan.xyz/?network=sepolia).
{% endhint %}

### Submitting a Solana Transaction

{% hint style="info" %}
You will need **`SOL`** to submit a Solana transaction, which is not currently supported by **`portal.receiveTestnetAsset`**. You can find a faucet to get test **`SOL`** tokens [here](../../resources/testnet-faucets.md).
{% endhint %}

```kotlin
val chainId = "solana:EtWTRABZaYq6iMfeYKouRu166VU2xqa1" // Solana Devnet

val params = SendAssetParams(
  amount = "0.0001", // Sends 0.0001 SOL
  to = "0xDestinationAddress", // The recipient address
  token = "NATIVE" // Token, use "NATIVE" for the chain's native token
)

// Send the tokens
val txHash = portal.sendAsset(chainId, params)

print("✅ Transaction hash: \(txHash)")
```

You just sent your first token from your Portal wallet, that's awesome! :tada:

You may have a more advanced use case than simply sending tokens from your Portal wallet. Next, we will dive into how to build your own transaction and also how to sign it (without submitting it).
