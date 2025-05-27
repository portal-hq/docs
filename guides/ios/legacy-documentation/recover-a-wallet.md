---
description: >-
  This guide will walk you through the recovery process in the event that the
  user needs to recover their wallet.
---

# Recover a wallet

MPC recovery allows your users to recover their MPC wallets in the event that their device is lost, stolen, or bricked.&#x20;

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
```
{% endcode %}

### Updating Your Mobile App

In order to initiate the recovery process from your mobile app, you must perform two steps:

1. Retrieve the encrypted client backup share from your API.
2. Get a new encrypted client backup share from running `portal.mpc.recover`.

{% hint style="info" %}
Ensure you have created and connected the UI element in the storyboard to the action.
{% endhint %}

```swift
import PortalSwift

struct CipherTextResult: Codable {
  var cipherText: String
}

class ViewController: UIViewController {
  public var portal: Portal?
  public var yourApiUrl: String = "https://YOUR_API_URL.com"

  @IBAction func handleRecover(_ sender: UIButton!) {

    // Get the existing encrypted client backup share from your API.
    let request = HttpRequest<CipherTextResult, [String : String]>(
      url: self.yourApiUrl + "clients/[clientId]/backup",
      method: "GET",
      body: [:],
      headers: ["Content-Type": "application/json"]
    )

    // Send the request.
    request.send() { (result: Result<Any>) in

      guard result.error == nil else {
        // ❌ Handle errors retrieving the backup share.
        return
      }

      // Get the existing backup share from the response.
      let response = result.data as! Dictionary<String, String>
      let backupShare = response["backupShare"]!

      // Provide the backupShare and the backup method to recover.
      self.portal?.mpc.recover(
        cipherText: backupShare,
        method: BackupMethods.iCloud.rawValue
      ) { (result: Result<String>) -> Void in
        // ❌ Handle recover errors.
        guard result.error == nil else {
          guard let mpcError = result.error as? MpcError else {
            // Handle unexpected error types here. Re-run recover.
            return
          }
  
          default:
            // Handle any other errors recovering the wallet. Re-run recover.
            return
          }
        }
      }  progress: { status in
        print("Recover Status: ", status)
      }
    }
  }

```

{% hint style="danger" %}
**WARNING**: To recover a wallet with the Portal SDK, your device must be configured to use passcode authentication. Please note that if you disable your passcode authentication after executing the `recover` function, you will need to run the `recover` function again to continue using your Portal wallet.
{% endhint %}

### Status Flow

```swift
case readingShare = "Reading share"
case decryptingShare = "Decrypting share"
case parsingShare = "Parsing share" 
case recoveringSigningShare = "Recovering signing share"
case generatingShare = "Generating share"
case parsingShare = "Parsing share" 
case storingShare = "Storing share"
case done = "Done"
```

That's it! You have now protected your users in the event they lose their device 💪

Next, we will dive into how to use Portal's `WebViewController` so that your users can explore your own catered web3 ecosystem!
