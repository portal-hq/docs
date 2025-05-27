# Concurrent Transactions

In some cases, you might want to submit multiple transactions that will be processed in sequence by the network. This is useful for scenarios like batch operations or when you need to ensure transactions are processed in a specific order. Here's how to submit multiple transactions with sequential nonces.

## Understanding Nonces

Before submitting concurrent transactions, it's important to understand nonces:

* Every Ethereum transaction requires a nonce
* Nonces must be used in sequence (0, 1, 2, etc.)
* The network will process transactions in nonce order
* Multiple pending transactions can exist in the mempool **as long as they have** **sequential nonces**
* Submitting multiple transactions with the same nonce will result in the transaction with the highest gas overriding others

## Steps

1. First, retrieve your current nonce from the network. This ensures you start with the correct sequence number.

```javascript
const web3 = new Web3("YOUR_RPC_URL");
const currentNonce = await web3.eth.getTransactionCount(walletAddress);
```

2. Format your transaction objects with incrementing nonces. Here's an example transaction format:

```javascript
const transactionParams = {
  nonce: "0x" + nonce.toString(16),  // Convert nonce to hex
  value: "0x1",
  from: "YOUR_ETH_ADDRESS",
  to: "RECIPIENT_ADDRESS",
  data: "0x",
  maxFeePerGas: "0x2540BE400",        // 10 Gwei
  maxPriorityFeePerGas: "0x3B9ACA00", // 1 Gwei
  gasLimit: "0x5208"                   // 21000
};
```

3. Submit multiple transactions using the Portal API. Each request should use the `/v1/sign` endpoint with `eth_sendTransaction` method:

```bash
curl -X POST 'https://mpc-client.portalhq.io/v1/sign' \
-H 'Content-Type: application/json' \
-H 'Authorization: Bearer <clientApiKey>' \
-d '{
  "share": $share,
  "method": "eth_sendTransaction",
  "params": "{\"nonce\": \"0x0\", \"value\": \"0x01\", \"from\": \"YOUR_ETH_ADDRESS\", \"to\": \"RECIPIENT_ADDRESS\", \"data\": \"0x\", \"maxFeePerGas\": \"0x2540BE400\", \"maxPriorityFeePerGas\": \"0x3B9ACA00\", \"gasLimit\": \"0x5208\"}",
  "rpcUrl": "YOUR_RPC_URL",
  "chainId": "eip155:11155111"
}'
```

4. Repeat the request for each transaction, incrementing the nonce each time.

## Important Notes

* All transactions will enter the mempool but may not be processed immediately
* Transactions will be processed in nonce order, regardless of submission time
* If a transaction with a lower nonce fails, subsequent transactions will remain pending
* Consider adding delays between submissions to prevent rate limiting
* Make sure your wallet has sufficient funds to cover all transactions

{% hint style="info" %}
If any transaction in the sequence fails due to insufficient funds or other errors, subsequent transactions with higher nonces will remain pending until the issue is resolved.
{% endhint %}
