---
description: >-
  Easily use our EIP-1193 provider or custom built functions to make sending and
  signing transactions easier!
---

# Sign a transaction

## Signing

The `Portal` instance includes a number of helper functions to allow you to execute `Provider` requests without having to worry about encoding, request format, or what method to pass in your request. These helper functions allows you to easily sign and submit transactions, sign messages, and even sign typed data.

```typescript
const chainId = 'eip155:1' // Ethereum Mainnet
const myAddress = await portal.getEip155Address() // Your Ethereum address
const messageToSign = "0x48656c6c6f20576f726c64" // Message must be a hex string

const transactionHash = await portal.request({
    chainId,
    method: 'eth_sign',
    params: [myAddress, messageToSign],
})
```

## Raw Requests with the Provider

## Estimating Gas

By default, Portal will estimate and populate the `gas` property in a `transaction` object if the property is undefined.

To estimate the `gas` value manually use the `eth_estimateGas` RPC call and pass in your transaction as the parameter.

```javascript
const gas = await portal.request({
    chainId: 'eip155:1',
    method: 'eth_estimateGas',
    params: [
        // partial transaction to estimate gas for
        {
            from:"0xFROMADDRESS",
            to:"0xTOADDRESS",
            value:"0xVALUE",
            input:"HASH_OF_CONTRACT_CALL_DATA", // only needed if calling a smart contract
        }
    ]
})

console.log(gas)
// "0x5208"
```

## Using the Portal Provider with Third Party Libraries

The Portal Web3 Provider can also be used with third-party libraries just like any other Web3 Provider. This will enable you to interact with these libraries like normal, while utilizing Portal's MPC technology under the hood.

<pre class="language-typescript"><code class="lang-typescript"><strong>// Example with `ethers`
</strong><strong>const ethersProvider = new ethers.providers.Web3Provider(portal.provider);
</strong></code></pre>

And now you are signing transactions with Portal! 🙌 🚀 Next, we'll explore how to simulate a transaction so that you can create smoother experiences for your users.
