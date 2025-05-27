---
description: >-
  These endpoints require a Portal API Key (aka "Custodian API Key") as a bearer
  token.
---

# V3 endpoints

{% hint style="warning" %}
Before going live with real users, always ensure that you create clients using a [Portal API Key](../../resources/authentication-and-api-keys.md#portal-api-key) from your Portal's **Production** environment. Read more [here](../../resources/going-to-production.md) on going live with real users.
{% endhint %}

## Clients

### Create a new client

<mark style="color:purple;">**`POST`**</mark> `https://api.portalhq.io/api/v3/custodians/me/clients`

Registers a new client and returns a client API key, client session token, and whether they are AA enabled.

#### Headers

<table><thead><tr><th>Name</th><th width="193">Type</th><th>Description</th></tr></thead><tbody><tr><td>Authorization<mark style="color:red;">*</mark></td><td>String</td><td>Bearer <strong><code>&#x3C;Custodian API Key></code></strong></td></tr><tr><td>Content-Type<mark style="color:red;">*</mark></td><td>String</td><td>application/json</td></tr></tbody></table>

#### Body Parameters

| Name                  | Type               | Description                                            |
| --------------------- | ------------------ | ------------------------------------------------------ |
| `isAccountAbstracted` | Boolean (optional) | The client will use ERC-4337 Account Abstraction (AA). |

#### Example Request

Be sure to replace the `[token]` with a Custodian API Key.

```bash
curl --request POST \
  --url https://api.portalhq.io/api/v3/custodians/me/clients \
  --header 'Authorization: Bearer [token]' \
  --header 'Content-Type: application/json' \
  --data '{
	"isAccountAbstracted": false
}
'
```

#### Example Response

{% tabs %}
{% tab title="200: Created Success" %}
```json
{
  "id": "string",
  "clientApiKey": "string",
  "clientSessionToken": "string",
  "isAccountAbstracted": boolean
}
```
{% endtab %}

{% tab title="400: Bad Request" %}
```json
{}
```
{% endtab %}

{% tab title="401: Unauthorized Request" %}
```json
{}
```
{% endtab %}
{% endtabs %}

### Create a Client Session Token (CST) for a client

<mark style="color:purple;">**`POST`**</mark> `https://api.portalhq.io/api/v3/custodians/me/clients/[clientId]/sessions`

Creates a new Client Session Token for a client.

#### Headers

<table><thead><tr><th>Name</th><th width="193">Type</th><th>Description</th></tr></thead><tbody><tr><td>Authorization<mark style="color:red;">*</mark></td><td>String</td><td>Bearer <strong><code>&#x3C;Custodian API Key></code></strong></td></tr><tr><td>Content-Type<mark style="color:red;">*</mark></td><td>String</td><td>application/json</td></tr></tbody></table>

#### Path Parameters

| Name                                         | Type   | Description         |
| -------------------------------------------- | ------ | ------------------- |
| `clientId`<mark style="color:red;">\*</mark> | String | The ID of a client. |

#### Example Request

Be sure to replace the `[token]` with a Custodian API Key and the `[clientId]` with the relevant Portal client ID.

```bash
curl --request POST \
  --url https://api.portalhq.io/api/v3/custodians/me/clients/[clientId]/sessions \
  --header 'Authorization: Bearer [token]' \
  --header 'Content-Type: application/json' \
  --data '{
	"isAccountAbstracted": false
}
'
```

#### Example Response

{% tabs %}
{% tab title="200: OK Success" %}
```json
{
  "id": "string",
  "clientSessionToken": "string",
  "isAccountAbstracted": boolean
}
```
{% endtab %}

{% tab title="400: Bad Request" %}
```json
{
  "error": "string"
}
```
{% endtab %}

{% tab title="401: Unauthorized Request" %}
```json
{
  "error": "string"
}
```
{% endtab %}
{% endtabs %}

### Create a one-time password (OTP) for a client using the Web SDK

<mark style="color:purple;">**`POST`**</mark> `https://api.portalhq.io/api/v1/custodians/clients/[clientId]/web-otp`

Generate a one-time password for a client.

#### Headers

<table><thead><tr><th>Name</th><th width="193">Type</th><th>Description</th></tr></thead><tbody><tr><td>Authorization<mark style="color:red;">*</mark></td><td>String</td><td>Bearer <strong><code>&#x3C;Custodian API Key></code></strong></td></tr><tr><td>Content-Type<mark style="color:red;">*</mark></td><td>String</td><td>application/json</td></tr></tbody></table>

#### Path Parameters

| Name                                         | Type   | Description         |
| -------------------------------------------- | ------ | ------------------- |
| `clientId`<mark style="color:red;">\*</mark> | String | The ID of a client. |

#### Example Request

Be sure to replace the `[token]` with a Custodian API Key and the `[clientId]` with the relevant Portal client ID.

```bash
curl --request POST \
  --url https://api.portalhq.io/api/v1/custodians/clients/[clientId]/web-otp \
  --header 'Authorization: Bearer [token]' \
  --header 'Content-Type: application/json'
'
```

#### Example Response

{% tabs %}
{% tab title="200: OK Success" %}
```json
{
  "id": "string"
  "otp": "string"
}
```
{% endtab %}

{% tab title="400: Bad Request Bad Request" %}
```
{}
```
{% endtab %}

{% tab title="401: Unauthorized Unauthorized" %}
```
{}
```
{% endtab %}
{% endtabs %}

### Get a single client

<mark style="color:blue;">**`GET`**</mark> `https://api.portalhq.io/api/v3/custodians/me/clients/[clientId]`

Fetches the specified client for the authorized custodian.

#### Headers

<table><thead><tr><th>Name</th><th width="193">Type</th><th>Description</th></tr></thead><tbody><tr><td>Authorization<mark style="color:red;">*</mark></td><td>String</td><td>Bearer <strong><code>&#x3C;Custodian API Key></code></strong></td></tr><tr><td>Content-Type<mark style="color:red;">*</mark></td><td>String</td><td>application/json</td></tr></tbody></table>

#### Path Parameters

| Name                                         | Type   | Description           |
| -------------------------------------------- | ------ | --------------------- |
| `clientId`<mark style="color:red;">\*</mark> | String | The ID of the client. |

#### Example Request

Be sure to replace the `[token]` with a Custodian API Key and the `[clientId]` with the relevant Portal client ID.

```bash
curl --request GET \
  --url https://api.portalhq.io/api/v3/custodians/me/clients/[clientId] \
  --header 'Authorization: Bearer [token]'
'
```

#### Example Response

{% tabs %}
{% tab title="200: OK Success" %}
```json
{
  "createdAt": "2024-04-16T21:15:06.443Z",
  "custodian": {
    "id": "custodianId",
    "name": "Custodian Name"
  },
  "ejectedAt": null,
  "environment": {
    "id": "environmentId",
    "name": "Development"
  },
  "id": "clientId",
  "isAccountAbstracted": false,
  "metadata": {
    "namespaces": {
      "eip155": {
        "address": "0x6e818d8f9b6c53c59a2d957d36c2146e28906195",
        "curve": "SECP256K1"
      }
    }
  },
  "wallets": [
    {
      "createdAt": "2024-04-16T21:15:45.144Z",
      "curve": "SECP256K1",
      "id": "wallet1Id",
      "backupSharePairs": [
        {
          "backupMethod": "PASSWORD",
          "createdAt": "2024-04-16T21:16:48.723Z",
          "id": "backupSharePairId1",
          "status": "completed"
        },
        {
          "backupMethod": "GDRIVE",
          "createdAt": "2024-04-16T21:17:02.074Z",
          "id": "backupSharePairId2",
          "status": "incomplete"
        },
        {
          "backupMethod": "ICLOUD",
          "createdAt": "2024-04-16T21:18:06.996Z",
          "id": "backupSharePairId3",
          "status": "incomplete"
        }
      ],
      "signingSharePairs": [
        {
          "createdAt": "2024-04-16T21:15:45.151Z",
          "id": "signingSharePairId1",
          "status": "completed"
        }
      ],
      "publicKey": "stringifiedJSON"
    }
  ]
}
```
{% endtab %}

{% tab title="400: Bad Request" %}
```json
{}
```
{% endtab %}

{% tab title="401: Unauthorized Request" %}
```json
{}
```
{% endtab %}
{% endtabs %}

### Get a list of clients

<mark style="color:blue;">**`GET`**</mark> `https://api.portalhq.io/api/v3/custodians/me/clients`

Fetches all clients for the authorized custodian.

#### Headers

<table><thead><tr><th>Name</th><th width="193">Type</th><th>Description</th></tr></thead><tbody><tr><td>Authorization<mark style="color:red;">*</mark></td><td>String</td><td>Bearer <strong><code>&#x3C;Custodian API Key></code></strong></td></tr><tr><td>Content-Type<mark style="color:red;">*</mark></td><td>String</td><td>application/json</td></tr></tbody></table>

#### Query Parameters

| Name     | Type   | Description                                                              |
| -------- | ------ | ------------------------------------------------------------------------ |
| `cursor` | String | The ID of the last client in the list. Used for cursor-based pagination. |
| `take`   | Number | The number of records to retrieve. Maximum is 100. Default is 100.       |

#### Example Request

Be sure to replace the `[token]` with a Custodian API Key.

```bash
curl --request GET \
  --url https://api.portalhq.io/api/v3/custodians/me/clients \
  --header 'Authorization: Bearer [token]'
'
```

#### Example Response

{% tabs %}
{% tab title="200: OK Success" %}
```json
{
  "results": [
    {
      "createdAt": "2024-04-16T21:15:06.443Z",
      "custodian": {
        "id": "custodianId",
        "name": "Custodian Name"
      },
      "ejectedAt": null,
      "environment": {
        "id": "environmentId",
        "name": "Development"
      },
      "id": "clientId",
      "isAccountAbstracted": false,
      "metadata": {
        "namespaces": {
          "eip155": {
            "address": "0x6e818d8f9b6c53c59a2d957d36c2146e28906195",
            "curve": "SECP256K1"
          }
        }
      },
      "wallets": [
        {
          "createdAt": "2024-04-16T21:15:45.144Z",
          "curve": "SECP256K1",
          "id": "wallet1Id",
          "backupSharePairs": [
            {
              "backupMethod": "PASSWORD",
              "createdAt": "2024-04-16T21:16:48.723Z",
              "id": "backupSharePairId1",
              "status": "completed"
            },
            {
              "backupMethod": "GDRIVE",
              "createdAt": "2024-04-16T21:17:02.074Z",
              "id": "backupSharePairId2",
              "status": "incomplete"
            },
            {
              "backupMethod": "ICLOUD",
              "createdAt": "2024-04-16T21:18:06.996Z",
              "id": "backupSharePairId3",
              "status": "incomplete"
            }
          ],
          "signingSharePairs": [
            {
              "createdAt": "2024-04-16T21:15:45.151Z",
              "id": "signingSharePairId1",
              "status": "completed"
            }
          ],
          "publicKey": "stringifiedJSON"
        }
      ]
    },
    ...
  ],
  "metadata": {
    "cursor": "nextCursorToUse",
    "take": 100,
    "total": 1000
  }
}
```
{% endtab %}

{% tab title="400: Bad Request" %}
```json
{}
```
{% endtab %}

{% tab title="401: Unauthorized Request" %}
```json
{}
```
{% endtab %}
{% endtabs %}

### Get a list of assets for a client

<mark style="color:green;">**`GET`**</mark> `https://api.portalhq.io/api/v3/custodians/me/clients/:clientId/chains/:chain/assets`

This endpoint retrieves the asset balances (native and token balances) for a specified client and blockchain. It provides detailed information on the native balance and token balances held by a given address on the specified chain.

#### Supported Chains

You can use either the full chain identifier or the shortcut for popular chains.

* Solana Mainnet (`solana:5eykt4UsFv8P8NJdTREpY1vzqKqZKvdp` or `solana`)
* Solana Devnet (`solana:EtWTRABZaYq6iMfeYKouRu166VU2xqa1` or `solana-devnet`)
* Bitcoin (`bip122:000000000019d6689c085ae165831e93-p2wpkh` or `bitcoin-p2wpkh` or `bitcoin-segwit`)
* Bitcoin Testnet (`bip122:000000000933ea01ad0ee984209779ba-p2wpkh` or `bitcoin-p2wpkh-testnet` or `bitcoin-segwit-testnet`)
* Tron (`tron:mainnet`)
* Tron Nile (`tron:nile`)
* Tron Shasta (`tron:shasta`)
* Stellar (`stellar:pubnet`)
* Stellar Testnet (`stellar:testnet`)
* Ethereum Mainnet (`eip155:1` or `ethereum`)
* Ethereum Sepolia (`eip155:11155111` or `sepolia`)
* Base Mainnet (`eip155:8453` or `base`)
* Base Sepolia (`eip155:84532` or `base-sepolia`)
* Polygon Mainnet (`eip155:137` or `polygon`)
* Polygon Amoy (`eip155:80002` or `polygon-amoy`)
* Optimism Mainnet (`eip155:10`)
* Binance Smart Chain (`eip155:56`)
* Binance Smart Chain Testnet (`eip155:97`)
* Fantom (`eip155:250`)
* Moonbeam (`eip155:1284`)
* Arbitrum Mainnet (`eip155:42161`)
* Avalanche Mainnet (`eip155:43114`)
* Linea Mainnet (`eip155:59144`)
* Celo (`eip155:42220`)
* Celo Alfajores Testnet (`eip155:44787`)

{% hint style="info" %}
When using CAIP-2 chain formats in the URL (e.g. `eip155:11155111`), ensure the URL is URI encoded to accommodate the "**:**" character.
{% endhint %}

#### Headers

<table><thead><tr><th>Name</th><th width="193">Type</th><th>Description</th></tr></thead><tbody><tr><td>Authorization<mark style="color:red;">*</mark></td><td>String</td><td>Bearer <strong><code>&#x3C;Custodian API Key></code></strong></td></tr><tr><td>Content-Type<mark style="color:red;">*</mark></td><td>String</td><td>application/json</td></tr></tbody></table>

#### Path Parameters

| Name                                         | Type   | Description                                                                                                                                                      |
| -------------------------------------------- | ------ | ---------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| `clientId`<mark style="color:red;">\*</mark> | String | The ID of the client.                                                                                                                                            |
| `chainId`<mark style="color:red;">\*</mark>  | String | Must be in [CAIP-2 format](https://github.com/ChainAgnostic/CAIPs/blob/main/CAIPs/caip-2.md) or chain identifier shortcut (e.g. `eip155:11155111` or `sepolia`). |

#### Example Request

Be sure to replace the `[token]` with a Custodian API Key, the `chain` with a valid chain value, and the `clientId` with the relevant Portal client ID.

```bash
curl --request GET \
  --url https://api.portalhq.io/api/v3/custodians/me/clients/[clientId]/chains/[chain]/assets \
  --header 'Authorization: Bearer [token]'
'
```

#### Example Response

{% tabs %}
{% tab title="200: Success" %}
```json
{
  "nativeBalance": {
    "balance": "0.10991767582513721",
    "decimals": 18,
    "name": "Ether",
    "rawBalance": "109917675825137210",
    "symbol": "ETH",
    "metadata": {
      "logo": "https://cdn.moralis.io/eth/0x.png",
      "thumbnail": "https://cdn.moralis.io/eth/0x_thumb.png"
    }
  },
  "tokenBalances": [
    {
      "balance": "0.0199",
      "decimals": 18,
      "name": "Wrapped Ether",
      "rawBalance": "19900000000000000",
      "symbol": "WETH",
      "metadata": {
        "tokenAddress": "0xfff9976782d46cc05630d1f6ebab18b2324d6b14",
        "verifiedContract": false,
        "totalSupply": "48148.648311179713152621",
        "rawTotalSupply": "48148648311179713152621",
        "percentageRelativeToTotalSupply": 0.00004133033989114
      }
    }
  ],
  "nfts": [
    {
      "nftId": "ethereum.0x123abc...def456.1234",
      "name": "Cosmic Kitty #1234",
      "description": "A rare, interstellar feline NFT from the Cosmic Kitties collection.",
      "imageUrl": "https://example.com/images/cosmic-kitty-1234.png",
      "chainId": "eip155:1",
      "contractAddress": "0x123abc...def456",
      "tokenId": "1234",
      "collection": {
        "name": "Cosmic Kitties",
        "description": "A collection of 10,000 unique space-faring felines.",
        "imageUrl": "https://example.com/images/cosmic-kitties-logo.png"
      },
      "lastSale": {
        "price": 1500000000000000000,
        "currency": "ETH",
        "date": "2024-07-15T14:30:00Z"
      },
      "rarity": {
        "rank": 42,
        "score": 0.95
      },
      "floorPrice": {
        "price": 1200000000000000000,
        "currency": "ETH"
      },
      "detailedInfo": {
        "ownerCount": 1,
        "tokenCount": 1,
        "createdDate": "2024-01-01T00:00:00Z",
        "attributes": [
          {
            "traitType": "Fur Color",
            "value": "Nebula Purple",
            "displayType": null
          },
          {
            "traitType": "Eye Color",
            "value": "Starlight Blue",
            "displayType": null
          },
          {
            "traitType": "Accessory",
            "value": "Jetpack",
            "displayType": null
          }
        ],
        "owners": [
          {
            "ownerAddress": "0xabcdef...123456",
            "quantity": 1,
            "firstAcquiredDate": "2024-07-15T14:30:00Z",
            "lastAcquiredDate": "2024-07-15T14:30:00Z"
          }
        ],
        "extendedCollectionInfo": {
          "bannerImageUrl": "https://example.com/images/cosmic-kitties-banner.png",
          "externalUrl": "https://cosmickitties.io",
          "twitterUsername": "@CosmicKitties",
          "discordUrl": "https://discord.gg/cosmickitties",
          "instagramUsername": "@cosmic.kitties",
          "mediumUsername": "@CosmicKittiesNFT",
          "telegramUrl": "https://t.me/cosmickitties",
          "distinctOwnerCount": 8500,
          "distinctNftCount": 10000,
          "totalQuantity": 10000
        },
        "extendedSaleInfo": {
          "fromAddress": "0x98765...fedcba",
          "toAddress": "0xabcdef...123456",
          "priceUsdCents": 270000,
          "transaction": "0xfedcba...987654",
          "marketplaceId": "opensea",
          "marketplaceName": "OpenSea"
        },
        "marketplaceInfo": [
          {
            "marketplaceId": "opensea",
            "marketplaceName": "OpenSea",
            "marketplaceCollectionId": "cosmic-kitties",
            "nftUrl": "https://opensea.io/assets/ethereum/0x123abc...def456/1234",
            "collectionUrl": "https://opensea.io/collection/cosmic-kitties",
            "verified": true,
            "floorPrice": {
              "value": 1200000000000000000,
              "paymentToken": {
                "paymentTokenId": "ethereum",
                "name": "Ethereum",
                "symbol": "ETH",
                "address": null,
                "decimals": 18
              },
              "valueUsdCents": 270000
            }
          }
        ],
        "mediaInfo": {
          "previews": {
            "imageSmallUrl": "https://example.com/images/cosmic-kitty-1234-small.png",
            "imageMediumUrl": "https://example.com/images/cosmic-kitty-1234-medium.png",
            "imageLargeUrl": "https://example.com/images/cosmic-kitty-1234-large.png",
            "imageOpengraphUrl": "https://example.com/images/cosmic-kitty-1234-og.png",
            "blurhash": "L9B4IwofV@of~qofM{of00WB%Mj[",
            "predominantColor": "#8A2BE2"
          },
          "animationUrl": "https://example.com/animations/cosmic-kitty-1234.mp4",
          "backgroundColor": "#000000"
        }
      }
    }
  ]
}
```
{% endtab %}

{% tab title="400: Bad Request" %}
```json
{}
```
{% endtab %}

{% tab title="401: Unauthorized Request" %}
```json
{}
```
{% endtab %}
{% endtabs %}

### Build transaction for a client

<mark style="color:purple;">**`POST`**</mark> `https://api.portalhq.io/api/v3/custodians/me/clients/:clientId/chains/:chain/assets/send/build-transaction`

Creates an unsigned transaction for transferring assets to another address on a specific chain. You can then use this unsigned transaction to sign and submit the transaction.

#### Supported Chains

* You can use any of the following friendly chain names for the `chain` path param (or alternatively you can use the CAIP-2 `chainId`):
  * `ethereum` (`eip155:1`)
  * `sepolia` (`eip155:11155111`)
  * `base` (`eip155:8453`)
  * `base-sepolia` (`eip155:84531`)
  * `polygon` (`eip155:137`)
  * `polygon-mumbai` (`eip155:80001`)
  * `celo` (`eip155:42220`)
  * `celo-alfajores` (`eip155:44787`)
  * `solana` (`solana:5eykt4UsFv8P8NJdTREpY1vzqKqZKvdp`)
  * `solana-devnet` (`solana:EtWTRABZaYq6iMfeYKouRu166VU2xqa1`)
  * `tron` (`tron:mainnet`)
  * `tron-nile` (`tron:nile`)
  * `tron-shasta` (`tron:shasta`)
  * `stellar` (`stellar:pubnet`)
  * `stellar-testnet` (`stellar:testnet`)
  * `bitcoin-segwit` (`bip122:000000000019d6689c085ae165831e93-p2wpkh`)
  * `bitcoin-segwit-testnet` (`bip122:000000000933ea01ad0ee984209779ba-p2wpkh`)
  * `bitcoin-p2wpkh` (`bip122:000000000019d6689c085ae165831e93-p2wpkh`)
  * `bitcoin-p2wpkh-testnet` (`bip122:000000000933ea01ad0ee984209779ba-p2wpkh`)

{% hint style="info" %}
When using CAIP-2 chain formats in the URL (e.g. `eip155:11155111`), ensure the URL is URI encoded to accommodate the "**:**" character.
{% endhint %}

#### Headers

<table><thead><tr><th>Name</th><th width="193">Type</th><th>Description</th></tr></thead><tbody><tr><td>Authorization<mark style="color:red;">*</mark></td><td>String</td><td>Bearer <strong><code>&#x3C;Custodian API Key></code></strong></td></tr><tr><td>Content-Type<mark style="color:red;">*</mark></td><td>String</td><td>application/json</td></tr></tbody></table>

#### Path Parameters

| Name                                         | Type   | Description                                      |
| -------------------------------------------- | ------ | ------------------------------------------------ |
| `clientId`<mark style="color:red;">\*</mark> | String | The ID of the client.                            |
| `chain`<mark style="color:red;">\*</mark>    | String | Must be one of the chains in "Supported Chains". |

#### Body Parameters

| Name                                       | Type   | Description                                                                                                       |
| ------------------------------------------ | ------ | ----------------------------------------------------------------------------------------------------------------- |
| `to`<mark style="color:red;">\*</mark>     | String | The recipient's address.                                                                                          |
| `token`<mark style="color:red;">\*</mark>  | String | The token symbol (e.g., "USDC") or contract address or mint address (for Solana).                                 |
| `amount`<mark style="color:red;">\*</mark> | String | The amount to transfer (as a string, in the token's primary denomination). For example, "0.01" is 1 cent of USDC. |

#### Example Request

Be sure to replace the `[token]` with a Custodian API Key, the `chain` with a valid chain value, and the `clientId` with the relevant Portal client ID.

```bash
curl --request POST \
  --url https://api.portalhq.io/api/v3/custodians/me/clients/[clientId]/chains/[chain]/assets/send/build-transaction \
  --header 'Authorization: Bearer [token]' \
  --header 'Content-Type: application/json' \
  --data '{
	"to": "0xdFd8302f44727A6348F702fF7B594f127dE3A902",
	"token": "USDC",
	"amount": "0.01"
}
'
```

#### Example Response

The response includes the unsigned transaction details and metadata. The structure of the response differs depending on whether the chain is Solana or an EIP-155 chain (e.g., Ethereum, Polygon, Base).

{% tabs %}
{% tab title="200: EIP-155 Response" %}
```json
{
  "transaction": {
    "from": "0x21d8d1d2d9907c051670d8c9a80bd2192d273f8d",
    "to": "0xa0b86991c6218b36c1d19d4a2e9eb0ce3606eb48",
    "data": "0xa9059cbb000000000000000000000000dfd8302f44727a6348f702ff7b594f127de3a9020000000000000000000000000000000000000000000000000000000000002710"
  },
  "metadata": {
    "rawAmount": "10000",
    "formattedAmount": "0.01",
    "tokenDecimals": 6,
    "tokenAddress": "0xa0b86991c6218b36c1d19d4a2e9eb0ce3606eb48"
  }
}
```
{% endtab %}

{% tab title="200: Solana Response" %}
```json
{
  "transaction": "AQAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAABAAUIdDKlh5tOZP+4hxw5QGUB5WiLXZUpT35gX+MyY76LpXEnDFkvgeRwDb4A/njVdx9pXqNvR7FsgmX68XfuHQLkYQjDL4wY8hJ13iBEAciC0uIGJEPr+VGVf2qQqxBytKhwAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAB1aziYgQ4rYMUc0A5K2y4oefHN83MNdiTvNPqWosjPIyXJY9OJInxuz0QKRSODYMLWhOZ2v8QhASOe9jb6fhZxvp6877brTo9ZfNqq8l0MbG75MLS9uDkfKYCA0UvXWEG3fbh12Whk9nL4UbO63msHLSF7V9bN5E6jPWFfv8AqU7bjXonfSSr1ztp277I64YFbNX6zvfltX7/vmtSupKIAgUGAAIEBgMHAAcDAQIACQMQJwAAAAAAAA==",
  "metadata": {
    "amount": "0.01",
    "mintAddress": "EPjFWdd5AufqSSqeM2qN1xzybapC8G4wEGGkZwyTDt1v",
    "lastValidBlockHeight": "259030944",
    "unsignedTransaction": {
      "signatures": null,
      "message": {
        "accountKeys": [
          "8pb5w7NTiYnwdeGu4RBB6SXrCNTL1D8nkBjEFNST4KDz",
          "3dRpdTmiBkgLcWa7ogi8aQVAipcVrNCNh2HxPtFNZ4U8",
          "bCtKaS7SecyaiTDL388bnLyMmgFvGdxmLmeEhcoq5Eo",
          "11111111111111111111111111111111",
          "8APEEA4SHrfGteABQcUmH2yEHy7nEe8DqgvmKGAStHR",
          "ATokenGPvbdGVxr1b2hvZbsiqW5xWH25efTNsLJA8knL",
          "EPjFWdd5AufqSSqeM2qN1xzybapC8G4wEGGkZwyTDt1v",
          "TokenkegQfeZyiNwAJbNbGKPFXCWuBvf9Ss623VQ5DA"
        ],
        "header": {
          "numRequiredSignatures": 1,
          "numReadonlySignedAccounts": 0,
          "numReadonlyUnsignedAccounts": 5
        },
        "instructions": [
          {
            "programIdIndex": 5,
            "accounts": [
              0,
              2,
              4,
              6,
              3,
              7
            ],
            "data": ""
          },
          {
            "programIdIndex": 7,
            "accounts": [
              1,
              2,
              0
            ],
            "data": "3GAG5eogvTjV"
          }
        ],
        "recentBlockhash": "6JpyVC9BZDFVbgFqU8H2nkqvUY7vt6fmHYqSYPAbwbxT"
      }
    }
  }
}
```
{% endtab %}

{% tab title="200: Bitcoin Response" %}
```json
{
  "transaction": {
    "signatureHashes": [
      "signatureHash1",
      "signatureHash2"
    ],
    "rawTxHex": "rawTxHex",
    "publicKey": "jsonStringifiedPublicKey"
  },
  "metadata": {
    "chainId": "bip122:000000000933ea01ad0ee984209779ba-p2wpkh",
    "amount": "0.00001",
    "fromAddress": "tb1qpl54na90wlmdxj5z4wtz7cpp6p32dc9tajwa5g",
    "toAddress": "tb1q7ynrnrywae5ypxk6fqgzqhqy202ehk7hntqc9j",
    "rawAmount": "1000",
    "feeInSatoshis": "420",
    "changeInSatoshis": "4584",
    "tokenDecimals": 8,
    "tokenSymbol": "BTC"
  }
}
```
{% endtab %}

{% tab title="400: Bad Request" %}
```json
{
  "error": "Error message"
}
```
{% endtab %}
{% endtabs %}

#### Notes

* The `token` field can accept either a token symbol (e.g. "USDC" or "USDT") or a contract address (for EIP-155 tokens) or a mint address (for Solana tokens).
* The `amount` should be provided in the token's primary denomination (e.g. whole USDC, not micro-USDC).
* This endpoint creates an unsigned transaction. The client is responsible for signing the transaction before broadcasting it to the network.
* The response structure differs between Solana and EIP-155 chains. Make sure to handle both response types in your implementation.

## Client Shares

### Prepare a client for eject

<mark style="color:orange;">**`PATCH`**</mark> `https://api.portalhq.io/api/v3/custodians/me/clients/[clientId]/enable-eject`

**Your custodian must be using Portal-Managed Backups to use this endpoint.** Prepare a wallet for ejection of its private keys. This endpoint will enable a Portal client to be ejected for 5 minutes.

You can find the `walletId`  parameter from the [Get a Single Client](v3-endpoints.md#get-a-single-client) endpoint.

#### Headers

<table><thead><tr><th>Name</th><th width="193">Type</th><th>Description</th></tr></thead><tbody><tr><td>Authorization<mark style="color:red;">*</mark></td><td>String</td><td>Bearer <strong><code>&#x3C;Custodian API Key></code></strong></td></tr><tr><td>Content-Type<mark style="color:red;">*</mark></td><td>String</td><td>application/json</td></tr></tbody></table>

#### Body Parameters

| Name       | Type   | Description                           |
| ---------- | ------ | ------------------------------------- |
| `walletId` | String | The walletId of the wallet to eject.  |

#### Example Request

Be sure to replace the `[token]` with a Custodian API Key and the `[clientId]` with the relevant Portal client ID.

```bash
curl --request PATCH \
  --url https://api.portalhq.io/api/v3/custodians/me/clients/[clientId]/enable-eject \
  --header 'Authorization: Bearer [token]' \
  --header 'Content-Type: application/json' \
  --data '{
      "walletId": "walletId"
}
'
```

#### Example Response

{% tabs %}
{% tab title="200: Success" %}
```json
{
  "ejectableUntil": "2025-03-04T15:26:43.123Z"
}
```
{% endtab %}

{% tab title="400: Bad Request" %}
```json
{}
```
{% endtab %}

{% tab title="401: Unauthorized Request" %}
```json
{}
```
{% endtab %}
{% endtabs %}

### Get a client's backup shares

<mark style="color:green;">**`GET`**</mark> `https://api.portalhq.io/api/v3/custodians/me/clients/:clientId/wallets/:walletId/ejectable-backup-shares`

**Your custodian must be using Portal-Managed Backups to use this endpoint.** Fetches a client's encrypted backup share (if you sent it to Portal previously) and their custodian backup share. This endpoint is used when running eject while using the Enclave MPC API with a Portal-Managed Backup configuration.

#### Headers

| Name                                            | Type   | Description                   |
| ----------------------------------------------- | ------ | ----------------------------- |
| Authorization<mark style="color:red;">\*</mark> | String | Bearer **`<Client API Key>`** |
| Content-Type<mark style="color:red;">\*</mark>  | String | application/json              |

#### Example Request

Be sure to replace the `[token]` with a Custodian API Key, the `[walletId]` with the Portal client's wallet ID, and the `[clientId]` with the relevant Portal client ID.

```bash
curl --request GET \
  --url https://api.portalhq.io/api/v3/custodians/me/clients/[clientId]/wallets/[walletId]/ejectable-backup-shares \
  --header 'Authorization: Bearer [token]'
'
```

#### Example Response

{% tabs %}
{% tab title="200: Success" %}
```json
{
  "encryptedClientBackupShare": "0351cf2baa54748662a6c89bb032...", // Or null
  "custodianBackupShare": "{\"backupSharePairId\":\"backupSharePairId\",\"clientBk\":\"clientBk\",\"clientId\":\"clientId\",\"clientPartialPubKey_x\":\"clientPartialPubKey_x\",\"clientPartialPubKey_y\":\"clientPartialPubKey_y\",\"pailler_p\":\"pailler_p\",\"pailler_q\":\"pailler_q\",\"pedersenClient_n\":\"pedersenClient_n\",\"pedersenClient_s\":\"pedersenClient_s\",\"pedersenClient_t\":\"pedersenClient_t\",\"pedersenServer_n\":\"pedersenServer_n\",\"pedersenServer_s\":\"pedersenServer_s\",\"pedersenServer_t\":\"pedersenServer_t\",\"serverBk\":\"serverBk\",\"serverPartialPubKey_x\":\"serverPartialPubKey_x\",\"serverPartialPubKey_y\":\"serverPartialPubKey_y\",\"share\":\"share\",\"ssid\":\"ssid",\"x\":\"x\",\"y\":\"y\",\"yClient_x\":\"yClient_x\",\"yClient_y\":\"yClient_y\",\"yServer_x\":\"yServer_x\",\"yServer_y\":\"yServer_y\"}"
}
```
{% endtab %}

{% tab title="400: Bad Request" %}
```json
{}
```
{% endtab %}

{% tab title="401: Unauthorized" %}
```json
{}
```
{% endtab %}
{% endtabs %}

## Alert Webhooks

### Get a list of alert webhooks

<mark style="color:green;">**`GET`**</mark> `https://api.portalhq.io/api/v3/custodians/me/alerts/webhooks`

Retrieve a list of your alert webhooks configured in your Portal Admin Dashboard.

#### Headers

<table><thead><tr><th>Name</th><th width="193">Type</th><th>Description</th></tr></thead><tbody><tr><td>Authorization<mark style="color:red;">*</mark></td><td>String</td><td>Bearer <strong><code>&#x3C;Custodian API Key></code></strong></td></tr><tr><td>Content-Type<mark style="color:red;">*</mark></td><td>String</td><td>application/json</td></tr></tbody></table>

#### Example Request

Be sure to replace the `[token]` with a Custodian API Key.

```bash
curl --request GET \
  --url 'https://api.portalhq.io/api/v3/custodians/me/alerts/webhooks' \
  --header 'Authorization: Bearer [token]'
```

#### Example Response

{% tabs %}
{% tab title="200: Success" %}
```json
{
  "data": [
    {
      "autoAddNewAddresses": true,
      "createdAt": "2024-12-19T19:05:04.707Z",
      "events": ["EIP_155_TX_V1"],
      "id": "cm4voycw2001f68veade3tr0i",
      "updatedAt": "2024-12-19T20:07:16.648Z",
      "url": "https://example.com/alerts-webhooks/1/events"
    }
  ]
}
```
{% endtab %}

{% tab title="400: Bad Request" %}
```json
{}
```
{% endtab %}

{% tab title="401: Unauthorized Request" %}
```json
{}
```
{% endtab %}
{% endtabs %}

### Get a list of events sent to your alert webhook

<mark style="color:green;">**`GET`**</mark> `https://api.portalhq.io/api/v3/custodians/me/alerts/webhooks/:alertWebhookId/events`

Retrieve a list of the events sent to your alert webhook (including those that failed to be delivered).

#### Headers

<table><thead><tr><th>Name</th><th width="193">Type</th><th>Description</th></tr></thead><tbody><tr><td>Authorization<mark style="color:red;">*</mark></td><td>String</td><td>Bearer <strong><code>&#x3C;Custodian API Key></code></strong></td></tr><tr><td>Content-Type<mark style="color:red;">*</mark></td><td>String</td><td>application/json</td></tr></tbody></table>

#### Query Parameters

<table><thead><tr><th>Name</th><th width="226">Type</th><th>Description</th></tr></thead><tbody><tr><td><code>eventTypes</code><mark style="color:red;">*</mark></td><td><code>"EIP_155_TX_V1"</code></td><td>The alert webhook event types.</td></tr><tr><td><code>deliveryStatuses</code></td><td><code>"success"</code>, <code>"failed"</code>, <code>"pending"</code></td><td>Returns only alert webhook events with one of the specified delivery statuses.</td></tr><tr><td><code>since</code></td><td>Unix Number</td><td>Returns only alert webhook events that occurred after this time.</td></tr><tr><td><code>until</code></td><td>Unix Number</td><td>Returns only alert webhook events that occurred before this time.</td></tr><tr><td><code>cursor</code></td><td>String</td><td>The alert webhook event <code>"id"</code> for cursor-based pagination.</td></tr><tr><td><code>limit</code></td><td>Number (default: <code>100</code>)</td><td>The amount of alert webhook events to get back in the request.</td></tr><tr><td><code>transactionHash</code></td><td>String</td><td>Returns only alert webhook events that include this transaction hash.</td></tr><tr><td><code>blockNumbers</code></td><td>String (multiple delimited by <code>","</code>)</td><td>Returns only alert webhook events that include these block numbers.</td></tr><tr><td><code>chainIds</code></td><td><a href="../../resources/chain-id-formatting.md">CAIP-2 chains</a> (multiple delimited by <code>","</code>)</td><td>Returns only alert webhook events that include these chainIds.</td></tr></tbody></table>

#### Example Request

Be sure to replace the `[token]` with a Custodian API Key and the `[webhookId]` with the alert webhook ID you are interested in.

```bash
curl --request GET \
  --url 'https://api.portalhq.io/api/v3/custodians/me/alerts/webhooks/[webhookId]/events' \
  --header 'Authorization: Bearer [token]'
```

#### Example Response

{% tabs %}
{% tab title="200: Success" %}
```json
{
  "data": [
    // Successfully delivered alert webhook event retry.
    {
      "blockNumber": 7313326,
      "chainId": "eip155:11155111",
      "confirmed": 1,
      "createdAt": "2024-12-19T20:08:17.086Z",
      "deliveryStatus": "success",
      "errorMessage": null,
      "id": "85f3d2f7-b2ff-417d-b9cb-dc9823b4ae61",
      "retries": 0,
      "transactionHashes": [
        "0x4978a4ba15a018532791ba4491e48b1aa8b544309c1ffce062bb1fb7e84dc069"
      ],
      "type": "EIP_155_TX_V1",
      "updatedAt": "2024-12-19T20:08:17.086Z"
    },
    // Failed to deliver alert webhook event retry.
    {
      "blockNumber": 7313326,
      "chainId": "eip155:11155111",
      "confirmed": null,
      "createdAt": "2024-12-19T20:06:50.272Z",
      "deliveryStatus": "failed",
      "errorMessage": "Request failed with status code 400",
      "id": "f928772e-add0-4bf9-b0d3-c45f94513929",
      "retries": 1,
      "transactionHashes": [
        "0x4978a4ba15a018532791ba4491e48b1aa8b544309c1ffce062bb1fb7e84dc069"
      ],
      "type": "EIP_155_TX_V1",
      "updatedAt": "2024-12-19T20:06:50.272Z"
    },
    // Failed to deliver alert webhook event.
    {
      "blockNumber": 7313326,
      "chainId": "eip155:11155111",
      "confirmed": null,
      "createdAt": "2024-12-19T20:05:42.679Z",
      "deliveryStatus": "failed",
      "errorMessage": "Request failed with status code 400",
      "id": "eb7c6438-dc76-4d26-a704-1621f66b2972",
      "retries": 0,
      "transactionHashes": [
        "0x4978a4ba15a018532791ba4491e48b1aa8b544309c1ffce062bb1fb7e84dc069"
      ],
      "type": "EIP_155_TX_V1",
      "updatedAt": "2024-12-19T20:05:42.679Z"
    },
    // Successfully delivered alert webhook event.
    {
      "blockNumber": 7313246,
      "chainId": "eip155:11155111",
      "confirmed": 1,
      "createdAt": "2024-12-19T19:50:51.760Z",
      "deliveryStatus": "success",
      "errorMessage": null,
      "id": "f8600f2b-3970-4a1d-a4cb-00795c5879f6",
      "retries": 0,
      "transactionHashes": [
        "0xbed0c8f1c3eca9ff6b5c26c6b79f996e2be147dfe7e981bac4853eb13044112c"
      ],
      "type": "EIP_155_TX_V1",
      "updatedAt": "2024-12-19T19:50:51.760Z"
    },
  ],
  "metadata": {
    "alertWebhook": {
      "id": "alertWebhookId1",
      "url": "https://example.com/alerts/webhooks/1/events"
    },
    "cursor": null, // The alert webhook event "id".
    "limit": 100,
    "total": 4
  }
}
```
{% endtab %}

{% tab title="400: Bad Request" %}
```json
{}
```
{% endtab %}

{% tab title="401: Unauthorized Request" %}
```json
{}
```
{% endtab %}
{% endtabs %}

### Replay failed-to-deliver alert webhook events

<mark style="color:purple;">**`POST`**</mark> `https://api.portalhq.io/api/v3/custodians/me/alerts/webhooks/:alertWebhookId/events/failed/replay`

Replays a list of alert webhook events with a delivery status of `"failed"`.

{% hint style="warning" %}
If the `"failed"` alert webhook event has already been replayed _successfully_, you cannot replay the alert webhook event again with this endpoint.
{% endhint %}

#### Headers

<table><thead><tr><th>Name</th><th width="193">Type</th><th>Description</th></tr></thead><tbody><tr><td>Authorization<mark style="color:red;">*</mark></td><td>String</td><td>Bearer <strong><code>&#x3C;Custodian API Key></code></strong></td></tr><tr><td>Content-Type<mark style="color:red;">*</mark></td><td>String</td><td>application/json</td></tr></tbody></table>

#### Path Parameters

<table><thead><tr><th>Name</th><th width="193">Type</th><th>Description</th></tr></thead><tbody><tr><td><code>alertWebhookId</code><mark style="color:red;">*</mark></td><td>String</td><td>The <code>"id"</code> of the alert webhook. You can get this from the <a href="v3-endpoints.md#get-a-list-of-alert-webhooks">list alert webhooks endpoint</a>.</td></tr></tbody></table>

#### Body Parameters

<table><thead><tr><th>Name</th><th width="226">Type</th><th>Description</th></tr></thead><tbody><tr><td><code>eventIds</code><mark style="color:red;">*</mark></td><td>String[]</td><td>A list of <code>"failed"</code> alert webhook event <code>"id"</code>s to replay.</td></tr></tbody></table>

#### Example Request

Be sure to replace the `[token]` with a Custodian API Key and the `[webhookId]` with the alert webhook ID you are interested in.

```bash
curl --request POST \
  --url https://api.portalhq.io/api/v3/custodians/me/alerts/webhooks/[webhookId]/events/failed/replay \
  --header 'Authorization: Bearer [token]' \
  --header 'Content-Type: application/json' \
  --data '{
	"eventIds": ["eventId1"]
}'
```

#### Example Response

{% tabs %}
{% tab title="200: Success" %}
```json
{
  "data": {
    "replaying": ["alertWebhookEventId1"], // The alert webhook events are currently being replayed.
    "failed": ["alertWebhookEventId2"] // The alert webhook events failed to be replayed.
  }
}
```
{% endtab %}

{% tab title="400: Bad Request" %}
```json
{}
```
{% endtab %}

{% tab title="401: Unauthorized Request" %}
```json
{}
```
{% endtab %}
{% endtabs %}

## Deprecated Endpoints:

### Format a raw alert webhook event (@deprecated)

<mark style="color:purple;">**`POST`**</mark> `https://api.portalhq.io/api/v3/custodians/me/alerts/wallets/format`

This endpoint formats a raw webhook request body for wallet events.

#### Supported Chains

You can use either the full chain identifier or the shortcut for popular chains.

* Ethereum Mainnet (`eip155:1` or `ethereum`)
* Ethereum Sepolia (`eip155:11155111` or `sepolia`)
* Solana Mainnet (`solana:5eykt4UsFv8P8NJdTREpY1vzqKqZKvdp` or `solana`)
* Solana Devnet (`solana:EtWTRABZaYq6iMfeYKouRu166VU2xqa1` or `solana-devnet`)
* Base Mainnet (`eip155:8453` or `base`)
* Base Sepolia (`eip155:84531` or `base-sepolia`)
* Polygon Mainnet (`eip155:137` or `polygon`)
* Polygon Mumbai (`eip155:80001` or `polygon-mumbai`)

#### Headers

<table><thead><tr><th>Name</th><th width="193">Type</th><th>Description</th></tr></thead><tbody><tr><td>Authorization<mark style="color:red;">*</mark></td><td>String</td><td>Bearer <strong><code>&#x3C;Custodian API Key></code></strong></td></tr><tr><td>Content-Type<mark style="color:red;">*</mark></td><td>String</td><td>application/json</td></tr></tbody></table>

#### Body Parameters

| Name                                         | Type       | Description                              |
| -------------------------------------------- | ---------- | ---------------------------------------- |
| `chain`<mark style="color:red;">\*</mark>    | `"eip155"` | The chain the raw webhook event is from. |
| `rawAlert`<mark style="color:red;">\*</mark> | String     | The stringified raw webhook event.       |

#### Example Response

{% tabs %}
{% tab title="200: Success" %}
```json
{
  chainName: 'sepolia',
  chainId: 'eip155:11155111',
  from: '0xf428e1f82f18de15ef2861a4bdde42e755998ece',
  to: '0xdfd8302f44727a6348f702ff7b594f127de3a902',
  amount: '0.001',
  tokenSymbol: 'USDC',
  assetType: 'NON_NATIVE_TOKEN',
  direction: 'OUTBOUND',
  metadata: {
    confirmed: true,
    rawAmount: '1000',
    tokenDecimals: '6',
    tokenAddress: '0x1c7d4b196cb0c7b01d743fbc6116a902379c7238',
    tokenName: 'USDC',
    sentAt: '2024-09-06T20:30:24.000Z',
    transactionHash: '0x229c1cc8ed898fae917e0ed90af94c624015e53a790eda1a7d30b1ca2ffd916c',
    blockNumber: '6645168',
    fee: {
      amount: '0.000172838817388923',
      decimals: 18,
      rawAmount: '172838817388923',
      tokenSymbol: 'ETH'
    },
    nftTokenId: null,
    triggeredBy: '0xf428e1f82f18de15ef2861a4bdde42e755998ece',
    userOperationHash: '0x82b24bc1feb022bcf621d22efd473acf7bdb6fdd108c7bbaaf988a1717edfdb4'
  }
}
```
{% endtab %}

{% tab title="400: Bad Request" %}
```json
{}
```
{% endtab %}

{% tab title="401: Unauthorized Request" %}
```json
{}
```
{% endtab %}
{% endtabs %}

### Format raw alert webhook events (@deprecated)

<mark style="color:purple;">**`POST`**</mark> `https://api.portalhq.io/api/v3/custodians/me/alerts/format`

This endpoint formats a raw webhook request body for wallet events, which may include one or many transactions at once.

#### Supported Chains

You can use either the full chain identifier or the shortcut for popular chains.

* Ethereum Mainnet (`eip155:1` or `ethereum`)
* Ethereum Sepolia (`eip155:11155111` or `sepolia`)
* Solana Mainnet (`solana:5eykt4UsFv8P8NJdTREpY1vzqKqZKvdp` or `solana`)
* Solana Devnet (`solana:EtWTRABZaYq6iMfeYKouRu166VU2xqa1` or `solana-devnet`)
* Base Mainnet (`eip155:8453` or `base`)
* Base Sepolia (`eip155:84531` or `base-sepolia`)
* Polygon Mainnet (`eip155:137` or `polygon`)
* Polygon Mumbai (`eip155:80001` or `polygon-mumbai`)

#### Headers

<table><thead><tr><th>Name</th><th width="193">Type</th><th>Description</th></tr></thead><tbody><tr><td>Authorization<mark style="color:red;">*</mark></td><td>String</td><td>Bearer <strong><code>&#x3C;Custodian API Key></code></strong></td></tr><tr><td>Content-Type<mark style="color:red;">*</mark></td><td>String</td><td>application/json</td></tr></tbody></table>

#### Body Parameters

| Name                                         | Type       | Description                              |
| -------------------------------------------- | ---------- | ---------------------------------------- |
| `chain`<mark style="color:red;">\*</mark>    | `"eip155"` | The chain the raw webhook event is from. |
| `rawAlert`<mark style="color:red;">\*</mark> | String     | The stringified raw webhook event.       |

#### Example Response

{% tabs %}
{% tab title="200: Success" %}
```json
[
  {
    chainName: 'sepolia',
    chainId: 'eip155:11155111',
    from: '0xf428e1f82f18de15ef2861a4bdde42e755998ece',
    to: '0xdfd8302f44727a6348f702ff7b594f127de3a902',
    amount: '0.001',
    tokenSymbol: 'USDC',
    assetType: 'NON_NATIVE_TOKEN',
    direction: 'OUTBOUND',
    metadata: {
      confirmed: true,
      rawAmount: '1000',
      tokenDecimals: '6',
      tokenAddress: '0x1c7d4b196cb0c7b01d743fbc6116a902379c7238',
      tokenName: 'USDC',
      sentAt: '2024-09-06T20:30:24.000Z',
      transactionHash: '0x229c1cc8ed898fae917e0ed90af94c624015e53a790eda1a7d30b1ca2ffd916c',
      blockNumber: '6645168',
      fee: {
        amount: '0.000172838817388923',
        decimals: 18,
        rawAmount: '172838817388923',
        tokenSymbol: 'ETH'
      },
      nftTokenId: null,
      triggeredBy: '0xf428e1f82f18de15ef2861a4bdde42e755998ece',
      userOperationHash: '0x82b24bc1feb022bcf621d22efd473acf7bdb6fdd108c7bbaaf988a1717edfdb4'
    }
  }
]
```
{% endtab %}

{% tab title="400: Bad Request" %}
```json
{}
```
{% endtab %}

{% tab title="401: Unauthorized Request" %}
```json
{}
```
{% endtab %}
{% endtabs %}

### Replay an alert webhook event by block number (@deprecated)

<mark style="color:purple;">**`POST`**</mark> `https://api.portalhq.io/api/v3/custodians/me/alerts/retry-by-block-number`

This endpoint attempts to resend a wallet event scoped by a block number in the scenario you need to replay a transaction.

#### Supported Chains

EVM chains.

#### Headers

<table><thead><tr><th>Name</th><th width="193">Type</th><th>Description</th></tr></thead><tbody><tr><td>Authorization<mark style="color:red;">*</mark></td><td>String</td><td>Bearer <strong><code>&#x3C;Custodian API Key></code></strong></td></tr><tr><td>Content-Type<mark style="color:red;">*</mark></td><td>String</td><td>application/json</td></tr></tbody></table>

#### Body Parameters

| Name                                                 | Type   | Description                                                                               |
| ---------------------------------------------------- | ------ | ----------------------------------------------------------------------------------------- |
| `chainReferenceId`<mark style="color:red;">\*</mark> | String | The EVM chain reference ID. (e.g. `"11155111"` for Sepolia)                               |
| `blockNumber`<mark style="color:red;">\*</mark>      | Number | The block number of the transaction(s) you want to resend.                                |
| `streamId`<mark style="color:red;">\*</mark>         | String | The ID of the stream that Portal configured for you. (Reach out to our team to get this.) |

#### Example Response

{% tabs %}
{% tab title="204: Success No Content" %}
No content
{% endtab %}

{% tab title="400: Bad Request" %}
```json
{}
```
{% endtab %}

{% tab title="401: Unauthorized Request" %}
```json
{}
```
{% endtab %}
{% endtabs %}
