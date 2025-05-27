---
description: This guide will walk you through how to perform swaps with Portal's Web SDK.
---

# Perform swaps

{% hint style="info" %}
Please contact sales for additional pricing information on using the Swaps integrations at [partner@portalhq.io](mailto:partner@portalhq.io)
{% endhint %}

Portal's Swaps integration allows you to easily extend your existing `Portal` instance with swaps functionality.



## Fetching a list of Sources

The `getSources()` function returns an object containing valid, swappable token sources that can be used with your Portal MPC Wallet. The existing `Portal` instance that was provided at initialization is used to scope the request to the current chain.&#x20;

In order to retrieve this list, you can use the following code.

```typescript
const fetchSources = async (): Record<string, string> => {
    return await portal.getSources('YOUR_SWAPS_API_KEY', 'eip155:1')
}
```

### The returned list

The object returned by the `getSources()` function is a set of key/value pairs where the key is the name of the token and the value is the token address for that token.

## Fetching a swap Quote

The `getQuote()` function returns a `Quote` object for a given set of arguments.&#x20;

### QuoteArgs

The minimum requirements for a valid set of `Quote` arguments are a `buyToken`, a `sellToken`, and either a `buyAmount` or a `sellAmount`.

An example of a valid `QuoteArgs` object would look something like this.

```typescript
import { QuoteArgs } from '@portal-hq/swaps'

const quoteArgs: QuoteArgs = {
    buyToken: 'UNI',
    sellToken: 'ETH',
    sellAmount: 1000, // Amount is in base units, so WEI for ETH
}
```

### Using your QuoteArgs

Once you have a valid `QuoteArgs` object, you can call the `getQuote()` function.

```typescript
const fetchQuote = async (quoteArgs: QuoteArgs): QuoteResponse {
    return await portal.getQuote('YOUR_SWAPS_API_KEY', quoteArgs, 'eip155:1')
}
```

### The Quote object

The `Quote` object includes two properties:

* `allowanceTarget` The address requiring allowances to complete the Swap
* `cost` The total cost (gas fees) of the swap transaction
* `transaction` The transaction to perform the swap

## Executing a Swap

If you're selling ERC20 tokens, you'll need to utilize the `allowanceTarget` to give the associated contract permissions to access a given amount of the token you're selling. This requires a token allowance of at least what you're selling. If you're selling `ETH`, this step is not necessary.

When you're ready to execute a swap, you can use the `Provider` on your existing `Portal` instance to sign the transaction and make a request to chain.

```typescript
import { Eip1559, LegacyTx } from '@portal-hq/provider/types'

const executeSwap = async (transaction: Eip1559 | LegacyTx) => {
    const txHash = await portal.ethSendTransaction('eip155:1', transaction)
}
```
