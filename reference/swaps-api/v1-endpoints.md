---
description: Our Swaps API utilizes 0x to execute swaps on EVM chains.
---

# V1 endpoints

## Overview

The Swaps API can be accessed at `https://api.portalhq.io/`.

### Authentication

There are two forms of required authentication in order to use the Swaps API

* Client Authentication - a Client Session Token or Client API Key provided as a `Bearer` token in the `Authentication` header
* 0x Authentication - a 0x API Key provided as `apiKey` property of the JSON request body

## Get a list of available tokens

<mark style="color:green;">`POST`</mark> `/v1/swaps/sources`

Retrieves a list of available tokens for a given EVM chain

**Headers**

| Name          | Value                                  |
| ------------- | -------------------------------------- |
| Content-Type  | `application/json`                     |
| Authorization | `Bearer <CLIENT_AUTHENTICATION_TOKEN>` |

**Body**

| Name      | Type   | Description                                                                |
| --------- | ------ | -------------------------------------------------------------------------- |
| `address` | string | The client's ETH wallet address                                            |
| `apiKey`  | string | Your 0x API Key                                                            |
| chainId   | string | The EVM chain ID to scope sources to ("1" = mainnet, "11155111" = sepolia) |

**Response**

The response of this request is a JSON object containing key/value pairs.&#x20;

The `key` is the Token Symbol and the `value` is an acceptable value as the `token` property provided to the `/quote` route (either a Token Symbol or Token Address).

{% tabs %}
{% tab title="200" %}
```json
{
  ETH: "ETH",
  UNI: "0x1f9840a85d5aF5bf1D1762F925BDADdC4201F984",
  ...
}
```
{% endtab %}
{% endtabs %}

#### Example request

```bash
curl -X POST "https://api.portalhq.io/api/v1/swaps/sources" \
     -H "Content-Type: application/json" \
     -H "Authorization: Bearer <CLIENT_AUTHENTICATION_TOKEN>" \
     -d '{
           "address": "<CLIENT_ETH_WALLET_ADDRESS>",
           "apiKey": "<YOUR_0x_API_KEY>",
           "chainId": "1"
         }'
```

## Get a quote for buying/selling tokens

<mark style="color:green;">`POST`</mark> `/v1/swaps/quote`

Retrieves a transaction and cost estimate for a given swap.

**Headers**

| Name          | Value                                  |
| ------------- | -------------------------------------- |
| Content-Type  | `application/json`                     |
| Authorization | `Bearer <CLIENT_AUTHENTICATION_TOKEN>` |

**Body**

| Name                  | Type   | Description                                                                                                              |
| --------------------- | ------ | ------------------------------------------------------------------------------------------------------------------------ |
| `address`             | string | The client's ETH wallet address                                                                                          |
| `apiKey`              | string | Your 0x API key                                                                                                          |
| chainId               | string | The EVM chain ID to scope sources to ("1" = mainnet, "11155111" = sepolia)                                               |
| buyToken              | string | A `value` from the JSON object returned by `/v1/swaps/sources` representing the token you'd like to buy                  |
| buyAmount (optional)  | number | The amount of `buyToken` you'd like to buy in WEI. Exactly one of either `buyAmount` or `sellAmount` must be provided.   |
| sellToken             | string | A value from the JSON object returned by `/v1/swaps/sources` representing the token you'd like to buy                    |
| sellAmount (optional) | number | The amount of `sellToken` you'd like to sell in WEI. Exactly one of either `buyAmount` or `sellAmount` must be provided. |

**Response**

```typescript
allowanceTarget: data.allowanceTarget,
          cost: (parseInt(data.gasPrice) * parseInt(data.gas)) / 1e18, // Convert gas fee to ETH
          transaction,
```

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

#### Example request

```bash
curl -X POST "https://api.portalhq.io/api/v1/swaps/quote" \
     -H "Content-Type: application/json" \
     -H "Authorization: Bearer <CLIENT_AUTHENTICATION_TOKEN>" \
     -d '{
           "address": "<CLIENT_ETH_WALLET_ADDRESS>",
           "apiKey": "<YOUR_0x_API_KEY>",
           "chainId": "1",
           "buyToken": "0x1f9840a85d5aF5bf1D1762F925BDADdC4201F984",
           "sellToken": "ETH",
           "sellAmount": 1000000000000000000
         }'
```
