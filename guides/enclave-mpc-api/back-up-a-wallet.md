---
description: >-
  This guide will walk you through how to create a backup of a Portal client's
  wallet using the Enclave MPC API.
---

# Back up a wallet

Portal lets you securely back up your MPC wallets in order to recover them even if the signing share pairs are lost or damaged.&#x20;

There are two configurations you can choose from when running backup: **Portal-Managed Backups** or **Self-Managed Backups** which alter where backup shares are stored.

## Portal-Managed Backups

With Portal-Managed Backups, Portal will store the Custodian Backup Shares on our backend leaving the storage of the Client Backup Shares up to your application.&#x20;

This means you do **not** need to build and maintain a webhook server that accepts Custodian Backup Shares from Portal.&#x20;

The benefit of Portal-Managed Backups is that there is less integration work required. The tradeoff is that you are dependent on Portal for running backup & recovery.&#x20;

When using Portal-Managed Backups with the Enclave MPC API you will save the Client Backup Share in your backend. Be sure to store the shares in a way that makes them resilient to failure scenarios of your Client Signing Shares.

This implementation means that for recovery or eject you will retrieve the Client Backup Share from your own backend while requesting the Custodian Backup Share from Portal.&#x20;

## Self-Managed Backups

With Self-Managed Backups, Portal will send the Custodian Backup Shares to a webhook server you're required to deploy and maintain while your application is responsible for storing the Client Backup Share.

See our [Self-Managed Backups](../../resources/self-managed-backups.md) guide for more details on implementation.

## Implementing Backup

While using the SDKs provides 4 built in backup methods: GDrive, iCloud, Password/PIN, and Passkeys. However, when using the Enclave MPC API **your application is responsible for its own backup method**.

We recommend using a Key Management Service (KMS) like those in [Google Cloud](https://cloud.google.com/kms/docs/) and [AWS](https://docs.aws.amazon.com/kms/latest/developerguide/overview.html) paired with [envelope encryption](https://cloud.google.com/kms/docs/envelope-encryption) using AES 256 in GCM.

#### Example&#x20;

{% tabs %}
{% tab title="Portal-Managed Backups" %}
<pre class="language-javascript"><code class="lang-javascript">// Step 1. Run backup by passing in the response from generate (the signing shares)
const backupResponse = await axios.post(
  `https://mpc-client.portalhq.io/v1/backup`,
  {
    generateResponse: JSON.stringify(signingShares),
  },
  {
    headers: {
      'Content-Type': 'application/json',
      Authorization: `Bearer ${clientApiKey}`,
    },
  },
);
  
const { secp256k1Share, ed25519Share } = backupResponse.data;

// Step 2. Encrypt the client backup shares
const secp256k1Ciphertext = await YourEnvelopEncryptionService.encrypt(secp256k1Share)
const ed25519Ciphertext = await YourEnvelopEncryptionService.encrypt(ed25519Share)

// Step 3. Store the client ciphertexts with Portal or your backend [OPTIONAL]
await axios.post(
    `https://api.portalhq.io/api/v3/clients/me/backup-share-pairs/${secp256k1Share.id}`,
    {
      clientCipherText: secp256k1Ciphertext,
    },
    {
      headers: {
        'Content-Type': 'application/json',
        Authorization: `Bearer ${clientApiKey}`,
      },
    },
  );
  
await axios.post(
<strong>     `https://api.portalhq.io/api/v3/clients/me/backup-share-pairs/${ed25519Share.id}`,
</strong><strong>    {
</strong>      clientCipherText: ed25519Ciphertext,
    },
    {
      headers: {
        'Content-Type': 'application/json',
        Authorization: `Bearer ${clientApiKey}`,
      },
    },
  );

// Step 4. Mark the backup process as complete by updating the backup share pairs status
await axios.patch(
  `https://api.portalhq.io/api/v3/clients/me/backup-share-pairs`,
  {
    status: 'STORED_CLIENT_BACKUP_SHARE',
    backupSharePairIds: [secp256k1Share.id, ed25519Share.id],
  },
  {
    headers: {
      'Content-Type': 'application/json',
      Authorization: `Bearer ${clientApiKey}`,
    },
  },
);
</code></pre>
{% endtab %}

{% tab title="Self-Managed Backups" %}
```javascript
// Step 1. Run backup by passing in the response from generate (the signing shares)
const backupResponse = await axios.post(
  `https://mpc-client.portalhq.io/v1/backup`,
  {
    generateResponse: JSON.stringify(signingShares),
  },
  {
    headers: {
      'Content-Type': 'application/json',
      Authorization: `Bearer ${clientApiKey}`,
    },
  },
);
  
const { secp256k1Share, ed25519Share } = backupResponse.data;

// Step 2. Encrypt the client backup shares
const secp256k1Ciphertext = await YourEnvelopEncryptionService.encrypt(secp256k1Share)
const ed25519Ciphertext = await YourEnvelopEncryptionService.encrypt(ed25519Share)

// Step 3. Store the client ciphertexts on your own backend
await axios.post('{your_server}/users/[userId]/user-backup-share', {
  data: { secp256k1Ciphertext, ed25519Ciphertext }
})

// Step 4. Mark the backup process as complete by updating the backup share pairs status
await axios.patch(
  `https://api.portalhq.io/api/v3/clients/me/backup-share-pairs`,
  {
    status: 'STORED_CLIENT_BACKUP_SHARE',
    backupSharePairIds: [secp256k1Share.id, ed25519Share.id],
  },
  {
    headers: {
      'Content-Type': 'application/json',
      Authorization: `Bearer ${clientApiKey}`,
    },
  },
);
```
{% endtab %}
{% endtabs %}
