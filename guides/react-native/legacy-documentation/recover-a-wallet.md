---
description: >-
  This guide will walk you through the recovery process in the event that the
  user needs to recover their wallet.
---

# Recover a wallet

MPC recovery allows your users to recover their MPC wallets in the event that their device is lost, broken, or stolen.&#x20;

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
})
```
{% endcode %}

### Updating Your Mobile App

In order to initiate the recovery process from your mobile app, you must perform two steps:

1. Retrieve the encrypted client backup share from your API.
2. Get a new encrypted client backup share from running `portal.mpc.recover`.

{% code title="RecoveryButton.tsx" lineNumbers="true" %}
```typescript
import axios from 'axios'
import React, { FC } from 'react'
import { Button, View } from 'react-native'
import { BackupMethods, usePortal } from '@portal-hq/core'

const RecoveryButton: FC = () => {
  const portal = usePortal()
  
  const handleRecovery = async () => {
    // Get the existing encrypted client backup share from your API.
    const backupShare = await axios.get('/clients/[clientId]/backup')
    
    // Set the new signing share by running recover.
    await portal.mpc.recover(
      backupShare,
      BackupMethods.GoogleDrive, /* or BackupMethods.iCloud */
    )
  }
  
  return (
    <View>
      <Button onPress={handleRecovery} title="Recover your wallet" />
    </View>
  )
}

 export default RecoveryButton 
```
{% endcode %}

{% hint style="danger" %}
**WARNING**: To recover a wallet with the Portal SDK, your device must be configured to use passcode authentication. Please note that if you disable your passcode authentication after executing the `recover` function, you will need to run the `recover` function again to continue using your Portal wallet.
{% endhint %}

## Custodian Recover

Recovering your custodian MPC share is done by Portal retrieving the custodian backup share via webhook and generating new signing and backup shares with the client. Portal will then send the newly generated backup share to your server using the webhook created in [Backup](back-up-a-wallet/#implementing-the-webhook).

In order to support custodian MPC recovery, one dependencies must be met:

1. Your server must support the `/{webhook_root}/backup/fetch` webhook to provide Portal your existing custodian backup share.

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

## Next steps

Portal is all set up in your app! Now you can explore all that Portal has to offer.

{% content-ref url="../../../reference/react-native/portal-hq-core.md" %}
[portal-hq-core.md](../../../reference/react-native/portal-hq-core.md)
{% endcontent-ref %}

{% content-ref url="broken-reference" %}
[Broken link](broken-reference)
{% endcontent-ref %}

{% content-ref url="../../../reference/react-native/storage-adapters/" %}
[storage-adapters](../../../reference/react-native/storage-adapters/)
{% endcontent-ref %}
