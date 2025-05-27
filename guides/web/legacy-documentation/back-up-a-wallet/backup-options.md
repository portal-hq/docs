# Backup Options

### Passkey + Enclave Backup

Allow customers to create a passkey authenticate and store the private encryption key within a secure enclave.&#x20;

#### Implementation Requirements

1. Initialize the portal config object with a passkey object. &#x20;
2. The passkey object takes in a relying party.
   1. Set the relying party to be a registrable sub-domain of your domain.

```typescript
const BackupButton: React.FC = () => {
  const handleBackup = async () => {
    // Get an encryped client backup share from running backup.
    const backupShare = await portal.backupWallet(BackupMethods.Passkey)
    
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

### Password/Pin Backup

Allow customers to create a password/pin. Customers can either remember the password or store it in a password storage manager.

#### Implementation Requirements

1. Create a UI for password input.&#x20;
2. Enforce password requirements. Customer can choose between password, PIN code, passcode, or any other text-based input.
3. If user forgets password there are no additional recovery options.

{% code overflow="wrap" %}
```typescript
const BackupButton: React.FC = () => {
  const handleBackup = async () => {
    // Get an encryped client backup share from running backup.
    const backupShare = await portal.backupWallet(BackupMethods.Password, { passwordStorage: { password } })
    
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

### Google Drive Backup

See the docs on how to[gdrive.md](../../../../resources/backup-options/gdrive.md "mention")
