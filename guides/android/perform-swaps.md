---
description: >-
  This guide will walk you through how to perform swaps with Portal's Android
  SDK.
---

# Perform swaps

{% hint style="info" %}
Please contact sales for additional pricing information on using the Swaps integrations at [partner@portalhq.io](mailto:partner@portalhq.io)
{% endhint %}

The `PortalSwaps` class allows you to easily extend your existing `Portal` instance with swaps functionality.

## Initializing Swaps

{% hint style="info" %}
In order to initialize the Swaps class you need an existing `Portal` instance and a Swaps API Key. If you have not received a Swaps API Key, please contact support at [partner@portalhq.io](mailto:partner@portalhq.io).
{% endhint %}



```kotlin
val swaps = PortalSwaps("SWAPS-API-KEY", portal)
```

## Fetching a list of Sources

The `getSources()` function returns a `Map<String, String>` containing valid, swappable token sources that can be used with your Portal MPC Wallet. The existing `Portal` instance that was provided at initialization is used to scope the request to the current chain.&#x20;

In order to retrieve this list, you can use the following code.

```kotlin
val sources = swaps.getSources(chainId = "your chain Id")
```

### The returned list

The Map returned by the `getSources()` function is a set of key/value pairs where the key is the name of the token and the value is the token address for that token.

## Fetching a swap Quote

The `getQuote()` function returns a `Quote` object for a given set of arguments.&#x20;

### QuoteArgs

The minimum requirements for a valid set of `Quote` arguments are a `buyToken`, a `sellToken`, and either a `buyAmount` or a `sellAmount`.

An example of a valid `QuoteArgs` object would look something like this.

```kotlin
val quoteArgs = QuoteArgs(
    buyToken: "UNI",
    sellToken: "ETH",
    sellAmount: .01,
)
```

### Using your QuoteArgs

Once you have a valid `QuoteArgs` object, you can call the `getQuote()` function.

```kotlin
val quote = swaps.getQuote(chainId = "your chain id", args = quoteArgs)
```

### The Quote object

The `Quote` object includes two properties:

* `allowanceTarget` The address requiring allowances to complete the Swap
* `cost` The total cost (gas fees) of the swap transaction
* `transaction` The transaction to perform the swap

## Executing a Swap

If you're selling ERC20 tokens, you'll need to utilize the `allowanceTarget` to give the associated contract permissions to access a given amount of the token you're selling. This requires a token allowance of at least what you're selling. If you're selling `ETH`, this step is not necessary.

When you're ready to execute a swap, you can use the `Provider` on your existing `Portal` instance to sign the transaction and make a request to chain.

```kotlin
portal.provider.request(
    chainId = "your eth chain id",
    method = PortalRequestMethod.eth_sendTransaction, 
    params = listOf(quote.transaction)
)
```
