---
description: >-
  Use wallet provisioning to enable your users to create multiple signing shares
  across their devices.
---

# Cross-device sessions

Once users successfully back up their wallets, they can generate a new set of signing shares irrespective of the SDK in use. Furthermore, both the new and old sets of signing shares can be used simultaneously, thanks to **`portal.provisionWallet`**. This provisioning relies on the user already having a successful set of backup shares.

While provisioning, the wallet fetches the encryption key from the user's cloud storage provider to decrypt the encrypted client backup share. Note that our SDK handles the decryption; you only need to supply the encrypted client backup share.

For wallet provisioning support, consult [the recovery documentation](recover-a-wallet.md) to establish the necessary **`/backup/fetch`** webhook. Also, ensure the user has successfully completed the backup process.

Here's how to implement **`portal.provisionWallet`**:

```tsx
import axios from 'axios'
import React, { FC } from 'react'
import { Button, View } from 'react-native'
import { BackupMethods, usePortal } from '@portal-hq/core'

const ProvisionWalletButton: FC = () => {
  const portal = usePortal()
  
  const handleProvisionWallet = async () => {
    // Get the existing encrypted client backup share from your API.
    const backupShare = await axios.get('/clients/[clientId]/backup')
    
    // Set the new signing share by running provisionWallet.
    await portal.provisionWallet(
      backupShare,
      BackupMethods.GoogleDrive, /* or BackupMethods.iCloud */
    )
  }
  
  return (
    <View>
      <Button onPress={handleProvisionWallet} title="Provision your wallet" />
    </View>
  )
}

 export default ProvisionWalletButton 
```

And that's it! You're now equipped to support multiple sessions across different devices for your users.
