---
description: >-
  Sets the password used for the Password backup method. This method configures
  the password that will be used to encrypt and decrypt wallet backups using
  password-based key derivation.
---

# setPassword

**Function Signature**

```swift
public func setPassword(
    _ value: String
) throws
```

**Parameters**

* `value`: The password string used to generate a key for backup encryption/decryption via password-based key derivation. This should be a secure password that the user can reliably remember or safely store.

**Throws**

* `MpcError.backupMethodNotRegistered` if the Password backup method has not been registered using `registerBackupMethod`

**Notes**

* Must be called before using the `.Password` backup method for wallet backup or recovery operations
* The password should be securely stored or provided by the user when needed
* Should be used in conjunction with registering the Password backup method

**Example Usage**

```swift
// Basic usage
do {
    try portal.setPassword("mySecurePassword123!")
} catch {
    print("Error setting password: \(error)")
}

// Complete setup with password backup
do {
    // First register the password storage method
    let passwordStorage = PasswordStorage()
    portal.registerBackupMethod(.Password, withStorage: passwordStorage)
    
    // Set the password
    try portal.setPassword("mySecurePassword123!")
    
    // Now you can use password-based backup
    let backup = try await portal.backupWallet(.Password) { status in
        switch status.status {
        case .readingShare:
            print("Reading share...")
        case .encryptingShare:
            print("Encrypting with password...")
        case .storingShare:
            print("Storing encrypted backup...")
        case .done:
            print("Backup complete!")
        default:
            break
        }
    }
    
    // Complete the backup process
    try await backup.storageCallback()
} catch {
    print("Error in password backup process: \(error)")
}

// Example with password recovery
do {
    // Set up password for recovery
    try portal.setPassword("mySecurePassword123!")
    
    // Recover wallet using password
    let recovered = try await portal.recoverWallet(
        .Password,
        withCipherText: backupCipherText
    ) { status in
        print("Recovery status: \(status)")
    }
    
    print("Recovered wallet with address: \(recovered.ethereum)")
} catch {
    print("Error in password recovery process: \(error)")
}
```

**Security Best Practices**

1. Use strong passwords that meet these criteria:
   * Minimum length of 12 characters
   * Mix of uppercase and lowercase letters
   * Include numbers and special characters
   * Avoid common patterns or personal information
2. Password Storage:
   * Never store the password in plain text
   * Consider using the iOS Keychain for secure storage
   * Implement appropriate password recovery mechanisms
3. User Experience:
   * Validate password strength during input
   * Provide clear feedback on password requirements
   * Consider implementing biometric authentication for accessing stored passwords

**Implementation Flow**

1. Register the Password backup method
2. Set the password using this method
3. Use the password for backup or recovery operations
4. Implement secure password storage and recovery

**Related Documentation**

For more information about password-based backup and security, see:

* [Password/Pin Backup](https://docs.portalhq.io/guides/ios/back-up-a-wallet/backup-methods#password-pin-backup)
