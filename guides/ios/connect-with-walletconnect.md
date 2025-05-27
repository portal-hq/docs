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

The `PortalConnect` class is including in the `PortalSwift` pod, so no additional steps are required to start using `PortalConnect` in your app.

## Sharing the Portal instance with your ConnectViewController

In the ViewController that segues to your `ConnectViewController`, you'll need to set the `Portal` instance on the `destination` `ViewController`.&#x20;

```kotlin
override func prepare(for segue: UIStoryboardSegue, sender: Any?) {
    if let connectViewController = segue.destination as? ConnectViewController {
        connectViewController.portal = self.portal
    }
}
```

## Initializing PortalConnect

To initialize Portal Connect, call `portal.createPortalConnectInstance()`.

```swift
import PortalSwift

class ConnectViewController: UIViewController {
  private var connect: PortalConnect?
  public var portal: Portal?
  
  
  // UI Elements
  @IBOutlet weak var connectButton: UIButton!
  @IBOutlet weak var addressTextInput: UITextField!
  
  override func viewDidLoad() {
    super.viewDidLoad()
    
    connectButton.isEnabled = false
    connect = portal.createPortalConnectInstance()
  }
  
```

## Connecting via WalletConnect

To connect to a WalletConnect URI, using the mechanism that makes the most sense for your app, capture the WalletConnect URI. The most common practice is to use a QR Code scanner for this. Once you've captured the URI, you can call the `connect(uri)` function on your `PortalConnect` instance. This will initialize the WebSocket session with the WalletConnect relay and begin passing messages to your Portal Provider using your Portal MPC Wallet.

```kotlin
@IBAction func connectPressed() {
    let uri = addressTextInput.text
    connect?.connect(uri!)
}
```

Listen for the `connect` and `disconnect` events to get confirmation that you have successfully connected or if the user disconnects.

```swift
connect.on(event: Events.Connect.rawValue) { (data: Any) in } 
```

```swift
connect.on(event: Events.Disconnect.rawValue) { (data: Any) in }
```

Once this is done, you've got a connection to WalletConnect. All messages sent by the relay will now be processed by the Portal Provider.

{% hint style="info" %}
Wallet Connect's [Auth API ](https://docs.walletconnect.com/web3wallet/wallet-usage#auth-requests)is not currently supported. We have it on our roadmap. Please reach out if this is an urgent feature request.
{% endhint %}

## Handling Session Requests

`Session Requests` represent the initial connection request from the dApp to create a new session. These are triggered when Portal responds to the dApp after the `connect(uri)` function is called. These events will be triggered with either a `SessionProposal` object. These objects can be used to display information about the dApp the user is connecting to and the specific permissions being requested by the dApp.

### Accepting the dApps proposal

We have a helper method `addChainsToProposal` . This can be used to add all the chains in your gateway config to the proposal object.&#x20;

### Binding to Session Requests

In order to bind to Session Requests, add an event handler to your `PortalConnect` instance.

```swift
connect.on("portal_dappSessionRequested") { data in 
    // ... your business logic
    
    if (approved) {
        if var connectData = data as? ConnectData {
          var newConnectData = portalConnect.addChainsToProposal(data: connectData)
    
          portalConnect.emit(event: Events.PortalDappSessionApproved.rawValue, data: newConnectData)
        } else {
          print("Invalid data type. Expected ConnectData.")
        }
    } else {
        connect.emit("portal_dappSessionRejected", data)
    }
}
```

### Retrieve Session Request

Portal stores session requests for up to 24 hours. This feature can be useful if PortalConnect loses connection with the dApp being used and you want to respond to previous session requests. To retrieve a previous session request, use the `emitGetSessionRequest` method. You will need to provide both the `requestId` and the `topic`.

```swift
portalConnect.emitGetSessionRequest(
    requestId: "requestId",
    topic: "topic"
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

```swift
portalConnect.on(event: Events.PortalSigningRequested.rawValue) { (data: Any) in }
```

Listen for confirmation of signing requests

```swift
portalConnect.on(event: Events.PortalSignatureReceived.rawValue) { (data: Any) in }
```

## Handle Warnings and Errors

Set up a listener for `portal_connectError` in order to handle specific errors and warnings from Portal Connect. Check out our Portal Connect Error Codes [here](../../resources/error-codes/legacy-documentation/portal-connect-errors.md).

## Switching Chains

In order to switch the active chain for a portal connect instance use the `setChainId` method.

{% hint style="warning" %}
You must include that `chainId` in the gateway config with a gateway url on initialization of your Portal Object, otherwise, you can not switch chains.
{% endhint %}

```kotlin
connect.setChainId(137) // switches the active chain to polygon mainnet
```

## The final ConnectViewController class

Once you've gone through that process, you should have a file that looks something like this.

```swift
import PortalSwift

class ConnectViewController: UIViewController {
  private var connect: PortalConnect?
  public var portal: Portal?
  
  
  // UI Elements
  @IBOutlet weak var connectButton: UIButton!
  @IBOutlet weak var addressTextInput: UITextField!
  
  override func viewDidLoad() {
    super.viewDidLoad()
    
    connectButton.isEnabled = false
    connect = PortalConnect(portal!)
  }
  
  @IBAction func connectPressed() {
    let uri = addressTextInput.text
    connect?.connect(uri!)
  }
  
  connect.on("portal_dappSessionRequested") { data in 
      // ... your business logic
      
      if (approved) {
          connect.emit("portal_dappSessionApproved", data)
      } else {
          connect.emit("portal_dappSessionRejected", data)
      }
  }
  
  @IBAction func uriChanged(_ sender: Any) {
    let uri = addressTextInput.text
    
    connectButton.isEnabled =
      uri != nil
      && uri?.isEmpty == false
      && uri?.starts(with: "wc:") == true
  }
}

```
