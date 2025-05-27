---
description: >-
  This guide will walk you through the recovery process in the event that the
  user needs to recover their wallet.
---

# Recover a wallet

{% hint style="warning" %}
This product is currently in Beta and is under active development.
{% endhint %}

MPC recovery allows your users to recover their MPC wallets in the event that their device is lost, stolen, or bricked.&#x20;

MPC recovery uses the MPC backup that a user has already configured to create a new set of signing shares.

Recovery is handled in two pieces: **user recovery** and **custodian recovery**.&#x20;

Recovery creates two new pairs, one for **signing** and one for **backup**, and deprecates the two old pairs.

## User Recovery

Recovering a user's MPC share is done by retrieving the encryption key from the user's cloud storage provider and decrypting the encrypted share within your infrastructure.

In order to support user MPC recovery, four dependencies must be met:

1. Your server must send the encrypted share to the user's device
2. Your app must initiate the recovery process
3. Your app must send the new encrypted backup share to your server

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
})
```
{% endcode %}

### Updating Your App

In order to initiate the recovery process from your app, you must perform three steps:

1. Retrieve the encrypted client backup share from your API.
2. Get a new encrypted client backup share from running `portal.mpc.recover`.
3. Send the new backup share to your API and store it.
4. Call `portal.api.storedClientBackupShare` to notify Portal that the client backup share was saved successfully. (Alternatively you can [make an HTTP request to our API](../../../reference/custodian-api/v1-endpoints.md) directly.)

{% code title="RecoveryButton.tsx" lineNumbers="true" %}
```typescript
const RecoveryButton: FC = () => {
  const handleRecovery = async () => {
    // Get the existing encrypted client backup share from your API.
    const backupShare = await axios.get('/clients/[clientId]/backup')
    
    // Get a new encrypted client backup share from running recover.
    const newBackupShare = await portal.recoverWallet(
      backupShare,
    )
    
    try {
      // Send the new backup share to your API and store it.
      await axios.post('{your_server}/clients/[clientId]/backup', {
        data: { backupShare: newBackupShare }
      })

      // ✅ Notify Portal that the backup share was stored! 🙌
      await portal.storedClientBackupShare(true)
    } catch (error) {
      // ❌ Notify Portal that the backup share was not stored.
      await portal.storedClientBackupShare(false)
    }
  }
  
  return (
    <button onPress={handleRecovery}>Recover your wallet</button>
  )
}

 export default RecoveryButton 
```
{% endcode %}

{% hint style="danger" %}
**WARNING**: To recover a wallet with the Portal SDK, your device must be configured to use passcode authentication. Please note that if you disable your passcode authentication after executing the `recover` function, you will need to run the `recover` function again to continue using your Portal wallet.
{% endhint %}

## Custodian Recover

Recovering your custodian MPC share is done by Portal retrieving the custodian backup share via webhook and generating new signing and backup shares with the client. Portal will then send the newly generated backup share to your server using the webhook created in [Backup](../../react-native/legacy-documentation/back-up-a-wallet/#implementing-the-webhook).

In order to support custodian MPC recovery, one dependencies must be met:

1. Your server must support the /{webhook\_root}/backup/fetch webhook to provide Portal your existing custodian backup share

### **Implementing the webhook**

Portal will request the existing custodian backup share with a `POST` request to `/{webhook_root}/backup/fetch`.

The request body of this `POST` request will contain one field:

* `clientId` - The Portal `clientId` of the user

```typescript
app.post('/webhook/backup/fetch', async (req: Request, res: Response) => {
  const { clientId } = req.body
  
  // example db function getting a user from the "users" table by clientId
  const user = await db.users.find({
    where: {
      clientId: clientId,
    }
  })
  
  res.status(200).send({ backupShare: user.backupShare })
})
```
