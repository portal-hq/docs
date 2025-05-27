---
description: >-
  Configures the passkey authentication settings for backup and recovery
  operations. This method must be called before using passkey backup or recovery
  methods.
---

# setPasskeyConfiguration

**Function Signature**

```swift
@available(iOS 16, *)
public func setPasskeyConfiguration(
    relyingParty: String,
    webAuthnHost: String
) throws
```

**Parameters**

* `relyingParty`: The relying party identifier for WebAuthn/passkey authentication. This is typically your application's domain name (e.g., "portalhq.io")
* `webAuthnHost`: The WebAuthn host that will handle passkey operations. This should match your application's authentication server.

**Platform Availability**

* Requires iOS 16.0 or later
* Uses the native passkey APIs introduced in iOS 16

**Notes**

* Must be called before using passkey backup or recovery methods
* The relying party should match your application's domain name for security purposes
* Should be used in conjunction with `setPasskeyAuthenticationAnchor` for complete setup

**Example Usage**

```swift
// Basic configuration
do {
    try portal.setPasskeyConfiguration(
        relyingParty: "portalhq.io",
        webAuthnHost: "backup.web.portalhq.io"
    )
} catch {
    print("Error configuring passkey authentication: \(error)")
}

// Complete setup with authentication anchor
if #available(iOS 16, *) {
    do {
        // Configure passkey settings
        try portal.setPasskeyConfiguration(
            relyingParty: "portalhq.io",
            webAuthnHost: "backup.web.portalhq.io"
        )
        
        // Set the authentication anchor (typically your main window)
        try portal.setPasskeyAuthenticationAnchor(view.window!)
        
        // Now you can use passkey-based backup/recovery
        try await portal.backupWallet(.Passkey) { status in
            print("Backup status: \(status)")
        }
    } catch {
        print("Error setting up passkey authentication: \(error)")
    }
}
```

**Implementation Flow**

1. Check iOS version compatibility
2. Configure passkey settings using this method
3. Set the authentication anchor using `setPasskeyAuthenticationAnchor`
4. Use passkey-based backup or recovery methods

**Security Considerations**

* The relying party identifier is crucial for security and should match your app's domain
* Passkeys provide a secure, phishing-resistant alternative to passwords
* Each passkey is unique to the combination of user account, relying party, and device

**Related Documentation**

For more information about passkey integration, see:

* [Passkey + Enclave Backup](https://docs.portalhq.io/guides/ios/back-up-a-wallet/backup-methods#passkey--enclave-backup)
* [Configuring an associated domain](https://developer.apple.com/documentation/xcode/configuring-an-associated-domain#Provide-an-Apple-App-Site-Association-file)
* [Supporting associated domains](https://developer.apple.com/documentation/xcode/supporting-associated-domains#Add-the-associated-domain-file-to-your-website)
* [Passkey Authentication Anchor](setpasskeyauthenticationanchor.md)
