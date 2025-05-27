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

The `PortalConnect` class is included in the `portal-android` module, so no additional steps are required to start using `PortalConnect` in your app.

## Initializing

In order to initialize `PortalConnect` within your app, you'll need to have a custom Application instance to ensure you can share the `Portal` instance between Activities (Alternatively if you are using Dagger2 then you can add it as Singleton to your ApplicationComponent. Idea is that the Portal object should have the same lifecycle as your application). Once this is done, you can initialize `Portal` like normal in your `MainActivity`, and initialize `PortalConnect` within a `PortalConnectActivity.`

### The custom Application class

```kotlin
package com.example.app

import android.app.Application
import io.portalhq.android.Portal

class MyApplication: Application() {
    private lateinit var portal: Portal

    override fun onCreate() {
        super.onCreate();
    }

    fun getPortal(): Portal? {
        return portal
    }

    fun setPortal(portal: Portal) {
        this.portal = portal
    }
}
```

You'll also need to add the following property to the `application` node of your `AndroidManifest.xml`. This will register your application class as the class that is used when your app starts.

```xml
<application 
    android:name=".MyApplication" 
    ...
/>
```

## Initializing `Portal` in your Activity

In the Activity where you initialize `Portal`, you'll need to set the `Portal` instance on the Application.&#x20;

```kotlin
portal = Portal(
    "YOUR_CLIENT_API_KEY",
    YOUR_GDRIVE_INSTANCE,
    chainId,
    YOUR_KEYCHAIN_INSTANCE,
    mapOf(5 to "https://eth-$chain.g.alchemy.com/v2/$alchemyApiKey"),
    autoApprove = true,
)

(application as MyApplication).setPortal(portal)
```

To initialize Portal Connect, call `portal.createPortalConnectInstance()`.

```kotlin
package com.example.app

import androidx.appcompat.app.AppCompatActivity
import io.portalhq.android.Portal
import io.portalhq.android.connect.PortalConnect

class PortalConnectActivity() : AppCompatActivity() {
    private lateinit var portal: Portal
    private lateinit var connect: PortalConnect

    override fun onCreate(savedInstanceState: Bundle?) {
        super.onCreate(savedInstanceState)
        // Define portal
        portal = (application as MyApplication).getPortal()!!

        // Create PortalConnect
        connect = portal.createPortalConnectInstance()
    }
}
```

## Connecting via WalletConnect

To connect to a WalletConnect URI, using the mechanism that makes the most sense for your app, capture the WalletConnect URI. The most common practice is to use a QR Code scanner for this. Once you've captured the URI, you can call the `connect(uri)` function on your `PortalConnect` instance. This will initialize the WebSocket session with the WalletConnect relay and begin passing messages to your Portal Provider using your Portal MPC Wallet.

```kotlin
connectButton.setOnClickListener {
    val uri = wcUriTextInput.text.toString()
    connect.connect(uri)
}
```

Listen for the `connect` and `disconnect` events to get confirmation that you have successfully connected or if the user disconnects.

```kotlin
connect.on(ConnectEvents.Connect.value) { data: Any -> }
```

```kotlin
connect.on(ConnectEvents.Disconnect.value) { data: Any -> }
```

Once this is done, you've got a connection to WalletConnect. All messages sent by the relay will now be processed by the Portal Provider.

{% hint style="info" %}
Wallet Connect's [Auth API ](https://docs.walletconnect.com/web3wallet/wallet-usage#auth-requests)is not currently supported. We have it on our roadmap. Please reach out if this is an urgent feature request.
{% endhint %}

## Handling Session Requests

`Session Requests` represent the initial connection request from the dApp to create a new session. These are triggered when Portal responds to the dApp after the `connect(uri)` function is called. These events will be triggered with a `SessionProposal` object. These objects can be used to display information about the dApp the user is connecting to and the specific permissions being requested by the dApp.

### Accepting the dApps proposal

