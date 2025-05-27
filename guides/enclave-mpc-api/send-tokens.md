---
description: >-
  Here's exactly how you can send tokens from your Portal wallet to another
  address.
---

# Send tokens

## Fund your Wallet

Now that you have a wallet, the next step is to get test tokens for it. You can fund your wallet using the Fund wallet with testnet tokens Client API endpoint. If you are looking for a greater variety of test tokens, we recommend exploring our [faucets page](../../resources/testnet-faucets.md).

{% hint style="info" %}
The `chainId` will need to be a [CAIP-2](https://github.com/ChainAgnostic/CAIPs/blob/main/CAIPs/caip-2.md) compliant Chain ID. For more info on Chain ID formatting, see [this doc](../../resources/chain-id-formatting.md).
{% endhint %}

```bash
curl --request POST \
  --url https://api.portalhq.io/api/v3/clients/me/fund \
  --header 'Authorization: Bearer [clientApiKey|clientSessionToken]' \
  --header 'Content-Type: application/json' \
  --data '{
	"chainId": "eip155:11155111",
	"token": "NATIVE",
	"amount": "0.01"
}'
```

## Sending Tokens from your Wallet

Portal provides two ways to send transactions:

1. The Send Asset Enclave MPC API endpoint - Simply send tokens from your Portal wallet.
2. The Sign Enclave MPC API endpoint - Create and submit your own custom transactions from your Portal wallet.

For most use cases, we recommend using the Send Asset Enclave MPC API endpoint as shown in the examples below.

{% tabs %}
{% tab title="EVM" %}
The below example sends `0.0001` ETH on Ethereum Sepolia from your Portal client's wallet.

```sh
curl --request POST \
  --url https://mpc-client.portalhq.io/v1/assets/send \
  --header 'Authorization: Bearer [clientApiKey|clientSessionToken]' \
  --header 'Content-Type: application/json' \
  --data '{
	"share": "SECP256K1.share",
	"rpcUrl": "https://api.portalhq.io/rpc/v1/eip155/11155111",
	"chain": "sepolia",
	"to": "0xDestinationAddress",
	"token": "NATIVE",
	"amount": ".0001"
}'
```
{% endtab %}

{% tab title="Solana" %}
The below example sends `0.0001` SOL on Solana Devnet from your Portal client's wallet.

{% hint style="info" %}
You will need **`SOL`** to submit a Solana transaction, which is not currently supported by **`POST /api/v3/clients/me/fund`**. You can find a faucet to get test **`SOL`** tokens [here](../../resources/testnet-faucets.md).
{% endhint %}

```bash
curl --request POST \
  --url https://mpc-client.portalhq.io/v1/assets/send \
  --header 'Authorization: Bearer [clientApiKey|clientSessionToken]' \
  --header 'Content-Type: application/json' \
  --data '{
	"share": "ED25519.share",
	"rpcUrl": "https://api.portalhq.io/rpc/v1/solana/EtWTRABZaYq6iMfeYKouRu166VU2xqa1",
	"chain": "solana-devnet",
	"to": "0xDestinationAddress",
	"token": "NATIVE",
	"amount": ".0001"
}'
```
{% endtab %}

{% tab title="Bitcoin" %}
The below example sends `0.0001` BTC on Bitcoin Testnet from your Portal client's `p2wpkh` wallet.

{% hint style="info" %}
You will need **`BTC`** to submit a Solana transaction, which is not currently supported by **`POST /api/v3/clients/me/fund`**. You can find a faucet to get test **`BTC`** tokens [here](../../resources/testnet-faucets.md).
{% endhint %}

{% hint style="info" %}
The `chain` request body parameter for sending `BTC` can be one of:

* `bitcoin-segwit` - The Portal client's P2WPKH address on Bitcoin mainnet.
* `bitcoin-p2wpkh` - The Portal client's P2WPKH address on Bitcoin mainnet.
* `bitcoin-segwit-testnet` - The Portal client's P2WPKH address on Bitcoin testnet.
* `bitcoin-p2wpkh-testnet` - The Portal client's P2WPKH address on Bitcoin testnet.
{% endhint %}

```bash
curl --request POST \
  --url https://mpc-client.portalhq.io/v1/assets/send \
  --header 'Authorization: Bearer [clientApiKey|clientSessionToken]' \
  --header 'Content-Type: application/json' \
  --data '{
	"share": "SECP256K1.share",
	"chain": "bitcoin-segwit-testnet",
	"to": "tb1qDestinationAddress",
	"token": "NATIVE",
	"amount": ".0001"
}'
```
{% endtab %}
{% endtabs %}

You just sent your first token from your Portal wallet, that's awesome! :tada:

You may have a more advanced use case than simply sending tokens from your Portal wallet. Next, we will dive into how to build your own custom transaction and also how to sign it.
