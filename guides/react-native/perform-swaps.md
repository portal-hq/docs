---
description: >-
  This guide will walk you through how to perform swaps with Portal's React
  Native SDK.
---

# Perform swaps

{% hint style="info" %}
Please contact sales for additional pricing information on using the Swaps integrations at [partner@portalhq.io](mailto:partner@portalhq.io)
{% endhint %}

The `@portal-hq/swaps` package allows you to easily extend your existing `Portal` instance with swaps functionality.

## Installing Swaps support

Run the following in your project directory.

{% tabs %}
{% tab title="yarn" %}
```bash
yarn add @portal-hq/swaps
```
{% endtab %}

{% tab title="npm" %}
```bash
npm install --save @portal-hq/swaps
```
{% endtab %}
{% endtabs %}

## Initializing Swaps

{% hint style="info" %}
In order to initialize the Swaps class you need an existing `Portal` instance and a Swaps API Key. If you have not received a Swaps API Key, please contact support at [partner@portalhq.io](mailto:partner@portalhq.io).
{% endhint %}



```typescript
import Swaps from '@portal-hq/swaps'

const swaps = new Swaps('SWAPS-API-KEY', portal)
```

## Fetching a list of Sources

The `getSources()` function returns an object containing valid, swappable token sources that can be used with your Portal MPC Wallet. The existing `Portal` instance that was provided at initialization is used to scope the request to the current chain.&#x20;

In order to retrieve this list, you can use the following code.

```typescript
const fetchSources = async (): Record<string, string> => {
    return await swaps.getSources()
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
    sellAmount: .01,
}
```

### Using your QuoteArgs

Once you have a valid `QuoteArgs` object, you can call the `getQuote()` function.

```typescript
const fetchQuote = async (quoteArgs: QuoteArgs): QuoteResponse {
    return await swaps.getQuote(quoteArgs)
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
    await portal.provider.request({
        method: 'eth_sendTransaction',
        params: [transaction]
    })
}
```
