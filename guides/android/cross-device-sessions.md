---
description: >-
  Use wallet provisioning to enable your users to create multiple signing shares
  across their devices.
---

# Cross-device sessions

Once users successfully back up their wallets, they can generate a new set of signing shares irrespective of the SDK in use. Furthermore, both the new and old sets of signing shares can be used simultaneously, thanks to **`portal.provisionWallet`**. This provisioning relies on the user already having a successful set of backup shares.

While provisioning, the wallet fetches the encryption key from the user's cloud storage provider to decrypt the encrypted client backup share. Note that our SDK handles the decryption; you only need to supply the encrypted client backup share.

For wallet provisioning support, consult [the recovery documentation](legacy-documentation/recover-a-wallet.md) to establish the necessary **`/backup/fetch`** webhook. Also, ensure the user has successfully completed the backup process.

Here's how to implement **`portal.provisionWallet`**:

```kotlin
// Imports...
import kotlinx.coroutines.*

class MainActivity : AppCompatActivity() {
  lateinit var portal: Portal
  lateinit var provisionButton: Button

  // Your API instance.
  private val exchangeApi: Api = Api()

  // The user from your API instance.
  lateinit var user: User

  override fun onCreate(savedInstanceState: Bundle?) {
    super.onCreate(savedInstanceState)
    setContentView(R.layout.activity_main)
    provisionButton = findViewById(R.id.provisionButton)
    provisionButton.setOnClickListener { handleProvision() }
  }

  private fun handleProvision() {
    lifecycleScope.launch {
      try {
        // Retrieve the existing encrypted client backup share from your API.
        val backupShare = exchangeApi.getCipherText(user.id)

        // Set a new signing share by running provisionWallet.
        portal.provisionWallet(backupShare) { status ->
          // Ensure UI interactions are done on the main thread
          // Do something with the status, such as update a progress bar
          // or log the progress
          Log.i("[PORTAL]", "Provision wallet status: ${status.status}, is done: ${status.done}")
        } 
      } catch (e: Exception) {
        // ❌ Handle any errors provisioning the wallet.
        Log.e("[PORTAL]", "Error provisioning wallet: ${e.message}")
      }
    }
  }
}
```

And that's it! You're now equipped to support multiple sessions across different devices for your users.
