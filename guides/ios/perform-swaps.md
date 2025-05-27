---
description: This guide will walk you through how to perform swaps with Portal's iOS SDK.
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



```swift
var swaps = PortalSwaps(
    apiKey: "SWAPS-API-KEY", 
    portal: portal
)
```

## Fetching a list of Sources

The `getSources()` function returns a dictionary containing valid, swappable token sources that can be used with your Portal MPC Wallet. The existing `Portal` instance that was provided at initialization is used to scope the request to the current chain.&#x20;

In order to retrieve this list, you can use the following code.

```swift
swaps.getSources() { result in
    var sources = result.data
}
```

### The returned list

The dictionary returned by the `getSources()` function is a set of key/value pairs where the key is the name of the token and the value is the token address for that token.

## Fetching a swap Quote

The `getQuote()` function returns a `Quote` object for a given set of arguments.&#x20;

### QuoteArgs

The minimum requirements for a valid set of `Quote` arguments are a `buyToken`, a `sellToken`, and either a `buyAmount` or a `sellAmount`.

An example of a valid `QuoteArgs` object would look something like this.

```swift
var quoteArgs = QuoteArgs(
    buyToken: "UNI", // You can also use the token address instead, e.g. "0x1f9840a85d5af5bf1d1762f925bdaddc4201f984"
    sellToken: "ETH",
    sellAmount: "10000", // Base units of the token
)
```

### Using your QuoteArgs

Once you have a valid `QuoteArgs` object, you can call the `getQuote()` function.

```swift
swaps.getQuote(args: quoteArgs) { result in
    var quote = result.data
}
```

### The Quote object

The `Quote` object includes two properties:

* `allowanceTarget` The address requiring allowances to complete the Swap
* `cost` The total cost (gas fees) of the swap transaction
* `transaction` The transaction to perform the swap

## Executing a Swap

If you're selling ERC20 tokens, you'll need to utilize the `allowanceTarget` to give the associated contract permissions to access a given amount of the token you're selling. This requires a token allowance of at least what you're selling. If you're selling `ETH`, this step is not necessary.

When you're ready to execute a swap, you can use the `request` function on your existing `Portal` instance to sign the transaction and make a request to the chain.

```swift
let requestResponse = try await portal.request(
  chainId, // CAIP-2 format (e.g. "eip155:11155111" for Ethereum Sepolia)
  withMethod: .eth_sendTransaction,
  andParams: [transaction]
)
```
