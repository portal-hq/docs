# Sign Ethereum transactions

So far you have been able to create a Portal client and create their MPC wallets. Now, lets sign a transaction and send someone 1 gwei of ETH (the smallest denomination of ETH) over the Sepolia testnet.

### Steps

1. In order to send your first transaction, you’ll first need to fund your wallet. Head over to [Alchemy’s Sepolia](https://sepoliafaucet.com/) faucet to claim some Sepolia ETH.
2.  Next, we’ll need to format a transaction object.

    Here is the example transaction:

```
    var transaction = {
      "value": "0x01",
      "from": data.address,
      "to": "0xcae0d97d201ad54275b6e8a6b547c7611ad47963",
      "data": ""
    };
```

3. Now that we some testnet ETH, let's send a transaction! We will send our test funds to a Portal test wallet using a single API request to `https://mpc-client.portalhq.io/v1/sign` .
   1. You can learn more about Ethereum transactions here, but for this example you only need to know about a few fields:
      1. `method`: this defines which Ethereum RPC signer method we want to use.
      2. `params` : these are the parameters of your transaction.
         * `value` : this is the amount of ETH measured in Wei and hex encoded.
         * `from`: this is the address of the sender - your wallet in this case.
         * `to`: this is the address you’re sending to - our test wallet in this case.
         * `data`: this is used for interacting with smart contracts - we can ignore it.

In the example below we are using the `eth_sendTransaction` Ethereum RPC method to instruct the Portal API to both _sign_ and _submit_ a transaction to the blockchain.

<pre class="language-bash"><code class="lang-bash">curl -X POST 'https://mpc-client.portalhq.io/v1/sign' \
<strong>  -H 'Content-Type: application/json' \
</strong>  -H 'Authorization: Bearer &#x3C;clientApiKey>' \
  -d '{
    "share": $share,
    "method": "eth_sendTransaction",
    "params": "{\"value\": \"0x01\",\"from\": \"YOUR_ETH_ADDRESS\",\"to\": \"0xcae0d97d201ad54275b6e8a6b547c7611ad47963\",\"data\": \"\"}",
    "rpcUrl": "https://sepolia.infura.io/v3/f6a2916eba63419fb60fdd3b30797ef1",
    "chainId": "eip155:11155111"
  }'
</code></pre>

{% hint style="info" %}
If your wallet is not funded, you will receive a RPC error on this step.
{% endhint %}

Congrats! 🎉 You have successfully created a wallet and signed a transaction!
