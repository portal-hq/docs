---
description: >-
  The first step to the MPC process is generating key shares, or a wallet, for
  your users.
---

# Create a wallet

Once you have an instance of `Portal`, you can add logic to create web3 wallets.&#x20;

## The onReady hook

The `Portal` class dynamically loads a number of resources behind the scenes to enable MPC directly in the browser. For this reason, some functionality is not immediately available after initialization.&#x20;

We have include an `onReady` function to enable you to write your application logic in a more linear manner without having to explicitly poll the `ready` state of your `Portal` instance.

```typescript
portal.onReady(() => {
    // Your code
})
```

## Wallet Creation

The `createWallet` function initiates the MPC process to create a set of shares. The user's share and the wallet address is automatically stored in the browser's local storage, but hidden from the main browser context of your web application.&#x20;

```typescript
portal.onReady(async () => {
    const walletExists = await portal.doesWalletExist()

    if (!walletExists) {
        await portal.createWallet()
    }
    
    const ethAddress = await portal.getEip155Address()
    const solAddress = await portal.getSolanaAddress()
})
```

{% hint style="warning" %}
Be sure to use a **Production** API key when creating clients for production. Read more details about going to production [here](../../resources/going-to-production.md).
{% endhint %}

That's it! You now have an MPC Wallet built into your web application.
