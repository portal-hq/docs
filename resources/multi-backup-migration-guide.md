---
description: Transitioning from MPC version v5 to v6.
---

# Multi-backup migration guide

## What is Multi-Backup?

Multi-backup enhances data security by allowing users to utilize multiple backup methods, ensuring greater resilience and recovery options. In previous versions, recovery attempts could fail if they didn't match the latest backup method used. With multi-backup, users can recover data using any of the methods they've backed up with, such as `GDRIVE`, `ICLOUD`,`PASSWORD`, or `PASSKEY`.

## Key Changes in the Migration

* **Webhook Adjustments**:
  * `POST /backup`: Now includes a `"backupMethod"` key.
  * `POST /backup/fetch`: Replaces `"backupShare"` with `"backupShares"`.
* **API Endpoint Modifications**: The `POST /stored-client-backup-share` endpoint now requires `"backupMethod"`.
* **Database Schema Alterations**: To support multi-backup, you may need to modify your database schema.

## **Applicable SDK Version Upgrades**

This guide is applicable for the following SDK upgrades:

<img src="https://emoji.slack-edge.com/T02MA0KV6R1/react_native/d9b06c97a88741af.png" alt=":react_native:" data-size="line"> **React Native:** `^2.0.0` -> `^3.0.0`

<img src="https://emoji.slack-edge.com/T02MA0KV6R1/swift/f4598922e29dd3cc.png" alt=":swift:" data-size="line"> **Swift:** `^2.0.0` -> `^3.0.0`

<img src="https://emoji.slack-edge.com/T02MA0KV6R1/android/613577c8eadbecc1.gif" alt=":android:" data-size="line"> **Kotlin:** `^2.0.0` -> `^3.0.0`

<img src="https://a.slack-edge.com/production-standard-emoji-assets/14.0/apple-medium/1f310@2x.png" alt=":globe_with_meridians:" data-size="line"> **Web:** `^0.0.0` -> `^1.0.0`

## Database Changes

Multi-backup requires storing both custodian and user backup shares by their respective backup method. We recommend the following database model structure:

```prisma
// Encrypted backup share received from portal.backup().
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
model CustodianBackupShare {
  backupMethod String       @default("UNKNOWN")           // (String) The method used for the backup. Defaults to "UNKNOWN" if not specified.
  createdAt    DateTime     @default(now())               // (DateTime) The timestamp when the backup share was created.
  id           String       @id @default(cuid())          // (String) A unique identifier for each backup share
  share        String                                     // (String) The raw backup share data.
  userId       String                                     // (String) The identifier of the user to whom the backup share belongs.

  user User @relation(fields: [userId], references: [id]) // (User) A relation field that links to the corresponding user in the User model.

  @@unique([userId, backupMethod])                        // A unique constraint ensuring that each user has only one backup share per method.
}

// User model, often referred to as "Client" in documentation.
model User {
  clientApiKey   String  @unique               // (String) The unique Client API Key assigned to the user.     
  clientId       String  @unique               // (String) The unique identifier for the client that Portal uses.
  id             String  @id @default(cuid())  // (String) The primary key for the user

  userBackupShares    UserBackupShare[]    // (UserBackupShare[]) A list of the user backup shares related to the user.
  custodianBackupShares CustodianBackupShare[] // (CustodianBackupShare[]) A list of the custodian backup shares related to the user.
}
```

For assistance or more details on structuring your database for this migration, please reach out to our team, we are here to help! 😊

## Webhook Changes

Before upgrading to multi-backup, update your webhook implementations. These new implementations are backwards compatible. Here's what you need to account for:



**`POST /backup`**: This request now includes a `backupMethod` key. Store the custodian backup share accordingly.

Example body:

```json
{
  "clientId": "some-client-id",
  "share": "stringified-share",
  // New key-value pair 👇
  "backupMethod": "PASSWORD-SECP256K1" // One of: "GDRIVE-SECP256K1", "ICLOUD-SECP256K1", "PASSWORD-SECP256K1", "PASSKEY-SECP256K1", "GDRIVE-ED25519", "ICLOUD-ED25519", "PASSWORD-ED25519", "PASSKEY-ED25519"  (@WARNING: we may add others or modify them in the future)
}
```

{% hint style="info" %}
Be sure to store the `share` for the user by `backupMethod.`
{% endhint %}

Store the `share` for the user by `backupMethod.`Since a user can now have a backup share _per backup method_. If a `share` already exists for that user with the same `backupMethod`, delete the old share and keep the new one.&#x20;



**`POST /backup/fetch`**: Expect an array of backup shares in the response, reflecting the user's multiple backup methods.

```json
{
  // "backupShare": "stringified-share" // Removed.

  // New key-value pair 👇
  "backupShares": ["stringified-share", "stringified-share-2", ...]
}
```

## SDK + REST API Changes

* **Storing the Encrypted User Backup Share**: After `portal.backup()`, store the returned `cipherText` by the backup method that was used in your database.
* **Upgrading the API Endpoint to v2**: Use `PUT https://api.portalhq.io/api/v2/clients/me/wallet/stored-client-backup-share`, providing the `backupMethod` in the request body. You can provide it in the request body like this:

{% code fullWidth="false" %}
```json
{
  "success": true,
  // New key-value pair 👇
 "backupMethod": "PASSWORD-SECP256K1" // One of: "GDRIVE-SECP256K1", "ICLOUD-SECP256K1", "PASSWORD-SECP256K1", "PASSKEY-SECP256K1", "GDRIVE-ED25519", "ICLOUD-ED25519", "PASSWORD-ED25519", "PASSKEY-ED25519"  (@WARNING: we may add others or modify them in the future)
}
```
{% endcode %}

* **Retrieving the User Backup Share**: Update your retrieval process to match the backup method used in `portal.recover()`.
* **Update `isMultiBackupEnabled` feature flag:** Update the feature flag `isMultiBackupEnabled` to `true` when instantiating Portal.

{% code fullWidth="false" %}
```typescript
new Portal({
  ...,
  featureFlags: {
    ...,
    isMultiBackupEnabled: true,
  },
})
```
{% endcode %}



**You did it!** 🙌 You've successfully navigated the intricacies of enabling multiple backups for your users. Please reach out if you have any questions or if we can assist in any way.
