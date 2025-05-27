# Backup Options

### Password/Pin Backup

Allow customers to create a password/pin. Customers can either remember the password or store it in a password storage manager.

#### Implementation Requirements

1. Create a UI for password input.&#x20;
2. Enforce password requirements. Customer can choose between password, PIN code, passcode, or any other text-based input.
3. If user forgets password there are no additional recovery options.

{% code overflow="wrap" %}
```typescript
import React, { FC } from 'react'
import { Button, View } from 'react-native'
import axios from 'axios'
import { BackupMethods, usePortal } from '@portal-hq/core'

const BackupButton: FC = () => {
  const [password, setPassword] = useState<string>('')
  const portal = usePortal()
  
  const handleBackup = async () => {
    // Get an encryped client backup share from running backup.
    const backupShare = await portal.backupWallet(BackupMethods.Password, { passwordStorageConfig: { pasword } })
    
    try {
      // Send the backup share to your API and store it.
      await axios.post('{your_server}/clients/[clientId]/backup', {
        data: { backupShare }
      })

      // ✅ Notify Portal that the client backup share was stored! 🙌
      await portal.api.storedClientBackupShare(true)
    } catch (error) {
      // ❌ Notify Portal that the client backup share was not stored.
      await portal.api.storedClientBackupShare(false)
    }
  }
  
  return (
    <View>
        <TextInput
          autoCapitalize="none"
          autoCorrect={false}
          onChangeText={setPassword}
          placeholder="Password/Pin"
          placeholderTextColor="#6B6E73"
          spellCheck={false}
          style={styles.input}
          value={password}
        />
      <Button onPress={handleBackup} title="Backup your wallet" />
    </View>
  )
}

export default BackupButton
```
{% endcode %}

### iCloud Backup&#x20;

See the docs on how to set up [iCloud](../../../../resources/backup-options/icloud.md).

### Google Drive Backup

See the docs on how to set up [GDrive](../../../../resources/backup-options/gdrive.md).
