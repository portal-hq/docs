---
description: Once you have a Portal instance, you can now create a wallet for your user.
---

# Create a wallet

If you don't have a `Portal` instance already, we recommend reading [this page](./) first.

In the MPC wallet creation process, two sets of key shares are generated. The **signing key shares** are used for signing transactions, and the **backup key shares** are used for recovery if the device storing a signing key share is lost.

The **`portal.createWallet`** command initiates the MPC process to create a set of shares. The users' signing shares are automatically stored in the user's secure phone storage.

{% hint style="info" %}
Ensure you have created and connected the UI element in the storyboard to the action.
{% endhint %}

```swift
// Create the wallet.
// Obtain the Ethereum and Solana addresses of the user's new wallet.
let addresses = try await portal.createWallet()

print("My Portal EVM address: \(addresses.ethereum)")
print("My Portal Solana address: \(addresses.solana)")
```

{% hint style="warning" %}
Be sure to use a **Production** API key when creating clients for production. Read more details about going to production [here](../../resources/going-to-production.md).
{% endhint %}

{% hint style="danger" %}
**WARNING**: To create a wallet with the Portal SDK, your device must be configured to use passcode authentication. If you change your passcode, your Portal wallet will continue to operate as expected. However, if you disable passcode authentication after running the `generate` function, you will be required to execute the `recover` function before you can continue using your Portal wallet.
{% endhint %}

### Status Flow

```swift
case generatingShare = "Generating share"
case parsingShare = "Parsing share"
case storingShare = "Storing share"
case done = "Done"
```

Now that you've created your user's wallet, let's try to send a transaction with it!



**Related Documentation**

* [CreateWallet function reference](https://docs.portalhq.io/reference/ios/createwallet)
