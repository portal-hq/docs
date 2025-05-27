---
description: >-
  This guide will walk you through how to use your users' backups to recover
  their wallet.
---

# Recover a wallet

## Portal-Managed Recovery (default)

When using Portal-Managed Backups, you can simply call the `portal.recoverWallet()` function to generate new signing shares on your user's device.

<pre class="language-typescript"><code class="lang-typescript">// With iCloud
await portal.recoverWallet(.iCloud)
<strong>
</strong><strong>// With Password
</strong>portal.setPassword("THE-USER-PASSWORD")
await portal.recoverWallet(.Password)

// With Google Drive
await portal.recoverWallet(.GoogleDrive)

// With Passkeys
await portal.recoverWallet(.Passkey)
</code></pre>

{% hint style="danger" %}
**WARNING**: To recover a wallet with the Portal SDK, your device must be configured to use passcode authentication. Please note that if you disable your passcode authentication after executing the `recover` function, you will need to run the `recover` function again.
{% endhint %}

## Self-Managed Recovery

By default, Portal manages storing both the encrypted client backup share and the custodian backup share for you. If you prefer to store and manage the backup shares in your own infrastructure instead of using Portal-Managed Backups, see our Self-Managed Backups guide.

Before recovering, you will need to retrieve the encrypted client backup share from your API. You will then provide the encrypted client backup share to `portal.recoverWallet`. Here's an example of how that might look in your code:

<pre class="language-swift"><code class="lang-swift"><strong>// The CipherText decoding object
</strong><strong>struct CipherTextResult: Codable {
</strong>  var cipherText: String
}
<strong>
</strong>// Your API URL
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
</code></pre>

{% hint style="danger" %}
**WARNING**: To recover a wallet with the Portal SDK, your device must be configured to use passcode authentication. Please note that if you disable your passcode authentication after executing the `recover` function, you will need to run the `recover` function again.
{% endhint %}

## Progress Callbacks

You can learn how to handle the progress callbacks for `portal.recoverWallet` here.

## Next steps

Amazing! Your users can now easily recover their wallet. Next let's dive into handling sessions across multiple devices for your users.



**Related Documentation**

* [recoverWallet function reference](https://docs.portalhq.io/reference/ios/recoverwallet)
* [Backup options](https://docs.portalhq.io/resources/backup-options)
* [Self-Managed Backups](../../resources/self-managed-backups.md)
* [MPC progress callbacks](../web/mpc-progress-callbacks.md)
* [setPassword function reference](https://docs.portalhq.io/reference/ios/setpassword)
* [isWalletBackedUp function reference](https://docs.portalhq.io/reference/ios/iswalletbackedup)
