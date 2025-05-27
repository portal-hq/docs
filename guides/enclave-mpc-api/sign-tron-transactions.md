# Sign Tron transactions

Signing a Tron transaction is identical to how we sign an Ethereum or Solana transaction with the exception that we need to pass in the Tron specific RPC URL, chain ID, and method.&#x20;

{% hint style="info" %}
You can get an RPC URL for Tron from [www.trongrid.io](https://www.trongrid.io/).
{% endhint %}

In the example we are going to update:

* `chainID` to `tron:nile`
* `rpcUrl` to `grpc.nile.trongrid.io:50051?api_key=<API_KEY>`
* `method` to `tron_sendTransaction`
* `params` to a base64 encoded serialized Tron transaction

Here is a short snippet of how to get the `params` using `tronweb`, for a complete example checkout [this](https://github.com/portal-hq/Enclave-MPC-API-Examples/blob/main/Javascript/tron-sign.js) example repo:

```javascript
const tnw = require('tronweb');

const tronWeb = new tnw.TronWeb({
    fullHost: 'https://nile.trongrid.io',
    headers: { 'TRON-PRO-API-KEY': 'API_KEY' },
  });
  const fromTronHexAddress = tronWeb.address.toHex(tronAddress);
  const toHexAddress = '418840E6C55B9ADA326D211D818C34A994AECED808';
  const transactionBuidlerParsa = new tnw.TransactionBuilder(tronWeb);


  const transaction = await transactionBuidlerParsa.sendTrx(
    toHexAddress,
    1,
    fromTronHexAddress,
  );

  // // Serialize the transaction
  const txPb = tronWeb.utils.transaction.txJsonToPb(transaction)
  const rawData = txPb.getRawData().serializeBinary();

  // // Base64 encode the serialized transaction
  const base64Transaction = Buffer.from(rawData).toString('base64');

  // Sign the transaction
  const signResponse = await axios.post(
    `${PORTAL_MPC_CLIENT_URL}/v1/sign`,
    {
      share: shares.SECP256K1.share,
      method: 'tron_sendTransaction',
      rpcUrl,
      params: base64Transaction,
      chainId: 'tron:nile',
    },
    {
      headers: { Authorization: `Bearer ${clientApiKey}` },
    },
  );
```

Below is an example curl command for signing a Tron message:

```bash
curl -X POST 'https://mpc-client.portalhq.io/v1/sign' \
  -H 'Content-Type: application/json' \
  -H 'Authorization: Bearer <clientApiKey>' \
  -d '{
    "share": $share,
    "method": "tron_sendTransaction",
    "params": $base64EncodedTransaction,
    "rpcUrl": "grpc.nile.trongrid.io:50051?api_key=<API_KEY>",
    "chainId": "tron:nile"
  }'
```

{% hint style="info" %}
The RPC URL for Tron uses the GRPC protocol, not HTTP.
{% endhint %}

### Supported Methods and ChainIds

**Methods:**

* `tron_sendTransaction`

**ChainIds:**

* `tron:mainnet`
* `tron:nile`
* `tron:shasta`