We have a helper method `addChainsToProposal` . This can be used to add all the chains in your gateway config to the proposal object.&#x20;

### Binding to Session Requests

In order to bind to Session Requests, add an event handler to your `PortalConnect` instance.

```kotlin
connect.on("portal_dappSessionRequested") { data: Any -> 
    // ... your business logic
    
    if (approved) {
        var connectData = data as SessionMessageData
        var newProposal = connect.addChainsToProposal(connectData)

        connect.emit(ConnectEvents.DappSessionApproved.value, newProposal)
    } else {
        connect.emit('portal_dappSessionRejected', data)
    }
}
```

### Retrieve Session Request

Portal stores session requests for up to 24 hours. This feature can be useful if PortalConnect loses connection with the dApp being used and you want to respond to previous session requests. To retrieve a previous session request, use the `emitGetSessionRequest` method. You will need to provide both the `requestId` and the `topic`.

```swift
connect.emitGetSessionRequest(
    "requestId",
    "topic"
)
```

{% hint style="warning" %}
**Important Notes:**

* You can only respond to a session request **once**.
* Ensure you have stored the correct **`requestId`** and **`topic`** values to successfully retrieve the session request.
{% endhint %}

This functionality helps maintain continuity in dApp interactions by allowing you to handle any outstanding session requests even after a connection disruption.

## Handle Signing

Each portal connect instance gets created with its own instance of the Portal Provider. This allows users to connect with different chains to different dApps.&#x20;

#### Listen for signing events

Handle signing approval. This is only required if `autoApprove` is turned off.&#x20;

```kotlin
connect.on(ConnectEvents.PortalSigningRequested.value) { }
```

Listen for confirmation of signing requests

```kotlin
connect.on(ConnectEvents.PortalSignatureReceived.value) { }
```

## Handle Warnings and Errors

Set up a listener for `portal_connectError` in order to handle specific errors and warnings from Portal Connect. Check out our Portal Connect Error Codes [here](../../resources/error-codes/legacy-documentation/portal-connect-errors.md).

## Switching Chains

In order to switch the active chain for a portal connect instance use the `updateChainId` method.

{% hint style="warning" %}
You must include that `chainId` in the gateway config with a gateway url on initialization of your Portal Object, otherwise, you can not switch chains.
{% endhint %}

```kotlin
connect.updateChainId(137) // switches the active chain to polygon mainnet
```

## The final PortalConnectActivity class

Once you've gone through that process, you should have a file that looks something like this.

```kotlin
package com.example.app

import android.widget.Button
import androidx.appcompat.app.AppCompatActivity
import io.portalhq.android.Portal
import io.portalhq.android.connect.PortalConnect

class PortalConnectActivity() : AppCompatActivity() {
    private lateinit var portal: Portal
    private lateinit var connect: PortalConnect
    
    // UI Elements
    private lateinit var connectButton: Button
    private lateinit var wcUriTextInput: EditText

    override fun onCreate(savedInstanceState: Bundle?) {
        super.onCreate(savedInstanceState)
        // Define portal
        portal = (application as MyApplication).getPortal()!!

        // Create PortalConnect
        connect = PortalConnect(portal)
        
        // Bind to Dapp Session Requests
        connect.on("portal_dappSessionRequested") { data: Any -> 
            // ... your business logic
            
            if (approved) {
                connect.emit("portal_dappSessionApproved", data)
            } else {
                connect.emit("portal_dappSessionRejected", data)
            }
        }
        
        // Bind to text change events
        wcUriTextInput.addTextChangedListener(object: TextWatcher {
            override fun onTextChanged(s: CharSequence?, start: Int, before: Int, count: Int) {
                connectButton.isEnabled = (s?.length ?: 0) > 0 && s?.startsWith("wc:") ?: false
            }
        })
        
        // Bind to connect action
        connectButton.setOnClickListener {
            val uri = wcUriTextInput.text.toString()
            connect.connect(uri)
        }
    }
}
```
