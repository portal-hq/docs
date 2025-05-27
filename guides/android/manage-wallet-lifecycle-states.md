---
description: >-
  Users can have multiple states in their wallet lifecycle: having a wallet,
  having wallet backups, having certain recovery methods available, and more.
---

# Manage wallet lifecycle states

After initializing Portal for your user, it's crucial to determine their wallet's lifecycle state to ensure a healthy wallet state. We provide several helper methods to easily derive the state of your users' wallets:

```kotlin
// Checks if the user has created a wallet on any device.
portal.doesWalletExist()

// Checks if the user's wallet share is on their current device.
portal.isWalletOnDevice()

// Checks if the user has backed up their wallet.
portal.isWalletBackedUp()

// Checks if the user can recover their wallet.
portal.isWalletRecoverable()

// Returns a list of available recovery methods based on the user's backups.
portal.availableRecoveryMethods()
```

These methods can be used together to understand the user's wallet state and determine the next steps. For example:

```kotlin
suspend fun manageWalletState(portal: Portal) {
    try {
        // Determine the wallet's state.
        let walletExists = portal.doesWalletExist()
        let walletExistsOnDevice = portal.isWalletOnDevice()
        let walletIsRecoverable = portal.isWalletRecoverable()
        
        if (!walletExists) {
            // Create and back up the wallet using portal.createWallet() and portal.backup().
        } else if (!walletExistsOnDevice) {
            if (walletIsRecoverable) {
                // Recover the wallet using portal.recover().
            } else {
                // Inform the user to back up the wallet on the original device.
            }
        } else {
            // The wallet is ready to use.
        }
    } catch(e: Exception) {
        // Handle any errors that occur.
    }
}
```

And that's it! You're now helping your users maintain a safe and secure experience with their wallets while using your apps.
