---
description: Registers a storage implementation for a specific backup method.
---

# registerBackupMethod

{% hint style="info" %}
You can provide the backup methods when initializing the Portal main object.
{% endhint %}

**Function Signature**

```swift
public func registerBackupMethod(
    _ method: BackupMethods, 
    withStorage: PortalStorage
)
```

**Parameters**

* `method`: The backup method to register. Supported methods:
  * `.GoogleDrive`: Google Drive storage
  * `.iCloud`: iCloud storage
  * `.Password`: Password-protected storage
  * `.Passkey`: Passkey authentication storage
  * `.local`: Local storage
  * `.Unknown`: Can be used for custom storage
* `withStorage`: A custom implementation of `PortalStorage` protocol that handles the storage operations for the specified backup method

**Notes**

* Each backup method must have a registered storage implementation before it can be used for backup or recovery operations
* This method should be called before attempting any backup or recovery operations with the corresponding method

**Example Usage**

```swift
// Example 1: Registering a custom password storage
class MyCustomPasswordStorage: PortalStorage {
    // Custom implementation of PortalStorage protocol
}

do {
    let customStorage = MyCustomPasswordStorage()
    portal.registerBackupMethod(.Password, withStorage: customStorage)
} catch {
    print("Error registering backup method: \(error)")
}

// Example 2: Registering iCloud storage
do {
    let iCloudStorage = ICloudStorage(mobile: binaryWrapper)
    portal.registerBackupMethod(.iCloud, withStorage: iCloudStorage)
} catch {
    print("Error registering iCloud storage: \(error)")
}
```

**Custom Storage Implementation**

When implementing a custom storage solution, your class must conform to the `PortalStorage` protocol:

```swift
protocol PortalStorage {
  var api: PortalApiProtocol? { get set }
  var encryption: PortalEncryptionProtocol { get }

  func decrypt(_ value: String, withKey: String) async throws -> String
  func delete() async throws -> Bool
  func encrypt(_ value: String) async throws -> EncryptData
  func read() async throws -> String
  func validateOperations() async throws -> Bool
  func write(_ value: String) async throws -> Bool
}
```

**Related Documentation**

For more information about backup methods, see:

* [Backup Methods Overview](../../guides/ios/back-up-a-wallet.md)
