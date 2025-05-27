---
description: >-
  Store and manage backup shares in your own infrastructure instead of using
  Portal-Managed Backups.
---

# Self-Managed Backups

## Backup + Recovery Webhooks

By default, all customers use Portal-Managed Backups - a fully managed backup solution where Portal securely handles encrypting and storing your Portal wallets' backup shares. However, you can opt for "Self-Managed Backups" if you need to handle backup and recovery through your own infrastructure.

This guide covers implementing Self-Managed Backup using webhooks. If you're using the default Portal-Managed Backups, you do not need to implement these webhooks.

### Why do I need webhooks for Self-Managed Backup?

When using Self-Managed Backups, the raw custodian backup share is stored in your database rather than Portal's infrastructure. Here's how it works:

During the backup process, Portal generates the custodian backup share on its servers and then sends it to your API via a webhook endpoint for you to store. After the webhook request completes, the custodian backup share is deleted from the Portal servers. Portal never stores the custodian backup shares in its database — it is only ever used in memory during the duration of the backup and recover processes.

The raw **custodian backup share** is stored in your database.

Then, during the recovery process, Portal will request the custodian backup shares for the Portal client from your API via the `POST` `/backup/fetch` webhook endpoint. After the wallet successfully recovers, a new set of **signing** **shares** are generated, which are then stored on the Portal client's device.

## Your Database

Before we dive into the webhook endpoints' implementations, we should go over your database schema. Multi-backup requires storing both custodian and user backup shares by their respective backup method in your own database. We recommend the following database model structure:

```prisma
// Encrypted backup share received from portal.backup().
// Encrypted backup share retrieved from your API to be used in portal.recover().
model UserBackupShare {
  backupMethod String       @default("UNKNOWN")           // (String) The method used for the backup. Defaults to "UNKNOWN" if not specified.
  createdAt    DateTime     @default(now())               // (DateTime) The timestamp when the backup share was created.
  id           String       @id @default(cuid())          // (String) A unique identifier for each backup share
  cipherText   String                                     // (String) The encrypted backup share data.
  userId       String                                     // (Int) The identifier of the user to whom the backup share belongs.

  user User @relation(fields: [userId], references: [id]) // (User) A relation field that links to the corresponding user in the User model.

  @@unique([userId, backupMethod])                        // A unique constraint ensuring that each user has only one backup share per method.
}

// Raw backup share received from the POST /backup webhook.
// Raw backup share retrieved from the POST /backup/fetch webhook.
model CustodianBackupShare {
  backupMethod String       @default("UNKNOWN")           // (String) The method used for the backup. Defaults to "UNKNOWN" if not specified.
  createdAt    DateTime     @default(now())               // (DateTime) The timestamp when the backup share was created.
  id           String       @id @default(cuid())          // (String) A unique identifier for each backup share
  share        String                                     // (String) The raw backup share data.
  userId       String                                     // (String) The identifier of the user to whom the backup share belongs.

  user User @relation(fields: [userId], references: [id]) // (User) A relation field that links to the corresponding user in the User model.

  @@unique([userId, backupMethod])                        // A unique constraint ensuring that each user has only one backup share per method.
}

// User model, often referred to as "Client" in our documentation.
model User {
  clientApiKey   String  @unique               // (String) The unique Client API Key assigned to the user.     
  clientId       String  @unique               // (String) The unique identifier for the client that Portal uses.
  id             String  @id @default(cuid())  // (String) The primary key for the user

  userBackupShares      UserBackupShare[]      // (UserBackupShare[]) A list of the user backup shares related to the user.
  custodianBackupShares CustodianBackupShare[] // (CustodianBackupShare[]) A list of the custodian backup shares related to the user.
}
```

We won't go into depth just yet, but it's worth calling out that you can see a couple associations:

1. A user has many raw custodian backup shares _unique by backup method_.
2. A user has many encrypted user backup shares _unique by backup method_.
   1. The encryption key is stored differently depending on the backup method used.

Perfect, let's dive right into building out the webhook endpoints with this context.

## API Endpoints you need to build

There are two webhook endpoints that your API needs to support: `POST` `/backup` for backup and `POST` `/backup/fetch` for recover.

### `POST` `/backup`

