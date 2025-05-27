---
description: >-
  This guide will walk you through how backups of a user's wallet are set up
  with Portal.
---

# Back up a wallet

{% hint style="warning" %}
This product is currently in Beta and is under active development.
{% endhint %}

MPC backups allow your users to recover their MPC wallets in the event that their device is lost, stolen, or is bricked.&#x20;

Backups are handled in two pieces:[ user MPC backups](./#user-mpc-backups) and [custodian MPC backups](./#custodian-mpc-backups).

At the time of recovery, these two backups are **used together** to generate new shares to be stored on-device, allowing the user to migrate their wallet with a new device.

## User MPC backups

Storing a user MPC backup is done by storing an encryption key with the user's **cloud storage provider** and **the encrypted share within your infrastructure**.

In order to support user MPC backups, three main dependencies must be met:

1. You must have cloud storage configured for storing the user's share of the MPC backup
2. Your server must be able to store the encrypted share
3. Your app must send the encrypted share to your server for storage

### Setting up your cloud storage adapter

The Web SDK currently support Google Drive as the backup mechanism. When a user backs up their wallet, the encryption key for their backup share is stored in their Google Drive folder.&#x20;

In order to use the Google Drive functionality for backup you must first get an [oAuth Credential](https://support.google.com/cloud/answer/6158849?hl=en) for your Google Project.&#x20;

{% hint style="info" %}
If you don't already have a GCP Project, you can create one [here](https://cloud.google.com/).
{% endhint %}

**Setting up your oAuth Credential**

1. Go to the [Google Cloud Platform Console](https://console.cloud.google.com/).
2. From the projects list, select a project or create a new one.
3. If the APIs & services page isn't already open, open the console left side menu and select **APIs & services**.
4. On the left, click **Credentials**.
5. Click **New Credentials**, then select **OAuth client ID**.
6. For application type, choose `Web Application`
7. In the `Authorized JavaScript Origins` section, add `https://web.portalhq.io`
8. In the `Authorized Redirect URIs` section, add https://web.portalhq.io/auth/index.html
9. If this is your first time creating a client ID, you can also configure your consent screen by clicking **Consent Screen**. You won't be prompted to configure the consent screen after you do it the first time.
10. Click **Create client ID**

{% hint style="info" %}
You must also [enable the Google Drive API](https://support.google.com/googleapi/answer/6158841?hl=en) in your project in order for backup to function properly.
{% endhint %}

### **Initializing Portal with Your oAuth Client**

To use Google Drive backups with Portal, add the `Client ID` for your oAuth Client to your initialization config.

```typescript
const Portal = new Portal({
    apiKey: 'YOUR-CLIENT-API-KEY',
    autoApprove: true, // This should only be used for local development
    chainId: 5, 
    gatewayConfig: 'YOUR-INFURA-OR-ALCHEMY-URL',
    gdrive: {
        clientId: 'YOUR-OAUTH-CLIENT-ID',
    },
})
```

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

In order to add support for user MPC backups to your app, you must perform three steps:

1. Generate an encrypted client backup share using `portal.mpc.backup`.
2. Send the resulting backup share to your API and store it.
3. Call `portal.api.storedClientBackupShare` to notify Portal that the encrypted client backup share was saved successfully. (Alternatively you can [make an HTTP request to our API](../../../../reference/custodian-api/v1-endpoints.md) directly.)

{% code title="BackupButton.tsx" overflow="wrap" lineNumbers="true" %}
```typescript
const BackupButton: React.FC = () => {
  const handleBackup = async () => {
    // Get an encryped client backup share from running backup.
    const backupShare = await portal.backupWallet()
    
    try {
      // Send the backup share to your API and store it.
      await axios.post('{your_server}/clients/[clientId]/backup', {
        data: { backupShare }
      })

      // ✅ Notify Portal that the client backup share was stored! 🙌
      await portal.storedClientBackupShare(true)
    } catch (error) {
      // ❌ Notify Portal that the client backup share was not stored.
      await portal.storedClientBackupShare(false)
    }
  }
  
  return (
    <button onPress={handleBackup}>Backup your wallet</button>
  )
}

export default BackupButton
```
{% endcode %}

## Custodian MPC backups

Storing a custodian MPC backup is done by Portal generating a custodian backup share and sending the share –  via `webhook` – to be stored within your infrastructure.

In order to support custodian MPC backups, two main dependencies must be met:

1. Register a `webhooks_root` with Portal
2. Implement the `/{webhooks_root}/backup` route in your server to store your backup share

### Registering your webhooks\_root

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

Now that you've successfully implemented MPC backups, your app can support [MPC recovery](../../../react-native/recover-a-wallet.md).
