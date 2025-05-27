# iOS

Portal provides MPC **wallets** and dApp **connections** for organizations and their users.\
\
To integrate Portal, an organization adds a **client library** to their mobile app and a few **server API endpoints**.

![The Portal library connects your mobile app and server with the web3 ecosystem.](<../../.gitbook/assets/image (18).png>)

## Installation

We support Cocoa Pods and Swift Package Manager as distribution methods for our swift package.

### Setup&#x20;

{% tabs %}
{% tab title="Swift Package Manager" %}
Add the `PortalSwift` package through Xcode:

1. In Xcode, right-click on your project in the Project Navigator
2. Select "Add Packages..."
3. In the search field, enter either:
   * Package name: `PortalSwift`
   * Repository URL: `https://github.com/portal-hq/PortalSwift`
4. Select your desired version
5. Click "Add Package"

You can read more on adding SPM packages [here](https://developer.apple.com/documentation/xcode/adding-package-dependencies-to-your-app).
{% endtab %}

{% tab title="Cocoa Pods" %}
1. Add the following line to your `Podfile`:

```ruby
pod 'PortalSwift', :git => 'https://github.com/portal-hq/PortalSwift.git'
```

1. In Terminal, navigate to your project directory and run:

```bash
pod install
```

{% hint style="info" %}
_If you are running an xcode project and need to run `pod init` and run into ruby or xcode errors, ensure your xcode project is compatible with an xcode version lower than 14._&#x20;
{% endhint %}

1. After installation:
   * Close your `.xcodeproj` file
   * Open the newly generated `.xcworkspace` file
   * ⚠️ Important: Always use the `.xcworkspace` file for development when using CocoaPods
{% endtab %}
{% endtabs %}

## Initializing Portal

To initialize `Portal` in your application, create a new instance of the `Portal` class included in the package dependency you added. You can provide your Client API Key as the `apiKey` argument during initialization. `rpcConfig` is a map of [CAIP-2 Chain IDs](https://docs.portalhq.io/resources/chain-id-formatting#caip-2-chain-id-format) to their respective RPC URLs.

We can now create an instance of the `Portal` class. Below is an example of how you can do this:

```swift
import PortalSwift

let portal = try Portal(
  "CLIENT_API_KEY_OR_CLIENT_SESSION_TOKEN", 
   withRpcConfig: [
        "eip155:1": "https://eth-mainnet.g.alchemy.com/v2/\(alchemyApiKey)",
        "eip155:11155111": "https://eth-sepolia.g.alchemy.com/v2/\(alchemyApiKey)",
        "solana:5eykt4UsFv8P8NJdTREpY1vzqKqZKvdp": "https://api.mainnet-beta.solana.com",
        "solana:EtWTRABZaYq6iMfeYKouRu166VU2xqa1": "https://api.devnet.solana.com",
    ]
)
```

Now that we have our Portal instance, the next step is to generate wallets for your user. Let's create them!

{% hint style="info" %}
If you are using [Client Session Tokens (CSTs)](../../resources/authentication-and-api-keys.md), this hint is for you.

When your user's CST expires, all Portal SDKs will throw an error on the next MPC Operation the user makes (e.g. creating a wallet, backing up a wallet, recovering a wallet, or signing). That error will include a code **`SESSION_EXPIRED`** in the SDK methods, which you can use as an indicator to refresh your CST.
{% endhint %}

