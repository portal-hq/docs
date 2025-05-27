---
description: >-
  Use wallet recovery to enable your users to create multiple signing shares
  across their devices.
---

# Cross-device sessions

Once users successfully back up their wallets, they can generate a new set of signing shares irrespective of the SDK in use. Furthermore, both the new and old sets of signing shares can be used simultaneously, thanks to **`portal.recoverWallet`**. This provisioning relies on the user already having a successful set of backup shares (in the below example we'll assume they are password backup shares).

While recovering, the wallet fetches the encryption key from the user's cloud storage provider to decrypt the encrypted user backup share. Note that our SDK handles the decryption; you only need to supply the encrypted user backup share that was stored by your API.

For wallet recovery support, consult the [recovery documentation](recover-a-wallet.md) to establish the necessary **`/backup/fetch`** webhook. Also, ensure the user has successfully completed the backup process.

Here's how to implement **`portal.recoverWallet`**:

```swift
// The CipherText decoding object
struct CipherTextResult: Codable {
  var cipherText: String
}

// Your API URL
let yourApiUrl = "https://YOUR_API_URL.com"

// The user id
let userId = "THE-USER-ID"

// Set the user's password.
// Important note: this is only needed for the password BackupMethod
try portal.setPassword("THE-USER-PASSWORD")

// Obtain your API's URL for retrieving the encrypted user backup share.
// TODO: - replace the `BackupMethods.Password.rawValue` with the proper backup method you are using
guard let url = URL(string: "\(yourApiUrl)/users/\(userId)/encrypted-user-backup-shares?backupMethod=\(BackupMethods.Password.rawValue)") else {
  throw URLError(.badURL)
}

// Retrieve the encrypted user backup share on your API.
let requests = PortalRequests()
let yourApiResponse = try await requests.get(url)
let decodedResponse = try decoder.decode(CipherTextResult.self, from: yourApiResponse)
let encryptedUserBackupShare = decodedResponse.cipherText

// TODO: - replace the `.Password` with the proper backup method you are using
try await portal?.recoverWallet(.Password, withCipherText: encryptedUserBackupShare) { _ in
  // (Optional) Create a progress indicator here in the progress callback.
}

// ✅ The user has now recovered with their password successfully!
```

And that's it! You're now equipped to support multiple sessions across different devices for your users.
