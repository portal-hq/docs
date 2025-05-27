---
description: >-
  This quick start guide will get you up and running with any of the Portal
  SDKs.
---

# SDK Quick Start

## Overview

Portal offers SDKs for Web, iOS, Android, and React Native. To install the chosen Portal SDK in your app, simply add the Portal package to your project.

{% tabs %}
{% tab title="Web" %}
```bash
yarn add @portal-hq/web
```
{% endtab %}

{% tab title="iOS" %}
### Using Swift Package Manager (SPM)

1. In Xcode, right-click on your project in the Project Navigator
2. Select "Add Packages..."
3. In the search field, enter either:
   * Package name: `PortalSwift`
   * Repository URL: `https://github.com/portal-hq/PortalSwift`
4. Select your desired version
5. Click "Add Package"

### Using CocoaPods

1. Add the following line to your `Podfile`:

```ruby
pod 'PortalSwift', :git => 'https://github.com/portal-hq/PortalSwift.git'
```

2. In Terminal, navigate to your project directory and run:

```bash
pod install
```

3. After installation:
   * Close your `.xcodeproj` file
   * Open the newly generated `.xcworkspace` file
   * ⚠️ Important: Always use the `.xcworkspace` file for development when using CocoaPods
{% endtab %}

{% tab title="Android" %}
Update the `dependencies` in your app level `build.gradle` to include the `portal-android` dependency

```gradle
implementation "io.portalhq.android:portal-android:X.X.X"
```
{% endtab %}

{% tab title="React Native" %}
The basic Portal setup consists of three packages:

* `@portal-hq/core` - The core Portal library.
* `@portal-hq/keychain` - An adapter for storing MPC signing shares on-device.
* `@portal-hq/gdrive-storage` - An adapter for storing MPC backup shares off-device.

These modules allow you to initialize `Portal` in your app.

```bash
yarn add @portal-hq/core @portal-hq/keychain @portal-hq/gdrive-storage
```
{% endtab %}
{% endtabs %}

## Authentication

