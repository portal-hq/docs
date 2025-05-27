# Android

Portal provides MPC **wallets** and dApp **connections** for organizations and their users.\
\
To integrate Portal, an organization adds a **client library** to their mobile app and a few **server API endpoints**.

![The Portal library connects your mobile app and server with the web3 ecosystem.](<../../.gitbook/assets/image (18).png>)

## Installation

Update the dependencies in your `build.gradle` to include the `portal-android` dependency

```groovy
// In your app/build.gradle file
...
dependencies {
    ...
    implementation "io.portalhq.android:portal-android:X.X.X"
}
```

## Initializing Portal

After you update your `build.gradle` file, you must sync gradle and you're ready to import Portal into an Activity of your choice.



With the `Portal Android` dependency now installed, we can now create an instance of the `Portal` class. Below is an example of how you can do this. In this example, we're using your app's `MainActivity`.

{% tabs %}
{% tab title="5.x.x" %}
```kotlin
package io.portal.android.app
 
import io.portal.android.Portal
import io.portal.android.storage.cloud.google.GoogleStorage
import io.portal.android.storage.mobile.Keychain

class MainActivity : AppCompatActivity() {
    lateinit val portal: Portal
    
    override fun onCreate(savedInstanceState: Bundle?) {
        super.onCreate(savedInstanceState)
        
        // Initialize your Portal instance.
        portal = Portal(
            // A Portal client API key. You can obtain one from Portal's REST API.
            apiKey = CLIENT_API_KEY,
            // A map of chainIDs to Gateway URLs (e.g. Infura, Alchemy, etc.)
            rpcConfig = mapOf("eip155:11155111" to YOUR_GATEWAY_URL),
            // A boolean to auto-approve transactions.
            autoApprove = true,
            //Optional: Only provide Eth chain id here if you are upgrading from V3 otherwise skip this. This is needed for some legacy code
            legacyEthChainId = 11155111
        )
        
        // Configure Backup with GDrive
        portal.configureGoogleStorage(
          GDriveConfiguration(
              clientId = YOUR_GDRIVE_CLIENT_ID, // Your Google client id with GDrive access
              signOutAfterUse = true // Signout Google after backup/recover operation,
              gDriveBackupOption = GDriveBackupOption.AppDataFolder // this mode specifies where the backup key is stored in GDrive. Will be explained later in GDrive backup guides
          )
        )
        
    }
}
```


{% endtab %}

{% tab title="3.x.x" %}
```kotlin
package io.portalhq.android.app

import io.portalhq.android.Portal
import io.portalhq.android.storage.cloud.google.GoogleStorage
import io.portalhq.android.storage.mobile.Keychain

class MainActivity : AppCompatActivity() {
    lateinit val portal: Portal
    
    override fun onCreate(savedInstanceState: Bundle?) {
        super.onCreate(savedInstanceState)
        
        // 👇 Don't forget to replace below with your own Google Client ID.
        val gDrive = GoogleStorage(YOUR_GOOGLE_CLIENT_ID, signoutAfterUse = true)
        val backupOptions = BackupOptions(gdrive = gDrive)
        val keychain = Keychain(applicationContext)
    
        // Initialize the Portal instance
        portal = Portal(
            // A Portal client API key. You can obtain one from Portal's REST API.
            apiKey = CLIENT_API_KEY,
            // Provide this if you want to implement backup yourself
            backup = null,
            // The EVM network chain ID.
            chainId = 11155111,
            // An instance of Portal's Keychain.
            keychain = keychain,
            // A map of chainIDs to Gateway URLs (e.g. Infura, Alchemy, etc.)
            gatewayConfig = mapOf(5 to YOUR_GATEWAY_URL),
            // Provide configuration of backup options you want to use with Portal
            backupOptions = backupOptions
        )
    }
}
```

}
{% endtab %}
{% endtabs %}

Now that we have our Portal instance, the next step is to generate a wallet. Let's create one!

{% hint style="info" %}
If you are using [Client Session Tokens (CSTs)](../../resources/authentication-and-api-keys.md), this hint is for you.

When your user's CST expires, all Portal SDKs will throw an error on the next MPC Operation the user makes (e.g. creating a wallet, backing up a wallet, recovering a wallet, or signing). For Android, the SDK will throw `PortalHttpUnauthorizedException` which you can use as an indicator to refresh your CST.
{% endhint %}
