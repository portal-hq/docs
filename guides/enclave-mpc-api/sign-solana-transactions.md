# Sign Solana transactions

Signing a Solana transaction is identical to how we sign an Ethereum transaction with the exception that we need to pass in the Solana specific RPC URL, chain ID, and method. In the example we are going to update:

* `chainID` to `solana:4uhcVJyU9pJkvQyS88uRDiswHXSCkY3z`
* `rpcUrl` to `https://api.testnet.solana.com`
* `method` to `sol_signAndConfirmTransaction`
* `params` to a base64 encoded serialized Solana transaction

Here is a short snippet of how to get the `params` using `@solana/web3.js`, for a complete example checkout [this](https://github.com/portal-hq/Enclave-MPC-API-Examples/tree/main/Javascript) example repo:

```javascript
  const {
    Transaction,
    SystemProgram,
    LAMPORTS_PER_SOL,
  } = require('@solana/web3.js');

  let tx = new Transaction({
    recentBlockhash: blockhash,
    feePayer: feePayerAddress,
  }).add(
    SystemProgram.transfer({
      fromPubkey: fromPublicKey,
      toPubkey: toPublicKey,
      lamports: LAMPORTS_PER_SOL * 0.01, // 0.01 SOL
    }),
  );

  // Serialize the transaction to a Buffer
  const serializedTransaction = tx.serialize({ requireAllSignatures: false });

  // Convert the serialized Buffer to a Base64 string
  const base64Transaction = serializedTransaction.toString('base64');

  // Sign the transaction
  const signResponse = await axios.post(
    `${PORTAL_MPC_CLIENT_URL}/v1/sign`,
    {
      share: share,
      method: 'sol_signTransaction',
      params: base64Transaction,
      chainId: 'solana:5eykt4UsFv8P8NJdTREpY1vzqKqZKvdp',
    },
    {
      headers: { Authorization: `Bearer ${clientApiKey}` },
    },
  );
```

Below is an example curl command for signing a Solana message:

```bash
curl -X POST 'https://mpc-client.portalhq.io/v1/sign' \
  -H 'Content-Type: application/json' \
  -H 'Authorization: Bearer <clientApiKey>' \
  -d '{
    "share": $share,
    "method": "sol_signTransaction",
    "params": "AQAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAABAAEDqF/lgjmUoeVVHb3ObBSh4S4W1vrG333tiaGvUzWHYDLkuw5hIt53j8IqHX8I+gj28DXUsAE3bgbROXDHpUdhhAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAlNc9WKVngB8ANy0SFkCbEM6i7nZKuqAgsFEvoZkO4/ABAgIAAQwCAAAAECcAAAAAAAA=",
    "chainId": "solana:5eykt4UsFv8P8NJdTREpY1vzqKqZKvdp"
  }'
```
