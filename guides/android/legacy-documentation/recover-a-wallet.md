---
description: >-
  This guide will walk you through the recovery process in the event that the
  user needs to recover their wallet.
---

# Recover a wallet

MPC recovery allows your users to recover their MPC wallets in the event that their device is lost, stolen, or bricked.&#x20;

MPC recovery uses the MPC backup that a user has already configured to create a new set of signing shares.

Recovery creates a new set of **signing** **shares**, and deprecates the old set.

## User Recovery

Recovering a user's MPC share is done by retrieving the encryption key from the user's cloud storage provider and decrypting the encrypted share within your infrastructure.

In order to support user MPC recovery, two dependencies must be met:

1. Your server must send the encrypted share to the user's mobile device
2. Your mobile app must initiate the recovery process

### **Sharing encrypted shares with the client**

During the recovery process, the encrypted client backup share needs to be retrieved from the custodian backend.

Create an endpoint to get the encrypted share, so the client can decrypt the value using the encryption key stored in the user's cloud storage provider.

{% code title="server.js" overflow="wrap" lineNumbers="true" %}
```javascript
/*
 * This endpoint can be defined by you however you want
 */
app.get('/portal/clients/:clientId/backup', async (req, res) => {
  const { clientId } = req.params
  
  // example db function getting a "user" by clientId
  const user = await db.users.find({
    where: {
      clientId: clientId,
    }
  })
  res.status(200).send({ cipherText: user.cipherText })
```
{% endcode %}

### Updating Your Mobile App

In order to initiate the recovery process from your mobile app, you must perform two steps:

1. Retrieve the encrypted client backup share from your API.
2. Get a new encrypted client backup share from running `portal.mpc.recover`.

Here you implement a button that requests the original cipherText from your API. You then use that cipherText to recover the user's wallet.

```kotlin
package io.portal.android.app

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

  override fun onActivityResult(requestCode: Int, resultCode: Int, data: Intent?) {
    super.onActivityResult(requestCode, resultCode, data)

    if (portal.backup.drive.auth.onActivityResult != null) {
      portal.backup.drive.auth.onActivityResult!!(requestCode, resultCode, data)
    }
  }

  private fun handleRecover() {
    CoroutineScope(Dispatchers.IO).launch {
      try {
        // Retrieve the existing encrypted client backup share from your API.
        val backupShare = async { exchangeApi.getCipherText(user.id) }

        // Set a new signing share by running recover.
        portal.mpc.recover(backupShare.await()) { status ->
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
**WARNING**: To recover a wallet with the Portal SDK, your device must be configured to use passcode authentication. Please note that if you disable your passcode authentication after executing the `recover` function, you will need to run the `recover` function again to continue using your Portal wallet.
{% endhint %}

### Status Flow

```kotlin
ReadingShare("Reading share")
DecryptingShare("Decrypting share")
RecoveringSigningShare("Recovering signing share")
GeneratingShare("Generating share")
ParsingShare("Parsing share")
StoringShare("Storing share")
Done("Done")
```

That's it! You have now protected your users in the event they lose their device 💪

Next, let's explore how to use Portal's web3 provider to interact with a blockchain directly from your Android app.