During the backup process, Portal sends the **custodian backup share** to your API to store. The request will include a `X-Webhook-Secret` header (you can set this in the [Portal Admin Dashboard](https://app.portalhq.io)).&#x20;

The body of this `POST` request will contain three fields:

* `backupMethod` - The backup method used to create the backup share. Example values: `"GDRIVE"`, `"GDRIVE-SECP256K1"`, `"GDRIVE-ED25519"`, `"ICLOUD"`, `"PASSKEY"`, `"PASSWORD"`, etc. This should be stored as a generic string (not an enum) by `clientId`. Its value is subject to change in future releases.
* `clientId` - The Portal ID of the user. We recommend keeping track of this.
* `share` - A JSON stringified version of the custodian backup share.

Your webhook server is expected to store the `backupMethod`, `clientId`, and `share` value in a secure database.

{% hint style="warning" %}
Even if you are currently focused on a specific blockchain like Solana or Ethereum, it's essential to **store ALL backup shares** sent to your webhook. For example, if you're only using Solana and receive backup shares for both `"GDRIVE-SECP256K1"` and `"GDRIVE-ED25519"`, you need to store both. This ensures that all necessary data is available for wallet recovery, regardless of the blockchain involved.
{% endhint %}

{% hint style="warning" %}
The backup **`share`** is critical to protecting your users' wallet. It is important to treat the `share` value as a sensitive value and handle it accordingly. Do not log the value (or request body), be sure to store the value encrypted at rest, and understand the access control on the database.&#x20;
{% endhint %}

It should also return a `200` status code.

Here is a simple example of how to set this up using Node and Express:

<pre class="language-typescript" data-title="server.ts" data-line-numbers><code class="lang-typescript"><strong>app.post('/webhook/backup', async (req, res) => {
</strong>  const backupMethod = req.body['backupMethod']
  
  const share = req.body['share']
  validateShare(share)

  const clientId = req.body['clientId']
  const user = await this.getUserByClientId(clientId)

  // Remove any existing custodian backup shares for this backup method + user.
  await db.custodianBackupShare.deleteMany({
    where: {
      backupMethod,
      userId: user.id,
    },
  })

  // Store the custodian backup share.
  await db.custodianBackupShare.create({
    data: {
      backupMethod,
      share,
      userId: user.id,
    },
  })

  res.status(204).send()
})
</code></pre>

{% hint style="info" %}
When Portal makes a request to your `/backup` webhook, another immediate request is made to `/backup/fetch` right after in order to validate the custodian backup share was stored successfully. The `/backup/fetch` webhook is explained in the "[Handling recovery](../guides/react-native/recover-a-wallet.md)" doc.
{% endhint %}

{% hint style="warning" %}
Your webhook must respond within 10 seconds. If your webhook has an error or does not respond in time, the propagated error will look something like this:

**`FAILED_WEBHOOK`**: This error occurs when a webhook experiences an error. Unable to send backup share to configured webhook. Check your webhook status in the [Portal Admin Dashboard](https://app.portalhq.io/) to learn more.
{% endhint %}

### Recovery

During the recovery process, Portal requests the **custodian backup share** from your API. Portal will request the existing custodian backup shares for a user with a `POST` request to `[webhookBaseURL]/backup/fetch`. The request will include a `X-Webhook-Secret` header (you can set this in the [Portal Admin Dashboard](https://app.portalhq.io)).

The request body of this request will contain one field:

* `clientId` - The Portal Id of the user.

Your webhook server is expected to fetch the custodian backup shares for the given `clientId`. It should also return a `200` status code along with the `backupShares` in the response body.

Here is a simple example of how to set this up using Node and Express:

```typescript
app.post('/webhook/backup/fetch', async (req: Request, res: Response) => {
  const { clientId } = req.body
  
  // Obtain all custodian backup shares for the client.
  const custodianBackupShares = await db.custodianBackupShares.findMany({
    where: {
      clientId,
    },
  })
  
  // Ensure to only return back the JSON stringified shares you received from the /backup webhook endpoint.
  const rawShares = custodianBackupShares.map((custodianBackupShare) => {
    return custodianBackupShare.share
  })
  
  res.status(200).send({ backupShares: rawShares })
})
```

Once we retrieve your custodian backup shares for the client, we will do the heavy lifting to figure out which backup method you're using and select the appropriate custodian backup share to use as a result. So it's as simple as that, just send us all of the custodian backup shares for the client and we'll take care of the rest for this webhook endpoint! :muscle:

## Configuration

To configure your webhook go to the [Settings Page](https://app.portalhq.io/settings) of the Portal web app. Navigate to the **Webhooks** section and select the "New +" button. Enter in the base URL for your webhook server and select "Create". A secure, random webhook secret will be automatically generated for you unless you prefer to create your own secret.

<figure><img src="../.gitbook/assets/image (22).png" alt=""><figcaption><p>Setting up the webhook base URL.</p></figcaption></figure>

Once the webhook base URL has been added. You will be able to view the webhook configuration. See the image below describing the different components of the webhook configuration.

<figure><img src="../.gitbook/assets/CleanShot 2023-04-12 at 16.39.10.png" alt=""><figcaption></figcaption></figure>

1. **Base URL -** This is the URL you've configured.
   1. When Portal runs **backup** it will send a request to the `[webhookBaseURL]/backup` route to send your server a custodian backup share to save for a client by backup method.
   2. When Portal runs **recover** it will send a request to the `[webhookBaseURL]/backup/fetch` endpoint to request the custodian backup shares for a user from your server.
2. **Webhook Secret** - Click the icon to reveal your webhook secret.
3. **Webhook Endpoint** - Each endpoint that Portal sends requests to has a status indicator letting you know whether the route is working correctly.
4. **Request Explorer** - To view the HTTP requests and responses sent to each route select "View Requests".

## Security

To ensure secure transmission of recovery shares via the webhook we follow several security best practices.

### Webhook Secret

Including a secret along with each webhook request adds authentication to the requests sent to your webhook server. You should verify that the secret with the request matches the secret configured in Portal.

All of our webhook requests are sent with a secret in the `X-Webhook-Secret` header. The secret is securely generated on webhook configuration and can be viewed in the [Settings Page](https://app.portalhq.io/settings) of the web app.

<figure><img src="../.gitbook/assets/image (47).png" alt=""><figcaption><p>A configured webhook in the Settings page of the web app.</p></figcaption></figure>

### IP Allowlist

Restricting requests on your webhook server to only those from Portal's IP addresses protects against requests from other parties. Configure your webhook server to only accept inbound connections from Portal's IP address.

Portal always makes requests from the IP addresses `35.203.150.117`, `104.155.171.139` or `35.185.20.23`.
