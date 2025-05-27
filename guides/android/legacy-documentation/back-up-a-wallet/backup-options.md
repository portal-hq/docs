# Backup Options

### Password/Pin Backup

Allow customers to create a password/pin. Customers can either remember the password or store it in a password storage manager.

#### Implementation Requirements

1. Create a UI for password input.&#x20;
2. Enforce password requirements. Customer can choose between password, PIN code, passcode, or any other text-based input.
3. If user forgets password there are no additional recovery options.

{% code overflow="wrap" %}
```typescript
package io.portal.android.app

// Imports...

class MainActivity : AppCompatActivity() {
    lateinit var portal: Portal
    lateinit var backupButton: Button
    
    // Your API instance.
    private val exchangeApi: Api = Api()
    
    // The user from your API instance.
    lateinit var user: User
    
    override fun onCreate(savedInstanceState: Bundle?) {
        super.onCreate(savedInstanceState)
        setContentView(R.layout.activity_main)
        backupButton = findViewById(R.id.backupButton)
        backupButton.setOnClickListener { handleBackup() }
    }

    private fun handleBackup() {
        CoroutineScope(Dispatchers.IO).launch {
            try {
                val backupConfigs = BackupConfigs(PasswordStorageConfig(password = PASSWORD))
    
                // Get an encryped client backup share.
                val backupShare = portal.backupWallet(
                                      backupMethod = BackupMethods.Password,
                                      backupConfigs = backupConfigs)  { status ->
                    // Do something with the status, such as update a progress bar
                    // or log the progress
                    Log.println(Log.INFO, "[Portal]", "Backup status: ${status.status} is done: ${status.done}")
                }
                
                // Send the backup share to your API and store it.
                exchangeApi.storeCipherText(user.id, backupShare)
                
                // ✅ Notify Portal that the backup share was stored! 🙌
                portal.api.storedClientBackupShare(true)
            } catch (err: Throwable) {
                // ❌ Notify Portal that the backup share was not stored.
                portal.api.storedClientBackupShare(false)
            }
        }
    }
}
```
{% endcode %}

### Google Drive Backup

See the docs on how to[gdrive.md](../../../../resources/backup-options/gdrive.md "mention")

_Note - If you dont pass a backup option into_ `portal.backupWallet()` _we will default to GDRIVE backup._
