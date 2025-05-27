# Sign Stellar Transaction

Signing a Stellar transaction is identical to how we sign an Ethereum or Solana transaction with the exception that we need to pass in the Stellar specific RPC URL, chain ID, and method.&#x20;

{% hint style="info" %}
Before you submit a Stellar transaction, ensure that the Stellar account is funded and created on-chain. Failing to do so will result in an `address not found` error.
{% endhint %}

In the example we are going to update:

* `chainID` to `stellar:pubnet`
* `rpcUrl` to `http://horizon.stellar.org`
* `method` to `stellar_sendTransaction`
* `params` to a base64 encoded serialized Stellar transaction

Here is a short snippet of how to get the `params` using the stellar SDK. Note that in this example we assume the destination address is already created. For a complete example checkout [this](https://github.com/portal-hq/Enclave-MPC-API-Examples/blob/main/Javascript/stellar-sign.js) example repo:

```javascript

const axios = require('axios');
const StellarSdk = require('stellar-sdk');

var rpcUrl = 'https://horizon-testnet.stellar.org';

const server = new StellarSdk.Horizon.Server(rpcUrl);

const destinationAddress = "GDDRVYLDWMDZ2YEPDN6CIXD3ZJMCKTA4VEPZA2UYZBXSZDWIUMPGZE42"

await server.loadAccount(destinationAddress);

const sourceAccount = await server.loadAccount(stellarAddress);
const transaction = new StellarSdk.TransactionBuilder(sourceAccount, {
  fee: StellarSdk.BASE_FEE,
  networkPassphrase: StellarSdk.Networks.TESTNET, // Defaulting to testnet
})
  .addOperation(
    StellarSdk.Operation.payment({
      destination: destinationAddress, // example destination address
      asset: StellarSdk.Asset.native(), // Send XLM
      amount: '1', // amount to send
    }),
  )
  .setTimeout(30)
  .build();

// Serialize the transaction to XDR
const serializedTransaction = transaction.toXDR()

// // Convert the serialized Buffer to a Base64 string
const base64Transaction = serializedTransaction.toString('base64');

// // Sign the transaction
const signResponse = await axios.post(
  `${PORTAL_MPC_CLIENT_URL}/v1/sign`,
  {
    share: shares.ED25519.share,
    method: 'stellar_sendTransaction',
    rpcUrl,
    params: base64Transaction,
    chainId: 'stellar:testnet',
  },
  {
    headers: { Authorization: `Bearer ${clientApiKey}` },
  },
);

```

Below is an example curl command for signing a Stellar message:

```bash
curl -X POST 'https://mpc-client.portalhq.io/v1/sign' \
  -H 'Content-Type: application/json' \
  -H 'Authorization: Bearer <clientApiKey>' \
  -d '{
    "share": $share,
    "method": "stellar_sendTransaction",
    "params": $base64EncodedTransaction,
    "rpcUrl": "https://horizon-testnet.stellar.org",
    "chainId": "stellar:testnet"
  }'
```

### Supported Methods and ChainIds

**Methods:**

* `stellar_sendTransaction`

**ChainIds:**

* `stellar:pubnet`
* `stellar:testnet`
