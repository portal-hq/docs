---
description: Follow this guide to integrate Portal into your Flutter App.
---

# iOS

In order to integrate with **Portal** into your **Flutter** App currently you will need to write custom platform-specific code using platform channels.\


<figure><img src="../../.gitbook/assets/PlatformChannels.png" alt=""><figcaption><p>Platform channels overview</p></figcaption></figure>

On the client side, [`MethodChannel`](https://api.flutter.dev/flutter/services/MethodChannel-class.html?_gl=1*4sycbc*_ga*MTE0MjAwMDc0MS4xNzI2MDk0ODMw*_ga_04YGWK0175*MTcyNjE2NTI2Ny4yLjEuMTcyNjE2NTQ3NS4wLjAuMA..) enables sending messages that correspond to method calls. On the platform side, `MethodChannel` on Android ([MethodChannelAndroid](https://api.flutter.dev/javadoc/io/flutter/plugin/common/MethodChannel.html?_gl=1*wlfjam*_ga*MTE0MjAwMDc0MS4xNzI2MDk0ODMw*_ga_04YGWK0175*MTcyNjIzODQ5NC4zLjAuMTcyNjIzODQ5NC4wLjAuMA..)) and `FlutterMethodChannel` on iOS ([MethodChanneliOS](https://api.flutter.dev/ios-embedder/interface_flutter_method_channel.html?_gl=1*15n5mqb*_ga*MTE0MjAwMDc0MS4xNzI2MDk0ODMw*_ga_04YGWK0175*MTcyNjIzODQ5NC4zLjEuMTcyNjIzODYxNC4wLjAuMA..)) enable receiving method calls and sending back a result. These classes allow you to develop a platform plugin with very little 'boilerplate' code.\
\
You can check the Flutter official documentation for [platform channels](https://docs.flutter.dev/platform-integration/platform-channels).\
\
You can check our [Portal Flutter ](https://github.com/portal-hq/portal_flutter)example.

## Add Portal implementation for iOS:

1. Open the iOS host portion of your **Flutter** app in **Xcode**.
2. Add `PortalSwift` to the iOS project, you can follow this [#setup](../../guides/ios/#setup "mention").
3. Open `AppDelegate.swift` located under **Runner** > **Runner** in the project navigation.

Override the `application:didFinishLaunchingWithOptions:` function and create a `FlutterMethodChannel`

The client and host sides of a channel are connected through a channel name passed in the channel constructor. All channel names used in a single app must be unique; prefix the channel name with a unique 'identifier', for example: `your.bundle.identifier/portal`.

```swift
@UIApplicationMain
@objc class AppDelegate: FlutterAppDelegate {
  override func application(
    _ application: UIApplication,
    didFinishLaunchingWithOptions launchOptions: [UIApplication.LaunchOptionsKey: Any]?) -> Bool {

    let controller : FlutterViewController = window?.rootViewController as! FlutterViewController
    let portalChannel = FlutterMethodChannel(name: "your.bundle.identifier/portal",
                                              binaryMessenger: controller.binaryMessenger)
    
    setCallHandler(channel: portalChannel)

    GeneratedPluginRegistrant.register(with: self)
    return super.application(application, didFinishLaunchingWithOptions: launchOptions)
  }
}
```

Then define a callback to handle any method invocation that comes from the Flutter side and they Switch on those Methods then execute the proper Swift implementation for each one and reply back to Flutter using a regular callback of type `FlutterResult` which expected to be passed from Flutter side as a completion handler.

#### Here is an example implementation:

```swift
private func setCallHandler(
    channel: FlutterMethodChannel
) {
    channel.setMethodCallHandler { [weak self] (call: FlutterMethodCall, result: @escaping FlutterResult) in
        Task {
            switch call.method {
            case "initializePortal":
                if let apiKey = call.arguments as? String {
                    PortalWrapper.initializePortal(apiKey: apiKey, result: result)
                } else {
                    result(FlutterError(code: "INVALID_ARGUMENT", message: "Expected a String for apiKey", details: nil))
                }
                
            case "createWallet":
                PortalWrapper.createWallet(result: result)
                
            // TODO: - Handle all the messages here.

            default:
                result(FlutterMethodNotImplemented)
            }
        }
    }
}

```

\
And here is an `PortalWrapper` implementation:

```swift
import Flutter
import PortalSwift

class PortalWrapper {
    static var portal: Portal?
    
    static func initializePortal(apiKey: String, result: @escaping FlutterResult) {
        // Initialize portal here using the portal iOS SDK
        Task {
            do {
                portal = try Portal(
                    apiKey,
                    withRpcConfig: [
                        "solana:5eykt4UsFv8P8NJdTREpY1vzqKqZKvdp" : "https://api.mainnet-beta.solana.com",
                        "solana:EtWTRABZaYq6iMfeYKouRu166VU2xqa1": "https://api.devnet.solana.com"
                    ]
                )
                
                result([
                    "success": true,
                    "message": "Portal initialized"
                ])
            } catch {
                result(FlutterError(code: "FAILED",
                                    message: "Error registering portal with exception: \(error.localizedDescription)",
                                    details: nil))
            }
        }
    }
    
    static func createWallet(result: @escaping FlutterResult) {
        // Create wallet here using the portal iOS SDK
        guard let portal = portal else {
            result(FlutterError(code: "UNAVAILABLE",
                                message: "Portal is not initialized",
                                details: nil))
            return
        }
        
        Task {
            do {
                let (ethereum, solana) = try await portal.createWallet()
                
                result([
                    "success": true,
                    "addresses": [
                        "ethereum": ethereum,
                        "solana": solana
                    ]
                ])
            } catch {
                result(FlutterError(code: "FAILED",
                                    message: "Error creating portal wallet with exception: \(error.localizedDescription)",
                                    details: nil))
            }
        }
    }
}
```



### Calling platform-specific code using platform channels <a href="#example" id="example"></a>

Now Portal iOS implementation is ready for Flutter, Let's create the Flutter platform client:\
We will use the same channel name we defined above to initialize `MethodChannel`

```dart
static const platform = MethodChannel('your.bundle.identifier/portal');
```

Next, invoke a method on the method channel, specifying the concrete method to call using the `String` identifier `initializePortal`. The call might fail, so wrap the `invokeMethod` call in a try-catch statement.

```dart
Future<void> initializePortal(String apiKey) async {
  String message;
  try {
    final result = await platform.invokeMethod('initializePortal', apiKey);
    message = 'Success with message ${result?['message']}';
  } on PlatformException catch (e) {
    message = "Failed to initialize portal with error: '${e.message}'.";
  }
}
```

Finally, your Flutter function `initializePortal` to initialize portal is ready to be called. You can call it from Flutter side passing the `apiKey to initialize Portal.`

