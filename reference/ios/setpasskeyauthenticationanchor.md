---
description: >-
  Sets the presentation anchor for passkey authentication dialogs. This method
  is required for configuring where the passkey UI components will be displayed
  in your application.
---

# setPasskeyAuthenticationAnchor

**Function Signature**

```swift
@available(iOS 16, *)
public func setPasskeyAuthenticationAnchor(
    _ anchor: ASPresentationAnchor
) throws
```

**Parameters**

* `anchor`: The window anchor where passkey authentication UI will be presented. This is typically the main window of your application and should be of type `ASPresentationAnchor` (which UIWindow conforms to).

**Platform Availability**

* Requires iOS 16.0 or later
* Part of the native passkey implementation in iOS

**Notes**

* Must be called before using passkey backup or recovery methods
* Should be used in conjunction with `setPasskeyConfiguration` for complete setup
* The anchor is used to present the system's native passkey authentication dialog

**Example Usage**

```swift
// Basic usage in UIKit
if #available(iOS 16, *) {
    do {
        // Set the authentication anchor using the main window
        try portal.setPasskeyAuthenticationAnchor(view.window!)
    } catch {
        print("Error setting passkey authentication anchor: \(error)")
    }
}

// Complete setup in UIViewController
class MyViewController: UIViewController {
    func setupPasskeys() {
        if #available(iOS 16, *) {
            do {
                // Configure passkey settings
                try portal.setPasskeyConfiguration(
                    relyingParty: "portalhq.io",
                    webAuthnHost: "backup.web.portalhq.io"
                )
                
                // Set the authentication anchor
                guard let window = view.window else {
                    print("Error: No window available")
                    return
                }
                try portal.setPasskeyAuthenticationAnchor(window)
                
                print("Passkey authentication setup complete")
            } catch {
                print("Error setting up passkey authentication: \(error)")
            }
        }
    }
}

// Usage in SwiftUI
import SwiftUI

struct PasskeyView: View {
    @Environment(\.window) var window: UIWindow?
    
    func setupPasskeyAuth() {
        if #available(iOS 16, *) {
            do {
                guard let window = window else {
                    print("Error: No window available")
                    return
                }
                try portal.setPasskeyAuthenticationAnchor(window)
            } catch {
                print("Error setting passkey authentication anchor: \(error)")
            }
        }
    }
    
    var body: some View {
        Text("Passkey Setup")
            .onAppear {
                setupPasskeyAuth()
            }
    }
}
```

**Implementation Guide**

1. Ensure you're running on iOS 16 or later
2. Call `setPasskeyConfiguration` first to set up basic passkey settings
3. Obtain a reference to your app's main window or appropriate presentation context
4. Call this method with the window reference
5. Now you can use passkey-based authentication features

**UI Considerations**

* The passkey dialog will be presented as a system modal over your application
* The presentation anchor should be stable and available throughout the authentication process
* For best user experience, call this method when your view hierarchy is fully established

**Related Documentation**

For more information about passkey, see:

* [Passkey + Enclave Backup](https://docs.portalhq.io/guides/ios/back-up-a-wallet/backup-methods#passkey--enclave-backup)
* [Configuring an associated domain](https://developer.apple.com/documentation/xcode/configuring-an-associated-domain#Provide-an-Apple-App-Site-Association-file)
* [Supporting associated domains](https://developer.apple.com/documentation/xcode/supporting-associated-domains#Add-the-associated-domain-file-to-your-website)
* [Passkey Configuration](setpasskeyconfiguration.md)
