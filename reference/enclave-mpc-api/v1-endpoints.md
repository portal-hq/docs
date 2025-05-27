---
description: >-
  These endpoints require a Client API Key or Client Session Token (CST) as a
  bearer token.
---

# V1 endpoints

## Clients

### Create the client's wallet

<mark style="color:purple;">**`POST`**</mark> `https://mpc-client.portalhq.io/v1/generate`

This endpoint generates MPC shares for both SECP256K1 (EVM, etc) and ED25519 (Solana, XRP, etc) curves (a Portal wallet).

{% hint style="warning" %}
After receiving MPC shares from this endpoint, be sure to verify the shares with [this endpoint](https://docs.portalhq.io/reference/client-api/v3-endpoints#update-signing-share-pairs-statuses) before attempting to sign.
{% endhint %}

#### Headers

| Name                                            | Type   | Description                   |
| ----------------------------------------------- | ------ | ----------------------------- |
| Authorization<mark style="color:red;">\*</mark> | String | Bearer **`<Client API Key>`** |
| Content-Type<mark style="color:red;">\*</mark>  | String | application/json              |

#### Body Parameters

You can leave the request body empty.

```json
{}
```

#### Example Request

Be sure to replace the `[token]` with either a Client API Key or a Client Session Token.

```bash
curl --request POST \
  --url https://mpc-client.portalhq.io/v1/generate \
  --header 'Authorization: Bearer [token]' \
  --header 'Content-Type: application/json' \
  --data '{}'
```

#### Example Response

{% tabs %}
{% tab title="200: OK Success" %}
```json
{
  "SECP256K1": {
    "share": "eyJjbG...",
    "id": "cm0r6nkek00ej10dwwx8wdjmf"
  },
  "ED25519": {
    "share": "eyJjbG...",
    "id": "cm0r6nk7c00hbzefaq9s93s0x"
  }
}
```
{% endtab %}

{% tab title="400: Bad Request" %}
```json
{
  "id": "DKG_FAILED",        // Short error identificator
  "message": "Some message"  // Optional: Details of the error
}
```
{% endtab %}

{% tab title="401: Unauthorized Request" %}
```
"Incorrect API key format"
```
{% endtab %}
{% endtabs %}

### Send the client's assets

<mark style="color:purple;">**`POST`**</mark> `https://mpc-client.portalhq.io/v1/assets/send`

This endpoint allows you to easily send a token the client holds to another address.

{% hint style="warning" %}
The **`Send Assets`** endpoint builds, signs, and submits a transaction to an RPC gateway.&#x20;

**Important:** A 200 response only confirms that the transaction was received by the RPC gateway — it does not mean the transaction is confirmed on-chain. The transaction must first be placed in the mempool of the RPC gateway nodes, submitted to miner/validator nodes, and then validated and finalized by the network. This process can take seconds to minutes, depending on the blockchain.
{% endhint %}

#### Headers

<table><thead><tr><th width="323">Name</th><th width="159">Type</th><th width="268">Description</th></tr></thead><tbody><tr><td>Authorization<mark style="color:red;">*</mark></td><td>String</td><td>Bearer <strong><code>&#x3C;Client API Key></code></strong></td></tr><tr><td>Content-Type<mark style="color:red;">*</mark></td><td>String</td><td>application/json</td></tr></tbody></table>

#### Body Parameters

<table><thead><tr><th width="165.18359375">Name</th><th width="307.16015625">Type</th><th>Description</th></tr></thead><tbody><tr><td><code>share</code><mark style="color:red;">*</mark></td><td>String</td><td>The MPC share generated from <a href="v1-endpoints.md#create-wallets">"Create Wallets"</a> for a specific curve.</td></tr><tr><td><code>chain</code><mark style="color:red;">*</mark></td><td><p>String</p><p></p><p><a href="../../resources/chain-id-formatting.md">CAIP-2 Chain ID</a> or one of: </p><p><code>"base"</code></p><p><code>"base-sepolia"</code></p><p><code>"ethereum"</code></p><p><code>"bitcoin-segwit"</code></p><p><code>"bitcoin-segwit-testnet"</code></p><p><code>"bitcoin-p2wpkh"</code></p><p><code>"bitcoin-p2wpkh-testnet"</code></p><p><code>"polygon"</code></p><p><code>"polygon-amoy"</code></p><p><code>"celo"</code></p><p><code>"celo-alfajores"</code></p><p><code>"sepolia"</code></p><p><code>"solana"</code></p><p><code>"solana-devnet"</code></p><p><code>"stellar"</code></p><p><code>"stellar-testnet"</code></p><p><code>"tron"</code></p><p><code>"tron-nile"</code></p><p><code>"tron-shasta"</code></p></td><td>The blockchain network.</td></tr><tr><td><code>to</code><mark style="color:red;">*</mark></td><td>String</td><td>The recipient's address.</td></tr><tr><td><code>token</code><mark style="color:red;">*</mark></td><td>String</td><td>The token contract address (for ERC20 tokens on EVM chains) or the mint address (for Solana SPL tokens). You can also use "USDC" or "USDT" for the respective tokens. For native token transfers, use <code>"NATIVE"</code>.</td></tr><tr><td><code>amount</code><mark style="color:red;">*</mark></td><td>String</td><td>The amount of tokens to send ("0.1" is 0.1 USDC)</td></tr><tr><td><code>rpcUrl</code></td><td>String</td><td>The node provider RPC URL. Not required for Solana or Solana Devnet.</td></tr><tr><td><code>nonce</code></td><td>String</td><td>The hex string of the nonce to set on the transaction ("0x01" sets the nonce of the transaction to 1). Note that the hex string needs to include the "0x" and for single digits to include a leading zero like "0x0".   </td></tr><tr><td><code>metadataStr</code></td><td>String</td><td>Optional metadata string for the transaction.</td></tr></tbody></table>

{% hint style="info" %}
When submitting multiple transactions for a single client in quick succession (or in parallel), each transaction may initially have the same nonce if the blockchain hasn’t processed the earlier ones yet. This can cause errors, as the RPC gateway interprets identical nonces as attempts to overwrite transactions in the mempool.

To prevent this, track the nonce in your application and increment it manually for each transaction sent to the **`Send Assets`** endpoint, ensuring unique nonces for concurrent requests.
{% endhint %}

#### Example Request

Be sure to replace the `[token]` with either a Client API Key or a Client Session Token.

```bash
curl --request POST \
  --url https://mpc-client.portalhq.io/v1/assets/send \
  --header 'Authorization: Bearer [token]' \
  --header 'Content-Type: application/json' \
  --data '{
	"share": "share",
	"chain": "ethereum",
	"to": "0xdFd8302f44727A6348F702fF7B594f127dE3A902",
	"token": "NATIVE",
	"amount": "0.0001"
}'
```

#### Example Response

{% tabs %}
{% tab title="200: OK Success" %}
{% hint style="warning" %}
If your client is using [Account Abstraction](../../resources/account-abstraction.md), then **`transactionHash`** is actually a **User Operation hash**. You can manually look up the user operation hash [here](https://jiffyscan.xyz/?network=sepolia).
{% endhint %}

```json
{
  "transactionHash": "0x02073df9ba08895d681f80ae45440ba57e766602c97ee6321df5ac81bca900cc",
  "metadata": {
    "amount": "0.01",
    "rawAmount": "10000000000000000",
    "tokenAddress": "0xfFf9976782d46CC05630D1f6eBAb18b2324d6B14",
    "tokenDecimals": 18
  }
}
```
{% endtab %}

{% tab title="400: Bad Request" %}
```json
{
  "id": "RPC_OP_FAILED", // Short error identificator
  "message": "Failed to send transaction: details" // Optional details
}
```
{% endtab %}

{% tab title="401: Unauthorized Request" %}
```
"Incorrect API key format"
```
{% endtab %}
{% endtabs %}

### Sign a transaction or message by chain

<mark style="color:purple;">**`POST`**</mark> `https://mpc-client.portalhq.io/v1/sign`

This endpoint allows you to sign a transaction or message by chain.

#### Headers

| Name                                            | Type   | Description                   |
| ----------------------------------------------- | ------ | ----------------------------- |
| Authorization<mark style="color:red;">\*</mark> | String | Bearer **`<Client API Key>`** |
| Content-Type<mark style="color:red;">\*</mark>  | String | application/json              |

#### Body Parameters

| Name                                        | Type   | Description                                                                                                |
| ------------------------------------------- | ------ | ---------------------------------------------------------------------------------------------------------- |
| `method`<mark style="color:red;">\*</mark>  | String | The signer RPC method (e.g. "personal\_sign", "eth\_sendTransaction", "sol\_signAndSendTransaction", etc). |
| `params`<mark style="color:red;">\*</mark>  | String | A JSON stringified array of the parameters for the RPC method (e.g. "\[\\"test\\"]").                      |
| `share`<mark style="color:red;">\*</mark>   | String | The MPC share generated from ["Create Wallets"](v1-endpoints.md#create-wallets) for a specific curve.      |
| `chainId`<mark style="color:red;">\*</mark> | String | The blockchain network (a CAIP-2 chain ID like "eip155:11155111" for Sepolia).                             |
| `to`<mark style="color:red;">\*</mark>      | String | The recipient's address.                                                                                   |
| `rpcUrl`<mark style="color:red;">\*</mark>  | String | The node provider RPC URL.                                                                                 |
| `metadataStr`                               | String | Optional metadata string for the transaction.                                                              |

#### Example Request

Be sure to replace the `[token]` with either a Client API Key or a Client Session Token.

```bash
curl --request POST \
  --url https://mpc-client.portalhq.io/v1/sign \
  --header 'Authorization: Bearer [token]' \
  --header 'Content-Type: application/json' \
  --data '{
	"share": "share",
	"method": "eth_sendTransaction",
	"params": "{\"value\": \"0x01\",\"from\": \"\",\"to\": \"0xdFd8302f44727A6348F702fF7B594f127dE3A902\",\"data\": \"\"}",
	"rpcUrl": "https://api.portalhq.io/rpc/v1/eip155/1",
	"chainId": "eip155:1"
}'
```

#### Example Response

{% tabs %}
{% tab title="200: OK Success" %}
<pre class="language-json"><code class="lang-json"><strong>{
</strong>  "data": "0xf7a6a07fa599db56fca50fa1202670b59054e7ed452ea57b3f5b43148b8bdb165beb3a4b0fd532162a1f0fd475de1a9ecd07c95186f7a0856ce1bffa45e3acc91b"
}
</code></pre>
{% endtab %}

{% tab title="400: Bad Request" %}
```json
{
  "id": "RPC_OP_FAILED", // Short error identificator
  "message": "Failed to send transaction: details" // Optional details
}
```
{% endtab %}

{% tab title="401: Unauthorized Request" %}
```
"Incorrect API key format"
```
{% endtab %}
{% endtabs %}

### Sign a transaction or message by curve

<mark style="color:purple;">**`POST`**</mark> `https://mpc-client.portalhq.io/v1/raw/sign/:curve`

This endpoint allows you to sign a raw message by curve.

#### Headers

| Name                                            | Type   | Description                   |
| ----------------------------------------------- | ------ | ----------------------------- |
| Authorization<mark style="color:red;">\*</mark> | String | Bearer **`<Client API Key>`** |
| Content-Type<mark style="color:red;">\*</mark>  | String | application/json              |

#### Path Parameters

| Name                                      | Type   | Description                                                                           |
| ----------------------------------------- | ------ | ------------------------------------------------------------------------------------- |
| `curve`<mark style="color:red;">\*</mark> | String | The curve you want to use (e.g. `SECP256K1` for EVM chains and `ED25519` for Solana). |

#### Body Parameters

| Name                                      | Type   | Description                                                                                           |
| ----------------------------------------- | ------ | ----------------------------------------------------------------------------------------------------- |
| `param`<mark style="color:red;">\*</mark> | String | A hex string of the digest to sign without the leading `0x` (e.g. `"param": "7369676e2074686973"`).   |
| `share`<mark style="color:red;">\*</mark> | String | The MPC share generated from ["Create Wallets"](v1-endpoints.md#create-wallets) for a specific curve. |

#### Example Request

Be sure to replace the `[token]` with either a Client API Key or a Client Session Token.

```bash
curl --request POST \
  --url https://mpc-client.portalhq.io/v1/raw/sign/SECP256K1 \
  --header 'Authorization: Bearer [token]' \
  --header 'Content-Type: application/json' \
  --data '{
	"params": "0x123",
	"share": "share"
}'
```

#### Example Response

{% tabs %}
{% tab title="200: OK Success" %}
<pre class="language-json"><code class="lang-json"><strong>{
</strong>  "data": "0xf7a6a07fa599db56fca50fa1202670b59054e7ed452ea57b3f5b43148b8bdb165beb3a4b0fd532162a1f0fd475de1a9ecd07c95186f7a0856ce1bffa45e3acc91b"
}
</code></pre>
{% endtab %}

{% tab title="400: Bad Request" %}
```json
{
  "id": "BAD_REQUEST", // Short error identificator
  "message": "Can't parse the request body" // Optional details
}
```
{% endtab %}

{% tab title="401: Unauthorized Request" %}
```
"Incorrect API key format"
```
{% endtab %}
{% endtabs %}

### Back up the client's wallet

<mark style="color:purple;">**`POST`**</mark> `https://mpc-client.portalhq.io/v1/backup`

This endpoint allows you to create backup shares for your wallet.

#### Headers

| Name                                            | Type   | Description                   |
| ----------------------------------------------- | ------ | ----------------------------- |
| Authorization<mark style="color:red;">\*</mark> | String | Bearer **`<Client API Key>`** |
| Content-Type<mark style="color:red;">\*</mark>  | String | application/json              |

#### Body Parameters

| Name                                                 | Type | Description                                                                |
| ---------------------------------------------------- | ---- | -------------------------------------------------------------------------- |
| `generateResponse`<mark style="color:red;">\*</mark> | JSON | The response body from ["Create Wallets"](v1-endpoints.md#create-wallets). |

#### Example Request

Be sure to replace the `[token]` with either a Client API Key or a Client Session Token.

```bash
curl --request POST \
  --url https://mpc-client.portalhq.io/v1/backup \
  --header 'Authorization: Bearer [token]' \
  --header 'Content-Type: application/json' \
  --data '{
  "generateResponse": "{\"SECP256K1\":{\"share\":\"share\",\"id\":\"shareId\"},\"ED25519\":{\"share\":\"share\",\"id\":\"shareId\"}}"
}'
```

#### Example Response

{% tabs %}
{% tab title="200: OK Success" %}
```json
{
  "SECP256K1": {
    "share": "eyJjbGl...",
    "id": "clu32l6c8008wuvz52jokyo3r"
  },
  "ED25519": {
    "share": "eyJjbGl...",
    "id": "clu32l9310087evz5u5v9k1ao"
  }
}
```
{% endtab %}

{% tab title="400: Bad Request" %}
```json
{
  "id": "BAD_REQUEST", // Short error identificator
  "message": "Can't parse the request body" // Optional details
}
```
{% endtab %}

{% tab title="401: Unauthorized Request" %}
```
"Authorization header is missing"
```
{% endtab %}
{% endtabs %}

### Recover the client's wallet

<mark style="color:purple;">**`POST`**</mark> `https://mpc-client.portalhq.io/v1/recover`

This endpoint allows you to recover shares for your wallet using your backup shares.

#### Headers

| Name                                            | Type   | Description                   |
| ----------------------------------------------- | ------ | ----------------------------- |
| Authorization<mark style="color:red;">\*</mark> | String | Bearer **`<Client API Key>`** |
| Content-Type<mark style="color:red;">\*</mark>  | String | application/json              |

#### Body Parameters

| Name                                               | Type | Description                                                                  |
| -------------------------------------------------- | ---- | ---------------------------------------------------------------------------- |
| `backupResponse`<mark style="color:red;">\*</mark> | JSON | The response body from ["Back Up Wallets"](v1-endpoints.md#back-up-wallets). |

#### Example Request

Be sure to replace the `[token]` with either a Client API Key or a Client Session Token.

```bash
curl --request POST \
  --url https://mpc-client.portalhq.io/v1/recover \
  --header 'Authorization: Bearer [token]' \
  --header 'Content-Type: application/json' \
  --data '{
  "backupResponse": "{\"SECP256K1\":{\"share\":\"share\",\"id\":\"shareId\"},\"ED25519\":{\"share\":\"share\",\"id\":\"shareId\"}}"
}'
```

#### Example Response

{% tabs %}
{% tab title="200: OK Success" %}
```json
{
  "SECP256K1": {
    "share": "eyJjbGl...",
    "id": "clu32l6c8008wuvz52jokyo3r"
  },
  "ED25519": {
    "share": "eyJjbGl...",
    "id": "clu32l9310087evz5u5v9k1ao"
  }
}
```
{% endtab %}

{% tab title="400: Bad Request" %}
```json
{
  "id": "BAD_REQUEST", // Short error identificator
  "message": "Can't parse the request body" // Optional details
}
```
{% endtab %}

{% tab title="401: Unauthorized Request" %}
```
"Authorization header is missing"
```
{% endtab %}
{% endtabs %}
