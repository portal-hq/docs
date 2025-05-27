---
description: >-
  Want to implement your own style, but with all the functionality Portal
  offers? Use these functions to implement your own custom web3 UI for your
  users.
---

# Sign a transaction

This example screen displays how the **Portal Provider** interacts with the **MPC wallet** and **the blockchain**.&#x20;

The **Provider** has an input box to set a `toAddress` for the transaction of sending 0.001 ETH from our **MPC wallet**.  The **Provider** then receives a _signed transaction_ from our **mobile MPC library** and submits that to chain using the configured RPC url.

{% hint style="warning" %}
Ensure you have set the gateway URL correctly with [Infura](https://www.infura.io/) or [Alchemy](https://www.alchemy.com/) when you initialize the `Portal` class.
{% endhint %}

{% code title="NativeSendTx.tsx" overflow="wrap" lineNumbers="true" %}
```typescript
import React, { FC, useState } from 'react'
import { Button, Text, TextInput, View } from 'react-native'
import { usePortal } from '@portal-hq/core'

const NativeSendTx = () => {
  const portal = usePortal()
  const [toAddress, setToAddress] = useState('')
  const [txHash, setTxHash] = useState('')

  const handleSend = async () => {
    try {
      // Build the transaction using the simplified API
      const { transaction } = await portal.api.buildTransaction(
        toAddress,    // recipient address
        'NATIVE',     // token type (native ETH)
        '0.001', // amount
        'sepolia'     // network
      )

      // Send the transaction
      const newTxHash = await portal.request({
        method: 'eth_sendTransaction',
        params: transaction,
        chainId: 'eip155:11155111'
      })

      setTxHash(newTxHash)
      setToAddress('')
    } catch (error) {
      console.error('Failed to send transaction:', error)
    }
  }
3
  return (
    <View>
      <Text>Send 0.001 ETH</Text>
      <TextInput
        onChangeText={setToAddress}
        placeholder="Enter recipient address"
        value={toAddress}
      />

      <View style={{ marginTop: 10 }}>
        <Button
          disabled={!portal || !toAddress || !toAddress.length}
          onPress={handleSend}
          title="Send ETH"
        />
      </View>

      {txHash && txHash.length > 0 && (
        <View>
          <Text>Successfully sent transaction!</Text>
          <Text>Transaction Hash: {txHash}</Text>
        </View>
      )}
    </View>
  )
}

export default NativeSendTx
```
{% endcode %}

## Signing a Solana Transaction

You can use our provider just like you do with Ethereum with Solana. Simply specify the method and the chainId for Solana. This example uses `solana/web3.js` to construct a transaction object to sign and pass to the multi-chain provider.&#x20;

#### Solana Provider Methods

* `sol_signMessage`
* `sol_signTransaction`
* `sol_signAndSendTransaction`
* `sol_signAndConfirmTransaction`

```typescript
import React, { useState } from 'react'
import { Button, Text, TextInput, View } from 'react-native'
import { usePortal } from '@portal-hq/core'

const SolanaSendTx = () => {
  const portal = usePortal()
  const [toAddress, setToAddress] = useState('')
  const [txHash, setTxHash] = useState('')

  const handleSend = async () => {
    try {
      const chainId = 'solana:EtWTRABZaYq6iMfeYKouRu166VU2xqa1' // Solana Devnet

      // Build the transaction using the simplified API
      const { transaction } = await portal.api.buildTransaction(
        toAddress,         // recipient address
        'NATIVE',          // token type (native SOL)
        '0.001',           // amount in SOL
        chainId            // CAIP-2 Chain, or friendly name like "solana-devnet"
      )

      // Send the transaction
      const newTxHash = await portal.request({
        method: 'sol_signAndSendTransaction',
        params: [transaction], // transaction is already base64 encoded
        chainId,
      })

      setTxHash(newTxHash)
      setToAddress('')
    } catch (error) {
      console.error('Failed to send transaction:', error)
    }
  }

  return (
    <View>
      <Text>Send 0.001 SOL</Text>
      <TextInput
        onChangeText={setToAddress}
        placeholder="Enter recipient address"
        value={toAddress}
      />

      <View style={{ marginTop: 10 }}>
        <Button
          disabled={!portal || !toAddress || !toAddress.length}
          onPress={handleSend}
          title="Send SOL"
        />
      </View>

      {txHash?.length > 0 && (
        <View>
          <Text>Successfully sent transaction!</Text>
          <Text>Transaction Hash: {txHash}</Text>
        </View>
      )}
    </View>
  )
}

export default SolanaSendTx
```

## Raw sign

You can now utilize our SDK to generate raw signatures that are generated using the underlying key share without adding any chain specific formatting to the signature. This effectively unlocks your ability to use the Portal SDK with any chain that uses `SECP256K1` or `ED25519`.&#x20;

```typescript
  const handleRawSign = async () => {
    try {
      // eslint-disable-next-line @typescript-eslint/no-unsafe-assignment, @typescript-eslint/no-unsafe-call
      const signedMessage = await portal!.rawSign(
        '74657374', // Must be in hex format, e.g. "test" is "74657374" in hex.
        'eip155:11155111', // choose the chainId eip155 = secp256k1, solana = ed25519
      )
      console.log('✅ Signed message', signedMessage)
    } catch (error) {
      console.error(error)
      console.log('❌ Failed to sign')
      throw error
    }
  }

```

## Estimating Gas

By default, Portal will estimate and populate the `gas` property in a `transaction` object if the property is undefined.

To estimate the `gas` value manually use the `eth_estimateGas` RPC call and pass in your transaction as the parameter.

```javascript
// Build a transaction to be sent
const transaction = {
  from: await portal.keychain.getAddress(),
  to: toAddress,
  value: BigNumber.from('1').toHexString(), // Example value
}

// Use the Portal Web3 Provider to make requests
const gas = await portal.request({
  method: 'eth_estimateGas',
  params: [transaction],
})

console.log(gas) // "0x5208"
```

And now you are signing transactions with Portal! 🚀

Next, we'll explore how to simulate a transaction so that you can create smoother experiences for your users.
