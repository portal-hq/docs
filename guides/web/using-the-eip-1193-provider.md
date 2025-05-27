---
description: >-
  portal.provider is compliant with the EIP-1193 specification and can be used
  with libraries like ethers.js and web3.js. This guide will walk you through
  using portal.provider with ethers.js.
---

# Using the EIP-1193 Provider

Here's a code snippet that sends a transaction with `ethers.js` backed by `portal.provider`.

```typescript
// update portal to use your chain of choice 
portal.updateChain('<chainReference>')

// get ethers.js Signer backed by portal.provider
const ethersProvider = new ethers.BrowserProvider(portal.provider)
const ethersSigner = await ethersProvider.getSigner()

// Send the transaction
const tx = await ethersSigner.sendTransaction({
  to: '<address>',
  value: ethers.parseEther('0.001'),
})

// wait for the transaction to be mined
const receipt = await tx.wait()
```

Let's break it down.

First, `portal.updateChain` is used to bind `portal.provider` to a specific EVM chain. It takes the chain's [CAIP-2](../../resources/chain-id-formatting.md) reference, for example `11155111` for Sepolia, as argument.

`portal.provider` is then supplied to [ethers.js `BrowserProvider`](https://docs.ethers.org/v6/api/providers/#BrowserProvider). The returned object is an [ethers.js `Provider`](https://docs.ethers.org/v6/api/providers/#Provider) and [supports read-only requests](https://docs.ethers.org/v6/getting-started/#starting-connecting) to the chain. To be able to send a transaction we need a [`Signer`](https://docs.ethers.org/v6/api/providers/#Signer) which `ethersProvider.getSigner` returns.

The next bit of code is `ethers.js` specific and uses `ethersSigner.sendTransaction` to send 0.001 ETH to the address specified. The code also waits for the transaction to be mined.

That's all! We've successfully used `portal.provider` and `ethers.js` to send a transaction!