The Portal SDK is initialized with a Client API Key or Client Session Token. You can get a test Client API Key from the [Portal Admin Dashboard](https://app.portalhq.io) in the **`Settings`** -> **`Test Client API Keys`** section. Simply click the **`New +`** button.

<figure><img src="../.gitbook/assets/Screenshot 2024-12-10 at 12.26.53 AM (1).png" alt=""><figcaption></figcaption></figure>

A modal will then be presented, allowing you to copy your test Client API Key. You can repeat this process as many times as you need to.

## Initializing Portal

To initialize **`Portal`** in your application, create a new instance of the **`Portal`** class included in the package dependency you added. You can provide your new test Client API Key as the **`apiKey`** argument during initialization. **`rpcConfig`** is a map of [CAIP-2 Chain IDs](https://docs.portalhq.io/resources/chain-id-formatting#caip-2-chain-id-format) to their respective RPC URLs.

Portal offers custom approval flows during the signing process, but for now, we can auto-approve all signatures with the **`autoApprove`** argument.

{% tabs %}
{% tab title="Web" %}
```typescript
import Portal from '@portal-hq/web'

const portal = new Portal({
    apiKey: 'YOUR_TEST_CLIENT_API_KEY',
    autoApprove: true,
    rpcConfig: {
      'eip155:11155111': 'YOUR-INFURA-OR-ALCHEMY-URL'
    },
})
```
{% endtab %}

{% tab title="iOS" %}
```swift
import PortalSwift

let portal = try Portal("YOUR_TEST_CLIENT_API_KEY", autoApprove: true)
```
{% endtab %}

{% tab title="Android" %}
```kotlin
import io.portalhq.android.Portal

val portal = Portal("YOUR_TEST_CLIENT_API_KEY", autoApprove = true)
```
{% endtab %}

{% tab title="React Native" %}
```typescript
import Portal from '@portal-hq/core'

const portal = new Portal({
    apiKey: 'YOUR_TEST_CLIENT_API_KEY',
    autoApprove: true,
    gatewayConfig: {
      ['eip155:1']: 'https://api.portalhq.io/rpc/v1/eip155/1',
      ['eip155:11155111']: 'https://api.portalhq.io/rpc/v1/eip155/11155111',
      ['solana:5eykt4UsFv8P8NJdTREpY1vzqKqZKvdp']: 'https://api.portalhq.io/rpc/v1/solana/5eykt4UsFv8P8NJdTREpY1vzqKqZKvdp',
      ['solana:EtWTRABZaYq6iMfeYKouRu166VU2xqa1']: 'https://api.portalhq.io/rpc/v1/solana/EtWTRABZaYq6iMfeYKouRu166VU2xqa1',
    },
})
```
{% endtab %}
{% endtabs %}

## Create a wallet

To create a new wallet, use the **`portal.createWallet()`** function. You can optionally provide a callback to receive [status updates](../guides/react-native/mpc-progress-callbacks.md) during wallet creation.

{% tabs %}
{% tab title="Web" %}
```typescript
const eip155Address = await portal.createWallet()

console.log(`My Portal EVM address: ${eip155Address}`)
console.log(`My Portal Solana address: ${await portal.getSolanaAddress()}`)
```
{% endtab %}

{% tab title="iOS" %}
```swift
let addresses = try await portal.createWallet()

print("My Portal EVM address: \(addresses.ethereum)")
print("My Portal Solana address: \(addresses.solana)")
```
{% endtab %}

{% tab title="Android" %}
```kotlin
val addresses = portal.createWallet()

println("My Portal EVM wallet: ${addresses.ethereumAddress}")
println("My Portal Solana wallet: ${addresses.solanaAddress}")
```
{% endtab %}

{% tab title="React Native" %}
```typescript
const addresses = await portal.createWallet()

console.log(`My Portal EVM address: ${addresses.eip155}`)
console.log(`My Portal Solana address: ${addresses.solana}`)
```
{% endtab %}
{% endtabs %}

## Receive testnet tokens

Now that you have a wallet, the next step is to get test tokens for it. You can fund your wallet using **`portal.receiveTestnetAsset`**. If you are looking for a greater variety of test tokens, we recommend exploring our [faucets page](../resources/testnet-faucets.md).

{% hint style="info" %}
The `chainId` will need to be a [CAIP-2](https://github.com/ChainAgnostic/CAIPs/blob/main/CAIPs/caip-2.md) compliant Chain ID. For more info on Chain ID formatting, see [this doc](../resources/chain-id-formatting.md).
{% endhint %}

{% tabs %}
{% tab title="Web" %}
```typescript
const chainId = "eip155:11155111" // Ethereum Sepolia

const params = {
  amount: "0.01", // You will receive 0.01 ETH
  token: "NATIVE" // Token, use "NATIVE" for the chain's native token
}

// Fund your Portal wallet
const response = await portal.receiveTestnetAsset(chainId, params)

console.log(`✅ Transaction hash: ${response.data.txHash}`)
```
{% endtab %}

{% tab title="iOS" %}
```swift
let chainId = "eip155:11155111" // Ethereum Sepolia

let params = FundParams(
  amount: "0.01", // You will receive 0.01 ETH
  token: "NATIVE" // Token, use "NATIVE" for the chain's native token
)

// Fund your Portal wallet
let response = portal.receiveTestnetAsset(chainId, params)

print("✅ Transaction hash: \(response.data.txHash)")
```
{% endtab %}

{% tab title="Android" %}
```kotlin
val chainId = "eip155:11155111" // Ethereum Sepolia

val params = FundParams(
  amount = "0.01", // You will receive 0.01 ETH
  token = "NATIVE" // Token, use "NATIVE" for the chain's native token
)

// Fund your Portal wallet
val response = portal.receiveTestnetAsset(chainId, params)

println("✅ Transaction hash: ${response.data.txHash}")
```
{% endtab %}

{% tab title="React Native" %}
```typescript
const chainId = "eip155:11155111" // Ethereum Sepolia

const params = {
  amount: "0.01", // You will receive 0.01 ETH
  token: "NATIVE" // Token, use "NATIVE" for the chain's native token
}

// Fund your Portal wallet
const response = await portal.receiveTestnetAsset(chainId, params)

console.log("✅ Transaction hash: ${response.data.txHash}")
```
{% endtab %}
{% endtabs %}

## Send tokens

Portal provides two ways to send transactions:

1. **`portal.sendAsset()`** - A simple method for sending tokens from your Portal wallet.
2. **`portal.request()`** - Direct access to the underlying web3 provider for custom transactions. (You can learn more about this method [here](../guides/react-native/sign-a-transaction.md).)

For most use cases, we recommend using **`portal.sendAsset()`** as shown in the examples below.

### EVM

{% tabs %}
{% tab title="Web" %}
```typescript
const chainId = "eip155:11155111" // Ethereum Sepolia

const params = {
  amount: "0.0001", // Sends 0.0001 ETH
  to: "0xDestinationAddress", // The recipient address
  token: "NATIVE" // Token, use "NATIVE" for the chain's native token
}

// Send the tokens
const txHash = await portal.sendAsset(chainId, params)

console.log(`✅ Transaction hash: ${txHash}`)
```
{% endtab %}

{% tab title="iOS" %}
```swift
let chainId = "eip155:11155111" // Sepolia

let params = SendAssetParams(
  amount: "0.0001", // Sends 0.0001 ETH
  to: "0xDestinationAddress", // The recipient address
  token: "NATIVE" // Token, use "NATIVE" for the chain's native token
)

// Send the tokens
let txHash = try await portal.sendAsset(chainId, params)

print("✅ Transaction hash: \(txHash)")
```
{% endtab %}

{% tab title="Android" %}
```kotlin
val chainId = "eip155:11155111" // Sepolia

val params = SendAssetParams(
  amount = "0.0001", // Sends 0.0001 ETH
  to = "0xDestinationAddress", // The recipient address
  token = "NATIVE" // Token, use "NATIVE" for the chain's native token
)

// Send the tokens
val txHash = portal.sendAsset(chainId, params)

println("✅ Transaction hash: ${txHash}")
```
{% endtab %}

{% tab title="React Native" %}
```typescript
const chainId = "eip155:11155111" // Ethereum Sepolia

const params = {
  amount: "0.0001", // Sends 0.0001 ETH
  to: "0xDestinationAddress", // The recipient address
  token: "NATIVE" // Token, use "NATIVE" for the chain's native token
}

// Send the tokens
const txHash = await portal.sendAsset(params.to, params.token, params.amount, chainId)

console.log(`✅ Transaction hash: ${txHash}`)
```
{% endtab %}
{% endtabs %}

{% hint style="warning" %}
If your Portal client is using [Account Abstraction](https://docs.portalhq.io/resources/account-abstraction), then **`txHash`** is actually a **User Operation hash**. You can manually look up the user operation hash [here](https://jiffyscan.xyz/?network=sepolia).
{% endhint %}

### Solana

{% hint style="info" %}
You will need **`SOL`** to submit a Solana transaction, which is not currently supported by **`portal.receiveTestnetAsset`**. You can find a faucet to get test **`SOL`** tokens [here](../resources/testnet-faucets.md).
{% endhint %}

{% tabs %}
{% tab title="Web" %}
```typescript
const chainId = "solana:EtWTRABZaYq6iMfeYKouRu166VU2xqa1"; // Solana Devnet

const params = {
  amount: "0.0001", // Sends 0.0001 SOL
  to: "0xDestinationAddress", // The recipient address
  token: "NATIVE" // Token, use "NATIVE" for the chain's native token
}

// Send the tokens
const txHash = await portal.sendAsset(chainId, params)

console.log(`✅ Transaction hash: ${txHash}`)
```
{% endtab %}

{% tab title="iOS" %}
```swift
let chainId = "solana:EtWTRABZaYq6iMfeYKouRu166VU2xqa1" // Solana Devnet

let params = SendAssetParams(
  amount: "0.0001", // Sends 0.0001 SOL
  to: "0xDestinationAddress", // The recipient address
  token: "NATIVE" // Token, use "NATIVE" for the chain's native token
)

// Send the tokens
let response = try await portal.sendAsset(chainId, params)

print("✅ Transaction hash: \(response.result)")
```
{% endtab %}

{% tab title="Android" %}
```kotlin
val chainId = "solana:EtWTRABZaYq6iMfeYKouRu166VU2xqa1" // Solana Devnet

val params = SendAssetParams(
  amount = "0.0001", // Sends 0.0001 SOL
  to = "0xDestinationAddress", // The recipient address
  token = "NATIVE" // Token, use "NATIVE" for the chain's native token
)

// Send the tokens
val txHash = portal.sendAsset(chainId, params)

print("✅ Transaction hash: \(txHash)")
```
{% endtab %}

{% tab title="React Native" %}
```typescript
const chainId = "solana:EtWTRABZaYq6iMfeYKouRu166VU2xqa1"; // Solana Devnet

const params = {
  amount: "0.0001", // Sends 0.0001 SOL
  to: "0xDestinationAddress", // The recipient address
  token: "NATIVE" // Token, use "NATIVE" for the chain's native token
}

// Send the tokens
const txHash = await portal.sendAsset(params.to, params.token, params.amount, chainId)

console.log(`✅ Transaction hash: ${txHash}`)
```
{% endtab %}
{% endtabs %}

***

You just created a Portal client, created their wallet, accessed their addresses, received testnet tokens, and sent tokens on both Ethereum Sepolia and Solana Devnet! :tada:

Next we recommend going through the [complete SDK guides](../guides/ios/) where we go into much more detail.
