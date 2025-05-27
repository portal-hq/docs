---
description: Our Swaps API utilizes 0x to execute swaps on EVM chains.
---

# V3 endpoints

## Overview

The Swaps API can be accessed at `https://api.portalhq.io/`.

### Authentication

There are two forms of required authentication in order to use the Swaps API

* Client Authentication - a Client Session Token or Client API Key provided as a `Bearer` token in the `Authentication` header
* 0x Authentication - a 0x API Key provided as `apiKey` property of the JSON request body

### Supported Chains

* Mainnet (`eip155:1`)
* Optimism (`eip155:10`)
* Binance Smart Chain (`eip155:56`)
* Polygon (`eip155:137`)
* Fantom (`eip155:250`)
* Base (`eip155:8453`)
* Arbitrum (`eip155:42161`)
* Celo (`eip155:42220`)
* Avalanche (`eip155:43114`)
* Sepolia (`eip155:11155111`)

## Get a list of available liquidity sources

<mark style="color:green;">`POST`</mark> `/v3/swaps/sources`

Returns a list of the available liquidity sources for a particular chain.

**Headers**

| Name          | Value                                  |
| ------------- | -------------------------------------- |
| Content-Type  | `application/json`                     |
| Authorization | `Bearer <CLIENT_AUTHENTICATION_TOKEN>` |

**Body Parameters**

| Name      | Type   | Description                                                                                                                                             |
| --------- | ------ | ------------------------------------------------------------------------------------------------------------------------------------------------------- |
| `address` | string | The client's ETH wallet address                                                                                                                         |
| `apiKey`  | string | Your 0x API Key                                                                                                                                         |
| `chainId` | string | <p>The <a href="https://github.com/ChainAgnostic/CAIPs/blob/main/CAIPs/caip-2.md">CAIP-2 chainId</a></p><p>(e.g. "<code>eip155:1</code>" = Mainnet)</p> |

#### Example request

```bash
curl -X POST "https://api.portalhq.io/api/v3/swaps/sources" \
     -H "Content-Type: application/json" \
     -H "Authorization: Bearer <CLIENT_AUTHENTICATION_TOKEN>" \
     -d '{
           "address": "<CLIENT_ETH_WALLET_ADDRESS>",
           "apiKey": "<YOUR_0x_API_KEY>",
           "chainId": "eip155:1"
         }'
```

**Example response**

The response of this request is a JSON object containing key/value pairs.&#x20;

The `key` is the Token Symbol and the `value` is an acceptable value as the `token` property provided to the `/quote` route (either a Token Symbol or Token Address).

{% tabs %}
{% tab title="200" %}
```json
{
    "0x": "0x012345",
    "Aave_V2": "0x012345",
    "Balancer_V2": "0x012345",
    "BancorV3": "0x012345",
    ...
}
```
{% endtab %}
{% endtabs %}

## Get a quote for buying/selling tokens

<mark style="color:green;">`POST`</mark> `/v3/swaps/quote`

Get an easy-to-consume quote for buying or selling a token.

**Headers**

| Name          | Value                                  |
| ------------- | -------------------------------------- |
| Content-Type  | `application/json`                     |
| Authorization | `Bearer <CLIENT_AUTHENTICATION_TOKEN>` |

**Body Parameters**

| Name         | Type              | Description                                                                                                                                |
| ------------ | ----------------- | ------------------------------------------------------------------------------------------------------------------------------------------ |
| `address`    | string            | The client's ETH wallet address.                                                                                                           |
| `apiKey`     | string (optional) | Your 0x API key.                                                                                                                           |
| `chainId`    | string            | <p>The <a href="https://github.com/ChainAgnostic/CAIPs/blob/main/CAIPs/caip-2.md">CAIP-2 chainId</a></p><p>(e.g. "eip155:1" = Mainnet)</p> |
| `buyToken`   | string            | The token you'd like to buy.                                                                                                               |
| `buyAmount`  | number (optional) | The amount of `buyToken` you'd like to buy in WEI. Exactly one of either `buyAmount` or `sellAmount` must be provided.                     |
| `sellToken`  | string            | The token you'd like to sell.                                                                                                              |
| `sellAmount` | number (optional) | The amount of `sellToken` you'd like to sell in WEI. Exactly one of either `buyAmount` or `sellAmount` must be provided.                   |

#### Example request

```bash
curl -X POST "https://api.portalhq.io/api/v3/swaps/quote" \
     -H "Content-Type: application/json" \
     -H "Authorization: Bearer <CLIENT_AUTHENTICATION_TOKEN>" \
     -d '{
           "address": "<CLIENT_ETH_WALLET_ADDRESS>",
           "apiKey": "<YOUR_0x_API_KEY>",
           "chainId": "eip155:1",
           "buyToken": "0x1f9840a85d5aF5bf1D1762F925BDADdC4201F984",
           "sellToken": "ETH",
           "sellAmount": 1000000000000000000
         }'
```

**Example response**

{% tabs %}
{% tab title="200" %}
```json
{
  "allowanceTarget": "0xdef1c0ded9bec7f1a1670819833240f027b25eff",
  "cost": 10000000000,
  "transaction": {
    "data": "0xd9627aa...",
    "from": "0xc02aaa39b223fe8d0a0e5c4f27ead9083c756cc2",
    "gas": "111000",
    "to": "0xdef1c0ded9bec7f1a1670819833240f027b25eff",
    "value": "0",
  }
}
```
{% endtab %}
{% endtabs %}
