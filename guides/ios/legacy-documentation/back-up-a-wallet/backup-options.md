# Backup Options

### Passkey + Enclave Backup

Allow customers to create a native passkey on their device that is used to authenticate into a secure enclave that holds the encryption key for the user. Customer's passkeys are backed up to the native cloud storage for their device.&#x20;

#### Implementation Requirements

1. Initialize passkey storage as a backup option in the Portal Config Object.&#x20;

#### Use Portal as your relying party&#x20;

1. Add `backup.portalhq.io` as a web credential domain in your app.
2. Share your app bundle id with the Portal Team.

#### Use your own domain as the relying party

Ensure you have set up your associate domain correctly in your app and that you are serving an aasa file from whatever your relying party domain is set to. You will need to be sure you have the `webcredential` field set properly for your app in your aasa file.&#x20;

Resources from apple:

* [Configuring an associated domain](https://developer.apple.com/documentation/xcode/configuring-an-associated-domain#Provide-an-Apple-App-Site-Association-file)
* [Supporting associated domains](https://developer.apple.com/documentation/xcode/supporting-associated-domains#Add-the-associated-domain-file-to-your-website)

### Relying party

A relying party is a trusted domain that is tied to the public key credentials of your users for their passkey . We offer the option to use `portalhq.io` as your relying party domain. It requires you to add `portalhq.io` as an Associated Domain in your iOS application and share your team id + application bundle id. If you already have your domain as a `webcredential` for your application then you can simply pass in your domain as the relying party and everything should work.&#x20;

### Example

```swift
  import PortalSwift

class ViewController: UIViewController {
  public var portal: Portal?
  public var yourApiUrl: String = "https://YOUR_API_URL.com"
  
  @IBAction func handleBackup(_: UIButton!) {
    self.portal?.backupWallet(method: BackupMethods.Passkey.rawValue) {
      (result: Result<String>) in

      guard result.error == nil else {
        // ❌ Handle errors backing up the user's wallet.
        return
      }

      // We now have the encrypted client backup share.
      let newBackupShare = result.data!
      
      // Store the backup share on your server
      // This is an example function call as a placeholder 
      // for your business logic
      storeBackupShareOnServer(share: newBackupShare)
    } progress: { status in
      print("Backup Status: ", status)
    }
  }
}
```

### Password/Pin Backup

Allow customers to create a password/pin. Customers can either remember the password or store it in a password storage manager.

#### Implementation Requirements

1. Create a UI for password input.&#x20;
2. Enforce password requirements. Customer can choose between password, PIN code, passcode, or any other text-based input.
3. If user forgets password there are no additional recovery options.

```swift
import PortalSwift

class ViewController: UIViewController {
  public var portal: Portal?
  public var yourApiUrl: String = "https://YOUR_API_URL.com"

  // Call this function whenever you want to prompt the user for a PIN
  func requestPassword(completion: @escaping (String?) -> Void) {
    // UI to get password/pin from the user...
 }
  
  @IBAction func handleBackup(_ sender: UIButton!) {
    self.requestPassword { password in
      guard let enteredPassword = password, !enteredPassword.isEmpty else {
        // Handle case where no password/pin was entered or the operation was canceled
        return
      }
      
      let backupConfigs = try! BackupConfigs(passwordStorage: PasswordStorageConfig(password: enteredPassword))
      
      portal.backupWallet(method: BackupMethods.Password.rawValue, backupConfigs: backupConfigs)  {
        (result: Result<String>) -> Void in
        
        guard result.error == nil else {
          // ❌ Handle errors backing up the user's wallet.
          return
        }
        
        // We now have the encrypted client backup share.
        let newBackupShare = result.data!
        
        // Store the backup share on your server
        // This is an example function call as a placeholder 
        // for your business logic
        storeBackupShareOnServer(share: newBackupShare)
      } progress: { status in
        print("Backup Status: ", status)
      }
    }
  }
}
```

### iCloud Backup&#x20;

See the docs on how to [icloud.md](../../../../resources/backup-options/icloud.md "mention")

### Google Drive Backup

See the docs on how to[gdrive.md](../../../../resources/backup-options/gdrive.md "mention")
