---
description: >-
  Use wallet provisioning to enable your users to create multiple signing shares
  across their devices.
---

# Cross-device sessions

Once users successfully back up their wallets, they can generate a new set of signing shares irrespective of the SDK in use. Furthermore, both the new and old sets of signing shares can be used simultaneously, thanks to **`portal.provisionWallet`**. This provisioning relies on the user already having a successful set of backup shares.

While provisioning, the wallet fetches the encryption key from the user's cloud storage provider to decrypt the encrypted client backup share. Note that our SDK handles the decryption; you only need to supply the encrypted client backup share.

For wallet provisioning support, consult [the recovery documentation](legacy-documentation/recover-a-wallet.md) to establish the necessary **`/backup/fetch`** webhook. Also, ensure the user has successfully completed the backup process.

Here's how to implement **`portal.provisionWallet`**:

```tsx
const ProvisionButton: FC = () => {
  const handleProvision = async () => {
    // Get the existing encrypted client backup share from your API.
    const backupShare = await axios.get('/clients/[clientId]/backup')
    const backupMethod = "GDRIVE" // Use the backupMethod that was used by the client when they backed up their wallet.
    const backupConfig = {}

    // Get a new signing share from running provisionWallet.
    await portal.provisionWallet(backupShare, backupMethod, backupConfig)
  }
  
  return (
    <button onPress={handleProvision}>Provision your wallet</button>
  )
}

 export default ProvisionButton
```

And that's it! You're now equipped to support multiple sessions across different devices for your users.
