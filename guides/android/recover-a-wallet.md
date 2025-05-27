---
description: >-
  This guide will walk you through how to use your users' backups to recover
  their wallet.
---

# Recover a wallet

## Portal-Managed Recovery (default)

When using Portal-Managed Backups, you can simply call the `portal.recoverWallet()` function to generate new signing shares on your user's device.

```typescript
// With Password backup
val backupConfigs = BackupConfigs(PasswordStorageConfig(password = PASSWORD))
portal.recoverWallet(BackupMethods.password, backupConfigs) { status ->
    // (Optional) Get status updates on the recovery operation
}

// With GDrive backup
portal.recoverWallet(BackupMethods.Gdrive) { status ->
    // (Optional) Get status updates on the recovery operation
}

// With PasskeyBackups
await portal.recoverWallet(BackupMethods.Passkey) { status ->
    // (Optional) Get status updates on the recovery operation
}
```

{% hint style="danger" %}
**WARNING**: To recover a wallet with the Portal SDK, your device must be configured to use passcode authentication. Please note that if you disable your passcode authentication after executing the `recover` function, you will need to run the `recover` function again.
{% endhint %}

## Self-Managed Recovery

By default, Portal manages storing both the encrypted client backup share and the custodian backup share for you. If you prefer to store and manage the backup shares in your own infrastructure instead of using Portal-Managed Backups, see our [Self-Managed Backups](../../resources/self-managed-backups.md) guide.

Before recovering, you will need to retrieve the encrypted client backup share from your API. You will then provide the encrypted client backup share to `portal.recoverWallet`. Here's an example of how that might look in your code:

```swift
// Imports...

class MainActivity : AppCompatActivity() {
  lateinit var portal: Portal
  lateinit var recoverButton: Button

  // Your API instance.
  private val exchangeApi: Api = Api()

  // The user from your API instance.
  lateinit var user: User

  override fun onCreate(savedInstanceState: Bundle?) {
    super.onCreate(savedInstanceState)
    setContentView(R.layout.activity_main)
    recoverButton = findViewById(R.id.recoverButton)
    recoverButton.setOnClickListener { handleRecover() }
  }

  private fun handleRecover() {
    lifecycleScope.launch {
      try {
        // Retrieve the existing encrypted client backup share from your API.
        val cipherText = exchangeApi.getCipherText(user.id, "GDRIVE")

        // Set a new signing share by running recover.
        portal.recoverWallet(cipherText) { status ->
          // Do something with the status, such as update a progress bar
          // or log the progress
          Log.println(Log.INFO, "[PORTAL]", "Recover status: ${status.status} is done: ${status.done}")
        } 
      } catch (err: Error) {
        // ❌ Handle any errors recovering the wallet. Re-run recover.
      }
    }
  }
}
```

{% hint style="danger" %}
**WARNING**: To recover a wallet with the Portal SDK, your device must be configured to use passcode authentication. Please note that if you disable your passcode authentication after executing the `recover` function, you will need to run the `recover` function again.
{% endhint %}

## Progress Callbacks

You can learn how to handle the progress callbacks for `portal.recoverWallet` [here](mpc-progress-callbacks.md).

## Next steps

Amazing! Your users can now easily recover their wallet. Next let's dive into handling sessions across multiple devices for your users.
