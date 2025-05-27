---
description: Follow this guide to integrate Portal into your Flutter App.
---

# Android

In order to integrate **Portal** into your **Flutter** App currently you will need to write custom platform-specific code using platform channels.\


<figure><img src="../../.gitbook/assets/PlatformChannels.png" alt=""><figcaption><p>Platform channels overview</p></figcaption></figure>

On the client side, [`MethodChannel`](https://api.flutter.dev/flutter/services/MethodChannel-class.html?_gl=1*4sycbc*_ga*MTE0MjAwMDc0MS4xNzI2MDk0ODMw*_ga_04YGWK0175*MTcyNjE2NTI2Ny4yLjEuMTcyNjE2NTQ3NS4wLjAuMA..) enables sending messages that correspond to method calls. On the platform side, `MethodChannel` on Android ([MethodChannelAndroid](https://api.flutter.dev/javadoc/io/flutter/plugin/common/MethodChannel.html?_gl=1*wlfjam*_ga*MTE0MjAwMDc0MS4xNzI2MDk0ODMw*_ga_04YGWK0175*MTcyNjIzODQ5NC4zLjAuMTcyNjIzODQ5NC4wLjAuMA..)) enable receiving method calls and sending back a result. These classes allow you to develop a platform plugin with very little 'boilerplate' code.\
\
You can check the Flutter official documentation for [platform channels](https://docs.flutter.dev/platform-integration/platform-channels).\
\
You can check our [Portal Flutter ](https://github.com/portal-hq/portal_flutter)example.

## Add Portal implementation for Android:

1. Open the Android host portion of your **Flutter** app in **Xcode**.
2. Add `Portal Android SDK` to the Android project, you can follow this [Android setup guide](https://docs.portalhq.io/guides/android).
3. Open `MainActivity.kt` located under **android > app > src > main > kotlin** in the project navigation.

The client and host sides of a channel are connected through a channel name passed in the channel constructor. All channel names used in a single app must be unique; prefix the channel name with a unique 'identifier', for example: `your.bundle.identifier/portal`. Below the channel name and some method names are defined.

```kotlin
object Constants {
    const val FLUTTER_CHANNEL_NAME = "your.bundle.identifier/portal"
    const val METHOD_INITIALIZE_PORTAL = "initializePortal"
    const val METHOD_CREATE_WALLET = "createWallet"
}
```

Now inside the `MainActivity.kt`, configure channel and methods handling like this.

```kotlin
class MainActivity : FlutterActivity() {
    private lateinit var portal: Portal
    override fun configureFlutterEngine(flutterEngine: FlutterEngine) {
        super.configureFlutterEngine(flutterEngine)

        MethodChannel(flutterEngine.dartExecutor.binaryMessenger, Constants.FLUTTER_CHANNEL_NAME)
            .setMethodCallHandler { call, result ->
                handleFlutterMethodCall(call, result)
            }
    }

    private fun handleFlutterMethodCall(call: MethodCall, result: MethodChannel.Result) {
        when (call.method) {
            Constants.METHOD_INITIALIZE_PORTAL -> initializePortal(call.arguments, result)
        }
    }

    private fun initializePortal(arguments: Any?, result: MethodChannel.Result) {
        if (arguments == null || arguments !is String || arguments.isBlank()) {
            result.error("FAILED", "Missing argument apiKey", null)
            return
        }

        try {
            portal = Portal(
                apiKey = arguments,
                featureFlags = FeatureFlags(isMultiBackupEnabled = true)
            )
            result.success("Portal initialized")
        } catch (e: Exception) {
            result.error("FAILED", e.message, null)
        }
    }
}
```

Then define a callback to handle any method invocation that comes from the Flutter side and they Switch on those Methods then execute the proper Kotlin implementation for each one and reply back to Flutter using a regular callback of type `FlutterResult` which is expected to be passed from Flutter side as a completion handler.



You will also need to define this implementation in your native iOS code as well for the code to work on both iOS and Android. Refer to [this guide](https://docs.portalhq.io/resources/flutter-ios) for iOS specific instructions.

### Calling platform-specific code using platform channels <a href="#example" id="example"></a>

Now Portal Android implementation is ready for Flutter, Let's create the Flutter platform client:\
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

