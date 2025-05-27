---
description: >-
  This guide will walk you through how backups of a user's wallet are set up
  with Portal.
---

# Back up a wallet

MPC backups allow your users to recover their MPC wallets in the event that their device is lost, stolen, or is bricked.&#x20;

Backups are handled in two pieces:[ user MPC backups](./#user-mpc-backups) and [custodian MPC backups](./#custodian-mpc-backups).

At the time of recovery, these two backups are **used together** to generate new shares to be stored on-device, allowing the user to migrate their wallet with a new device.

## User MPC backups

Storing a user's MPC backup is done by storing an encryption key with the user's **cloud storage provider** and **the encrypted share within your infrastructure**.

In order to support user MPC backups, three main dependencies must be met:

1. You must have cloud storage configured for storing the user's share of the MPC backup
2. Your server must be able to store the encrypted share
3. Your mobile app must send the encrypted share to your server for storage

### Setting up your cloud storage adapter

With our swift implementation, you have the option of using _**iCloud**_ or _**Google Drive.**_&#x20;

_See_ [_Backup Cloud Storage_](../../../../resources/backup-options/) _for more info on how to configure them._

### **Storing encrypted shares**

On your server, create an endpoint to accept the encrypted share and store it.

<pre class="language-typescript" data-title="server.js" data-line-numbers><code class="lang-typescript">/*
 * This endpoint can be defined by you however you want
 */
app.post('/portal/clients/:clientId/backup', async (req, res) => {
<strong>  const { clientId } = req.params
</strong>  const { backupShare } = req.body
  
  // Example db function updating a "users" table with the backupShare
  await db.users.update({
    where: {
      clientId,
    },
    data: {
      backupShare,
    },
  })
  res.status(200).send()
})
</code></pre>

### **Sending shares to your server**

In order to add support for user MPC backups to your mobile app, you must perform two steps:

1. Generate an encrypted client backup share using `portal.mpc.backup`.
2. Send the resulting backup share to your API and store it.
3. Call `portal.api.storedClientBackupShare` to notify Portal that the encrypted client backup share was saved successfully. (Alternatively you can [make an HTTP request to our API](../../../../reference/custodian-api/v1-endpoints.md) directly.)

{% hint style="info" %}
Ensure you have created and connected the UI element in the storyboard to the action.
{% endhint %}

```swift
import PortalSwift

class ViewController: UIViewController {
  public var portal: Portal?
  public var yourApiUrl: String = "https://YOUR_API_URL.com"

  @IBAction func handleBackup(_ sender: UIButton!) {
    portal?.mpc.backup(method: BackupMethods.iCloud.rawValue)  {
      (result: Result<String>) -> Void in

      guard result.error == nil else {
        // ❌ Handle errors backing up the user's wallet.
        return
      }

      // We now have the encrypted client backup share.
      let newBackupShare = result.data!

      // Prepare the request to store the client backup share on your API.
      let request = HttpRequest<String, [String : String]>(
        url: self.yourApiUrl + "clients/[clientId]/backup",
        method: "POST",
        body: ["backupShare": newBackupShare],
        headers: ["Content-Type": "application/json"],
        isString: true
      )

      // Send the request.
      request.send() { (result: Result<Any>) in
        guard result.error == nil else {
          // ❌ Notify Portal that the client backup share was not stored.
          portal?.api.storedClientBackupShare(false)
          return
        }

        // ✅ Notify Portal that the client backup share was stored! 🙌
        portal?.api.storedClientBackupShare(true)
      }
    }
  } progress: { status in
      print("Backup Status: ", status)
    }
}
```

### Status Flow

```swift
case readingShare = "Reading share"
case generatingShare = "Generating share"
case parsingShare = "Parsing share" 
case encryptingShare = "Encrypting share"
case storingShare = "Storing share"
case done = "Done"
```

## Custodian MPC backups

Storing a custodian MPC backup is done by Portal generating a custodian backup share and sending the share –  via `webhook` – to be stored within your infrastructure.

In order to support custodian MPC backups, two main dependencies must be met:

1. Register a `webhooks_root` with Portal
2. Implement the `/{webhooks_root}/backup` route in your server to store your backup share

### Registering your webhook root

You can register your `webhooks_root` via the [Portal REST API](../../../../reference/custodian-api/v1-endpoints.md#register-a-new-webhook).

### Implementing the webhook

Portal will send the custodian MPC backup share to `/{webhooks_root}/backup` via a `POST` request. The body of this `POST` request will contain two fields:

* `clientId` - The Portal `clientId` for the user&#x20;
* `share` - a `JSON.stringified` version of the backup share

{% code title="server.js" lineNumbers="true" %}
```javascript
import express from 'express'

const app = express()

app.post('/webhook/backup', async (req, res) => {
  // Destructure the provided fields from the request body
  const { 
    clientId, // The Portal clientId for the user
    share, // a JSON.strinfigied version of the backup share
  } = req.body
  
  // TODO: Store the backup share within your infrastructure
  
  res.status(200).send()
})
```
{% endcode %}

{% hint style="info" %}
When Portal makes a request to your `/backup` webhook, another immediate request is made to `/backup/fetch` in order to validate the backup share was stored successfully.
{% endhint %}

## Next steps

Now that you've successfully implemented MPC backups, your app can support [MPC recovery](../recover-a-wallet.md). In the next section, we will walk through the recovery process.
