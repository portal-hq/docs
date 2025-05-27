---
description: >-
  Portal Connect allows you to connect your Portal MPC Wallet to any dApp that
  supports WalletConnect without extra dependency bloat.
---

# Connect with WalletConnect

Portal Connect utilizes your existing `Portal` instance to connect your Portal MPC Wallet to dApps via WalletConnect. When connecting via Portal Connect, a few things happen in the background:

1. You provide a WalletConnect `uri` for Portal Connect to connect with
2. Portal Connect opens a WebSocket connection to the WalletConnect `uri`
3. All requests received over the WebSocket are routed to `portal.provider` to perform the request like normal

This workflow allows for seamless integration between your Portal MPC Wallet and countless dApps that already include support for WalletConnect.

## Installing

In order to use Portal Connect, you'll need to install the `@portal-hq/connect` package in your project.

{% tabs %}
{% tab title="yarn" %}
```bash
yarn add @portal-hq/connect
```
{% endtab %}

{% tab title="npm" %}
```bash
npm install --save @portal-hq/connect
```
{% endtab %}
{% endtabs %}

_Note: This package does not negate the need to install other Portal dependencies, namely `@portal-hq/core`. Portal Connect depends on an existing `Portal` instance to operate properly._

## Initializing

To initialize Portal Connect, import and create an instance of `PortalConnect`. The `PortalConnect` class takes one argument, the `Portal` instance you'd like it to use under the hood.

```typescript
import { useEffect, useState } from 'react'
import { usePortal } from '@portal-hq/core'
import PortalConnect from '@portal-hq/connect'

const ConnectScreen = () => {
    // This assumes that you are utilizing the 
    // `PortalContextProvider` within your app
    const portal = usePortal()
    const [portalConnect, setPortalConnect] = useState<PortalConnect>()
    
    useEffect(() => {
        if (portal) {
            const portalConnect = PortalConnect(portal)
            setPortalConnect(portalConnect)
        }
    }, [portal])
    
    return (
        <>
            {/** The content of your view goes here */}
        </>
    )
}

export default ConnectScreen
```

## Connecting via WalletConnect

To connect to a WalletConnect URI, using the mechanism that makes the most sense for your app, capture the WalletConnect URI. The most common practice is to use a QR Code scanner for this. Once you've captured the URI, you can call the `connect(uri)` function on your `PortalConnect` instance. This will initialize the WebSocket session with the WalletConnect relay and begin passing messages to your Portal Provider using your Portal MPC Wallet.

```typescript
const [uri, setUri] = useState<string>('')

// ...

const handleConnect = async () => {
    if (uri.length > 0) {
        await portalConnect.connect(uri)
    }
}

// ...

return (
    <>
        {/** 
            This is an example of how a QR Code scanner might work. 
            Once you've chosen a QR Code scanner library, please 
            refer to the docs for that library for details on how to 
            implement components and capture scanned values. 
        */}
        <QRCodeScanner onQrCode={(text) => setUri(text)} />
        <Button onPress={handleConnect} title="Connect" />
    </>
)
```

Listen for the `connect` and `disconnect` events to get confirmation of connecting and disconnecting.&#x20;

```typescript
connect.on('connect', (event: Record<string, any>) => { }

connect.on('disconnect', (event: Record<string, any>) => { }
```

Once this is done, you've got a connection to WalletConnect. All messages sent by the relay will now be processed by the Portal Provider.

{% hint style="info" %}
Wallet Connect's [Auth API](https://docs.walletconnect.com/web3wallet/wallet-usage#auth-requests) is not currently supported. We have it on our roadmap. Please reach out if this is an urgent feature request.
{% endhint %}

## Handling Session Requests

`Session Requests` represent the initial connection request from the dApp to create a new session. These are triggered when Portal responds to the dApp after the `connect(uri)` function is called. These events will be triggered with a `SessionProposal` object. These objects can be used to display information about the dApp the user is connecting to and the specific permissions being requested by the dApp.

### Accepting the dApps proposal

We have a helper method `addChainsToProposal` . This can be used to add all the chains in your gateway config to the proposal object.&#x20;

### Binding to Session Requests

In order to bind to Session Requests, add an event handler to your `PortalConnect` instance.

```typescript
connect.on('portal_dappSessionRequested', (data: SessionProposalOrMetadata) => {
    // ... your business logic
    
    if (approved) {
        data = connector.addChainsToProposal(data)
        connect.emit('portal_dappSessionApproved', data)
    } else {
        connect.emit('portal_dappSessionRejected', data)
    }
})
```

## Handle Signing

Each portal connect instance gets created with its own instance of the Portal Provider. This allows users to connect with different chains to different dApps.&#x20;

#### Listen for signing events

Handle signing approval. This is only required if `autoApprove` is turned off.&#x20;

```typescript
connect.on('portal_signingRequested', (data: SigningRequestArguments) => { }

```

Listen for confirmation of signing requests

```typescript
connect.on('portal_signatureReceived', () => { }
```

## Handle Warnings and Errors

Set up a listener for `portal_connectError` in order to handle specific errors and warnings from Portal Connect. Check out our Portal Connect Error Codes [here](../../resources/error-codes/legacy-documentation/portal-connect-errors.md).

## Switching Chains

In order to switch the active chain for a portal connect instance use the `setChainId` method.&#x20;

{% hint style="warning" %}
You must include that `chainId` in the gateway config with a gateway url on initialization of your Portal Object, otherwise, you can not switch chains.
{% endhint %}

```typescript
connect.setChainId(137) // switches the active chain to polygon mainnet
```

## The final file

Once you've gone through that process, you should have a file that looks something like this.

```typescript
import { useEffect, useState } from 'react'
import { usePortal } from '@portal-hq/core'
import PortalConnect from '@portal-hq/connect'

const ConnectScreen = () => {
    // This assumes that you are utilizing the 
    // `PortalContextProvider` within your app
    const portal = usePortal()
    const [portalConnect, setPortalConnect] = useState<PortalConnect>()
    const [uri, setUri] = useState<string>('')
    
    const handleConnect = async () => {
        if (uri.length > 0) {
            await portalConnect.connect(uri)
        }
    }
    
    useEffect(() => {
        if (portal) {
            const portalConnect = PortalConnect(portal)
            setPortalConnect(portalConnect)
        }
        
        connect.on('portal_dappSessionRequested', (data: SessionProposalOrMetadata) => {
            // ... your business logic
            
            if (approved) {
                connect.emit('portal_dappSessionApproved', data)
            } else {
                connect.emit('portal_dappSessionRejected', data)
            }
        })
        
    }, [portal])
    
    return (
        <>
            {/** 
                This is an example of how a QR Code scanner might work. 
                Once you've chosen a QR Code scanner library, please 
                refer to the docs for that library for details on how to 
                implement components and capture scanned values. 
            */}
            <QRCodeScanner onQrCode={(text) => setUri(text)} />
            <Button onPress={handleConnect} title="Connect" />
        </>
    )
}

export default ConnectScreen
```
