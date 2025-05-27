---
description: >-
  The first step to the MPC process is generating key shares, or a wallet, for
  your users.
---

# Create a wallet

In the MPC **wallet generation** process, two sets of key shares are generated. The **signing key shares** are used for signing transactions, and the **backup key shares** are used for recovery if the device storing a signing key shares is lost.

The **`portal.createWallet`** command initiates the MPC process to create a set of shares for each curve we support (for example, Ethereum uses curve SECP256K1 and Solana uses curve ED25519). The users' shares are automatically stored in the user's secure phone storage using your configured [mobile storage adapter](../../reference/react-native/storage-adapters/mobile-storage/).

{% code title="CreateWallet.tsx" overflow="wrap" lineNumbers="true" %}
```typescript
import React, { useState } from 'react'
import { Button, Text, View } from 'react-native';
import { usePortal } from '@portal-hq/core'


const CreateWallet = () => {
  const portal = usePortal()
  const [eip155Address, setEip155Address] = useState<string>('')
  const [solanaAddress, setSolanaAddress] = useState<string>('')
  
  const handleCreateWallet = async () => {
    // Create the user's wallet.
    const addresses = await portal.createWallet()
    setEip155Address(addresses.eip155)
    setSolanaAddress(addresses.solana)
  }

  return (
    <View>
      <Button onPress={handleCreateWallet} title="Create Your Wallet" />
      <Text>ETH Address: {eip155Address}</Text>
      <Text>SOL Address: {solanaAddress}</Text>
    </View>
  )
}

export default CreateWallet
```
{% endcode %}

{% hint style="warning" %}
Be sure to use a **Production** API key when creating clients for production. Read more details about going to production [here](../../resources/going-to-production.md).
{% endhint %}

## Choosing an Implementation

Now that you've generated a wallet, it's time to choose a Portal implementation for your app!

We provide [ready-to-use components](build-a-webview.md) that are able to be plugged in to your mobile app.

We also provide the functions that power those components for you to [build your own experience powered by Portal](sign-a-transaction.md).

{% hint style="danger" %}
**WARNING**: To create a wallet with the Portal SDK, your device must be configured to use passcode authentication. If you change your passcode, your Portal wallet will continue to operate as expected. However, if you disable passcode authentication after running the `createWallet` function, you will be required to execute the `recover` function before you can continue using your Portal wallet.
{% endhint %}

### Next Steps

Choose either the [components](build-a-webview.md) or [native](sign-a-transaction.md) implementation as the next step!
