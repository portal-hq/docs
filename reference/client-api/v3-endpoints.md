---
description: >-
  These endpoints require a Client API Key or Client Session Token (CST) as a
  bearer token.
---

# V3 endpoints

## Clients

### Get the client's details

<mark style="color:blue;">**`GET`**</mark> `https://api.portalhq.io/api/v3/clients/me`

This endpoint retrieves the details of the current client, including information about associated wallets.

#### Headers

| Name                                            | Type   | Description                   |
| ----------------------------------------------- | ------ | ----------------------------- |
| Authorization<mark style="color:red;">\*</mark> | String | Bearer **`<Client API Key>`** |
| Content-Type<mark style="color:red;">\*</mark>  | String | application/json              |

#### Example Request

Be sure to replace the `[token]` with either a Client API Key or a Client Session Token.

```bash
curl --request GET \
  --url https://api.portalhq.io/api/v3/clients/me \
  --header 'Authorization: Bearer [token]'
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
      "bip122": {
        "address": "",
        "curve": "SECP256K1",
        "bitcoin": {
          "p2wpkh": {
            "mainnet": "bc1qpl54na90wlmdxj5z4wtz7cpp6p32dc9th54w0m",
            "testnet": "tb1qpl54na90wlmdxj5z4wtz7cpp6p32dc9tajwa5g"
          }
        }
      },
      "solana": {
        "address": "BrGo1hFAL8MpudNQ6K4YWDaFB838uboUcCpm8C9uyC4R",
        "curve": "ED25519"
      },
      "stellar": {
        "address": "GCQTIL5333ASTDBXAJYDIAFRWXYGMR2KPGUNIDNSHOMBINTAMPZJFOKT",
        "curve": "ED25519"
      },
      "eip155": {
        "address": "0x0f3f5cea9784e254972d915695dbc9bc2a395faf",
        "curve": "SECP256K1"
      },
      "tron": {
        "address": "TBMq3UK8JNvauSc7sPUp7fkoSzTN4PZhP2",
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

### Get the client's assets by chain

<mark style="color:green;">**`GET`**</mark> `https://api.portalhq.io/api/v3/clients/me/chains/:chain/assets`

This endpoint retrieves the asset balances (native and token balances) for a specified blockchain. It provides detailed information on the native balance and token balances held by a given address on the specified chain.

#### Supported Chains

You can use either the full chain identifier or the shortcut for popular chains.

* Solana Mainnet (`solana:5eykt4UsFv8P8NJdTREpY1vzqKqZKvdp` or `solana`)
* Solana Devnet (`solana:EtWTRABZaYq6iMfeYKouRu166VU2xqa1` or `solana-devnet`)
* Bitcoin (`bip122:000000000019d6689c085ae165831e93-p2wpkh` or `bitcoin-p2wpkh` or `bitcoin-segwit`)
* Bitcoin Testnet (`bip122:000000000933ea01ad0ee984209779ba-p2wpkh` or `bitcoin-p2wpkh-testnet` or `bitcoin-segwit-testnet`)
* Celo (`eip155:42220` or `celo`)
* Celo Alfajores Testnet (`eip155:44787` or `celo-alfajores`)
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
* Arbitrum Sepolia (`eip155:421614`)
* Avalanche Mainnet (`eip155:43114`)
* Linea Mainnet (`eip155:59144`)

{% hint style="info" %}
When using CAIP-2 chain formats in the URL (e.g. `eip155:11155111`), ensure the URL is URI encoded to accommodate the "**:**" character.
{% endhint %}

#### Headers

| Name                                            | Type   | Description                   |
| ----------------------------------------------- | ------ | ----------------------------- |
| Authorization<mark style="color:red;">\*</mark> | String | Bearer **`<Client API Key>`** |
| Content-Type<mark style="color:red;">\*</mark>  | String | application/json              |

#### Query Parameters

| Name                                        | Type                          | Description                                                                                                                                                      |
| ------------------------------------------- | ----------------------------- | ---------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| `chainId`<mark style="color:red;">\*</mark> | String                        | Must be in [CAIP-2 format](https://github.com/ChainAgnostic/CAIPs/blob/main/CAIPs/caip-2.md) or chain identifier shortcut (e.g. `eip155:11155111` or `sepolia`). |
| `includeNfts`                               | Boolean - Defaults to `false` | Optionally includes the NFTs of the client on the specified chain.                                                                                               |

#### Example Request

Be sure to replace the `[token]` with either a Client API Key or a Client Session Token and the `chain` with a valid chain value.

```bash
curl --request GET \
  --url 'https://api.portalhq.io/api/v3/clients/me/chains/[chain]/assets' \
  --header 'Authorization: Bearer [token]'
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

### Get the client's assets by chain (NFTs only)

<mark style="color:green;">**`GET`**</mark> `https://api.portalhq.io/api/v3/clients/me/chains/:chain/assets/nfts`

This endpoint retrieves the NFT assets for a specified blockchain.

#### Supported Chains

You can use either the full chain identifier or the shortcut for popular chains.

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

{% hint style="info" %}
When using CAIP-2 chain formats in the URL (e.g. `eip155:11155111`), ensure the URL is URI encoded to accommodate the "**:**" character.
{% endhint %}

#### Headers

| Name                                            | Type   | Description                   |
| ----------------------------------------------- | ------ | ----------------------------- |
| Authorization<mark style="color:red;">\*</mark> | String | Bearer **`<Client API Key>`** |
| Content-Type<mark style="color:red;">\*</mark>  | String | application/json              |

#### Query Parameters

| Name                                        | Type   | Description                                                                                                                                                      |
| ------------------------------------------- | ------ | ---------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| `chainId`<mark style="color:red;">\*</mark> | String | Must be in [CAIP-2 format](https://github.com/ChainAgnostic/CAIPs/blob/main/CAIPs/caip-2.md) or chain identifier shortcut (e.g. `eip155:11155111` or `sepolia`). |

#### Example Request

Be sure to replace the `[token]` with either a Client API Key or a Client Session Token and the `chain` with a valid chain value.

```bash
curl --request GET \
  --url https://api.portalhq.io/api/v3/clients/me/chains/[chain]/assets/nfts \
  --header 'Authorization: Bearer [token]'
```

#### Example Response

{% tabs %}
{% tab title="200: Success" %}
```json
[
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

### Get the client's Solana transaction history

<mark style="color:green;">**`GET`**</mark> `https://api.portalhq.io/api/v3/clients/me/chains/:chain/transactions`

Retrieves the client's transaction history for a specific chain.

#### Supported Chains

* Solana Mainnet (`solana:5eykt4UsFv8P8NJdTREpY1vzqKqZKvdp` or `solana`)
* Solana Devnet (`solana:EtWTRABZaYq6iMfeYKouRu166VU2xqa1` or `solana-devnet`)

{% hint style="warning" %}
You can find the EVM Portal client transaction history endpoint [here](v3-endpoints.md#get-the-clients-eip-155-transaction-history).
{% endhint %}

{% hint style="info" %}
When using CAIP-2 chain formats in the URL (e.g. `eip155:11155111`), ensure the URL is URI encoded to accommodate the "**:**" character.
{% endhint %}

#### Headers

| Name                                            | Type   | Description                   |
| ----------------------------------------------- | ------ | ----------------------------- |
| Authorization<mark style="color:red;">\*</mark> | String | Bearer **`<Client API Key>`** |
| Content-Type<mark style="color:red;">\*</mark>  | String | application/json              |

#### Path Parameters

| Name                                      | Type   | Description                                                                                                                                                      |
| ----------------------------------------- | ------ | ---------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| `chain`<mark style="color:red;">\*</mark> | String | Must be in [CAIP-2 format](https://github.com/ChainAgnostic/CAIPs/blob/main/CAIPs/caip-2.md) or chain identifier shortcut (e.g. `eip155:11155111` or `sepolia`). |

#### Query Parameters

| Name     | Type   | Description                                                                                                                               |
| -------- | ------ | ----------------------------------------------------------------------------------------------------------------------------------------- |
| `limit`  | Number | The max amount of transactions returned in the request. Defaults to `1000`. Max is `1000`.                                                |
| `offset` | Number | The number of transactions to skip before returning results. Used for pagination when combined with the limit parameter. Defaults to `0`. |

#### Example Request

Be sure to replace the `[token]` with either a Client API Key or a Client Session Token and the `chain` with a valid chain value.

```bash
curl --request GET \
  --url https://api.portalhq.io/api/v3/clients/me/chains/[chain]/transactions \
  --header 'Authorization: Bearer [token]'
```

#### Example Response

{% tabs %}
{% tab title="200: Success" %}
```json
{
  "data": {
    "transactions": [
      {
        "blockTime": 1741126345,
        "error": null,
        "signature": "3J7cMNTCks3dNmVZsivgGcQzdTS7hr5Y8Na4Rb1yLdP9JPcPBHPjvVRQUtLzfdgUDiLZZhgBwPebXGdZz3ueuH4k",
        "status": "finalized",
        "tokenMint": null, // Used to identify SPL token (null for SOL txs)
        "transactionDetails": {
          "transaction": {
            "message": {
              "accountKeys": [
                "VgogDdrRWZxQxSMxWy3wEJPij5ZvRxV9C9dp4k1fGrx",
                "75ZfLXXsSpycDvHTQuHnGQuYgd2ihb6Bu4viiCCQ7P4H",
                "11111111111111111111111111111111"
              ],
              "header": {
                "numReadonlySignedAccounts": 0,
                "numReadonlyUnsignedAccounts": 1,
                "numRequiredSignatures": 1
              },
              "instructions": [
                {
                  "accounts": [
                    0,
                    1
                  ],
                  "data": "3Bxs4Bc3VYuGVB19",
                  "programIdIndex": 2,
                  "stackHeight": null
                }
              ],
              "recentBlockhash": "94x5LakBUZwcnpprYuftki9mq22dC353yebkoxLjhJqt"
            },
            "signatures": [
              "3J7cMNTCks3dNmVZsivgGcQzdTS7hr5Y8Na4Rb1yLdP9JPcPBHPjvVRQUtLzfdgUDiLZZhgBwPebXGdZz3ueuH4k"
            ]
          },
          "signatureDetails": {
            "blockTime": 1741126345,
            "confirmationStatus": "finalized",
            "error": null,
            "memo": null,
            "signature": "3J7cMNTCks3dNmVZsivgGcQzdTS7hr5Y8Na4Rb1yLdP9JPcPBHPjvVRQUtLzfdgUDiLZZhgBwPebXGdZz3ueuH4k",
            "slot": 365141926
          },
          "metadata": {
            "blockTime": 1741126345,
            "slot": 365141926,
            "error": null,
            "fee": 5000,
            "innerInstructions": [],
            "loadedAddresses": {
              "readonly": [],
              "writable": []
            },
            "logMessages": [
              "Program 11111111111111111111111111111111 invoke [1]",
              "Program 11111111111111111111111111111111 success"
            ],
            "postBalances": [
              7990000,
              9610486252,
              1
            ],
            "postTokenBalances": [],
            "preBalances": [
              8995000,
              9609486252,
              1
            ],
            "preTokenBalances": [],
            "rewards": [],
            "status": {
              "Ok": null
            },
            "version": "legacy"
          }
        }
      }
    ]
  },
  "metadata": {
    "address": "clientSolanaAddress",
    "chainId": "solana:EtWTRABZaYq6iMfeYKouRu166VU2xqa1",
    "clientId": "clientId",
    "limit": null, // Defaults to 1000
    "offset": 0
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

### Get the client's EIP-155 transaction history

<mark style="color:green;">**`GET`**</mark> `https://api.portalhq.io/api/v3/clients/me/transactions`

Retrieves the client's transaction history for a specific chain.

#### Supported Chains

* Ethereum Mainnet (`eip155:1`)
* Ethereum Goerli (`eip155:5`) // @deprecated
* Ethereum Sepolia (`eip155:11155111`)
* Base Mainnet (`eip155:8453`)
* Base Sepolia (`eip155:84532`)
* Polygon Mainnet (`eip155:137`)
* Polygon Mumbai (`eip155:80001`)
* Polygon Amoy (`eip155:80002`)
* Arbitrum (`eip155:42161`)
* Arbitrum Sepolia (`eip155:421614`)

{% hint style="info" %}
When using CAIP-2 chain formats in the URL (e.g. `eip155:11155111`), ensure the URL is URI encoded to accommodate the "**:**" character.
{% endhint %}

#### Headers

| Name                                            | Type   | Description                   |
| ----------------------------------------------- | ------ | ----------------------------- |
| Authorization<mark style="color:red;">\*</mark> | String | Bearer **`<Client API Key>`** |
| Content-Type<mark style="color:red;">\*</mark>  | String | application/json              |

#### Query Parameters

| Name                                        | Type   | Description                                                                                                              |
| ------------------------------------------- | ------ | ------------------------------------------------------------------------------------------------------------------------ |
| `chainId`<mark style="color:red;">\*</mark> | String | Must be in [CAIP-2 format](https://github.com/ChainAgnostic/CAIPs/blob/main/CAIPs/caip-2.md) (e.g. `"eip155:11155111"`). |

#### Example Request

Be sure to replace the `[token]` with either a Client API Key or a Client Session Token and the `chain` with a valid chain value.

```bash
curl --request GET \
  --url 'https://api.portalhq.io/api/v3/clients/me/transactions?chainId=[chain]' \
  --header 'Authorization: Bearer [token]'
```

#### Example Response

{% tabs %}
{% tab title="200: Success" %}
```json
[
  {
    "blockNum": "0x57450c",
    "uniqueId": "0x3b8a06c63d02ba6826cce6188c2caa44b68dd6a36050dcfd5c085a7cba5444e3:external",
    "hash": "0x3b8a06c63d02ba6826cce6188c2caa44b68dd6a36050dcfd5c085a7cba5444e3",
    "from": "0xdfd8302f44727a6348f702ff7b594f127de3a902",
    "to": "0x8eddb4067a73061e93583897d4684b749a93f19c",
    "value": 0.01,
    "erc721TokenId": null,
    "erc1155Metadata": null,
    "tokenId": null,
    "asset": "ETH",
    "category": "external",
    "rawContract": {
      "value": "0x2386f26fc10000",
      "address": null,
      "decimal": "0x12"
    },
    "metadata": {
      "blockTimestamp": "2024-04-17T17:32:24.000Z"
    },
    "chainId": 11155111
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

### Fund wallet with testnet tokens

<mark style="color:purple;">**`POST`**</mark> `https://api.portalhq.io/api/v3/clients/me/fund`

Sends testnet tokens to your Portal client's wallet.

#### Supported Chains

* Ethereum Sepolia (`eip155:11155111`)
  * Up to `0.01` `ETH` per Portal client per 24 hours.
* Celo Alfajores (`eip155:44787`)
  * Up to `0.1` `CELO` per Portal client per 24 hours.
* Solana Devnet (`solana:EtWTRABZaYq6iMfeYKouRu166VU2xqa1`)
  * Up to `0.01` `SOL` per Portal client per 24 hours.

#### Headers

| Name                                            | Type   | Description                   |
| ----------------------------------------------- | ------ | ----------------------------- |
| Authorization<mark style="color:red;">\*</mark> | String | Bearer **`<Client API Key>`** |
| Content-Type<mark style="color:red;">\*</mark>  | String | application/json              |

#### Body Parameters

| Name                                        | Type   | Description                                                                |
| ------------------------------------------- | ------ | -------------------------------------------------------------------------- |
| `chainId`<mark style="color:red;">\*</mark> | String | The CAIP-2 chainId you want to receive testnet tokens on.                  |
| `token`<mark style="color:red;">\*</mark>   | String | The testnet token you want to receive.                                     |
| `amount`<mark style="color:red;">\*</mark>  | String | The amount to transfer (as a string, in the token's primary denomination). |

#### Example Request

Be sure to replace the `[token]` with either a Client API Key or a Client Session Token and the `chain` with a valid chain value.

```bash
curl --request POST \
  --url https://api.portalhq.io/api/v3/clients/me/fund \
  --header 'Authorization: Bearer [token]' \
  --header 'Content-Type: application/json' \
  --data '{
	"chainId": "eip155:11155111",
	"token": "ETH",
	"amount": "0.01"
}
'
```

#### Example Response

{% tabs %}
{% tab title="200: EIP-155 Response" %}
```json
{
  "data": {
    "explorerUrl": "https://sepolia.etherscan.io/tx/0x93d1eb05b151ed66aabdd09e7151bbf19037e1b414565ce64968f58c138995dc",
    "txHash": "0x93d1eb05b151ed66aabdd09e7151bbf19037e1b414565ce64968f58c138995dc"
  },
  "metadata": {
    "amount": "0.001",
    "chainId": "eip155:11155111",
    "clientId": "clientId",
    "custodianId": "custodianId",
    "environmentId": "environmentId",
    "token": "NATIVE"
  }
}
```
{% endtab %}

{% tab title="429: Too Many Requests" %}
```json
{
  "error": "Testnet funding is disabled for this client until \"2025-01-25T19:22:51.208Z\""
}
```
{% endtab %}
{% endtabs %}

### Build a transaction

<mark style="color:purple;">**`POST`**</mark> `https://api.portalhq.io/api/v3/clients/me/chains/:chain/assets/send/build-transaction`

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

| Name                                            | Type   | Description                   |
| ----------------------------------------------- | ------ | ----------------------------- |
| Authorization<mark style="color:red;">\*</mark> | String | Bearer **`<Client API Key>`** |
| Content-Type<mark style="color:red;">\*</mark>  | String | application/json              |

#### Path Parameters

| Name                                      | Type   | Description                                      |
| ----------------------------------------- | ------ | ------------------------------------------------ |
| `chain`<mark style="color:red;">\*</mark> | String | Must be one of the chains in "Supported Chains". |

#### Body Parameters

| Name                                       | Type   | Description                                                                                                                   |
| ------------------------------------------ | ------ | ----------------------------------------------------------------------------------------------------------------------------- |
| `to`<mark style="color:red;">\*</mark>     | String | The recipient's address.                                                                                                      |
| `token`<mark style="color:red;">\*</mark>  | String | The token symbol (e.g., "USDC") or contract address or mint address (for Solana). For native token transfers, use `"NATIVE"`. |
| `amount`<mark style="color:red;">\*</mark> | String | The amount to transfer (as a string, in the token's primary denomination). For example, "0.01" is 1 cent of USDC.             |

#### Example Request

Be sure to replace the `[token]` with either a Client API Key or a Client Session Token and the `chain` with a valid chain value.

```bash
curl --request POST \
  --url https://api.portalhq.io/api/v3/clients/me/chains/[chain]/assets/send/build-transaction \
  --header 'Authorization: Bearer [token]' \
  --header 'Content-Type: application/json' \
  --data '{
	"to": "0xdFd8302f44727A6348F702fF7B594f127dE3A902",
	"token": "USDC",
	"amount": "0.01"
}'
```

#### Example Response

The response includes the unsigned transaction details and metadata. The structure of the response differs depending on whether the chain is Solana or an EIP-155 chain (e.g., Ethereum, Polygon, Base).

{% tabs %}
{% tab title="200: EIP-155 Response" %}
```json
{
  "transaction": {
    "from": "0x54968898742c08da211a1cd355447cd1f37f0649",
    "to": "0xa0b86991c6218b36c1d19d4a2e9eb0ce3606eb48",
    "data": "0xa9059cbb000000000000000000000000dfd8302f44727a6348f702ff7b594f127de3a9020000000000000000000000000000000000000000000000000000000000002710"
  },
  "metadata": {
    "amount": "0.01",
    "fromAddress": "0x54968898742c08da211a1cd355447cd1f37f0649",
    "toAddress": "0xdFd8302f44727A6348F702fF7B594f127dE3A902",
    "tokenAddress": "0xa0b86991c6218b36c1d19d4a2e9eb0ce3606eb48",
    "tokenDecimals": 6,
    "rawAmount": "10000"
  }
}
```
{% endtab %}

{% tab title="200: Solana Response" %}
```json
{
  "transaction": "AQAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAABAAUIXIcyVRnqtuJq4VAVZQPX4M5MFtpcAzLmaNCC62aV/5Ftzsmzlp2NKI8Dlj+MKJ7LBQsJ9iiXAult3S/Y/4YTRgjDL4wY8hJ13iBEAciC0uIGJEPr+VGVf2qQqxBytKhwAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAB1aziYgQ4rYMUc0A5K2y4oefHN83MNdiTvNPqWosjPIyXJY9OJInxuz0QKRSODYMLWhOZ2v8QhASOe9jb6fhZxvp6877brTo9ZfNqq8l0MbG75MLS9uDkfKYCA0UvXWEG3fbh12Whk9nL4UbO63msHLSF7V9bN5E6jPWFfv8AqSgizMJvj1c1WJPJuxDQWi6VG4dp6Fq930FKOQcI25XjAgUGAAIEBgMHAAcDAQIACQMQJwAAAAAAAA==",
  "metadata": {
    "amount": "0.01",
    "fromAddress": "7EC5TEEAcDseLH6PyqKkPiBasVnsdmrRbRHjN3zkkHFW",
    "toAddress": "8APEEA4SHrfGteABQcUmH2yEHy7nEe8DqgvmKGAStHR",
    "tokenMintAddress": "EPjFWdd5AufqSSqeM2qN1xzybapC8G4wEGGkZwyTDt1v",
    "tokenDecimals": 6,
    "tokenProgramId": "TokenzQdBNbLqP5VEhdkAS6EPFLC1PHnBqCXEpPxuEb",
    "tokenExtensions": [
      "ConfidentialTransferAccount",
      "ConfidentialTransferMint",
      "CpiGuard",
      "DefaultAccountState",
      "GroupMemberPointer",
      "GroupPointer",
      "ImmutableOwner",
      "InterestBearingConfig",
      "MemoTransfer",
      "MetadataPointer",
      "MintCloseAuthority",
      "NonTransferable",
      "NonTransferableAccount",
      "PermanentDelegate",
      "TokenGroup",
      "TokenGroupMember",
      "TokenMetadata",
      "TransferFeeAmount",
      "TransferFeeConfig",
      "TransferHook",
      "TransferHookAccount",
      "Uninitialized",
    ],
    "rawAmount": "10000",
    "lastValidBlockHeight": "260129177",
    "serializedTransactionBase64Encoded": "AQAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAABAAUIXIcyVRnqtuJq4VAVZQPX4M5MFtpcAzLmaNCC62aV/5Ftzsmzlp2NKI8Dlj+MKJ7LBQsJ9iiXAult3S/Y/4YTRgjDL4wY8hJ13iBEAciC0uIGJEPr+VGVf2qQqxBytKhwAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAB1aziYgQ4rYMUc0A5K2y4oefHN83MNdiTvNPqWosjPIyXJY9OJInxuz0QKRSODYMLWhOZ2v8QhASOe9jb6fhZxvp6877brTo9ZfNqq8l0MbG75MLS9uDkfKYCA0UvXWEG3fbh12Whk9nL4UbO63msHLSF7V9bN5E6jPWFfv8AqSgizMJvj1c1WJPJuxDQWi6VG4dp6Fq930FKOQcI25XjAgUGAAIEBgMHAAcDAQIACQMQJwAAAAAAAA==",
    "serializedTransactionBase58Encoded": "4c5ErVEQWyXMS7yGF2TJaJBf7Vk3bkdaXx6LrCS3xkeLUPWnJMde1CmjFFxEEwbwLjX6N2u2RNpNFhTPML7ow4ZDpUPB5NeocD5GDDPQb9Sy7BPTEMQH7QGXEDx8pfYmk7Z3yYTi7Wz6CsgoKgwhQTGRqCr6mhacTYVAwUpiptsASucz6hqkffZx7P96UPrU3vwL1QMYjLXKYYduuiqket5bt6uCRyF5cchdjir2LPTaqdLsKehDsFPh6ko45vJ4QuXZmeMyeF1fxECfjoj1N8wazVL57Ct9t5CKWXcWrXJkTzksGwMJ7CJQCUrMT7CGswzd9pwj55Lxcq55KcsGvTsu9NEo6dCs4ZFDXuXBexg7C3XNTf5GgsqBEi5MQVcZicUQb3EdS1j8ShTiNyiVkG2JEkvHQc4FZGQvSqrjgaUoi7FSfMRVeB6vWWwtnUfsGea1UcarfrFN8BcvuZrHNA5pyfC89iJpm55GRqgZFFupCCoWG3sJ47spU19oGEoAYD4pw6TJw",
    "unsignedTransactionMessage": {
      "signatures": null,
      "message": {
        "accountKeys": [
          "7EC5TEEAcDseLH6PyqKkPiBasVnsdmrRbRHjN3zkkHFW",
          "8PeKkecAjt1VgQNg1acdhgrajDJDB9uEDXP6MvRC8WAZ",
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
        "recentBlockhash": "3hg5wAbN1FYh5fGANDpCgWMDG1NPMYmNwdDiYTqinFUv"
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

### Simulate + validate a transaction

<mark style="color:purple;">**`POST`**</mark> `https://api.portalhq.io/api/v3/clients/me/evaluate-transaction`

This endpoint evaluates a transaction by either simulating its execution or validating its authenticity (or both). The evaluation provides detailed insights into the transaction, including potential changes in asset balances, exposures, and a classification of the transaction's risk.

#### Supported Chains

* Ethereum Mainnet (`eip155:1`)
* Ethereum Sepolia (`eip155:11155111`)
* Optimism Mainnet (`eip155:10`)
* Binance Smart Chain (`eip155:56`)
* Polygon Mainnet (`eip155:137`)
* Blast Mainnet (`eip155:238`)
* zkSync Mainnet (`eip155:324`)
* zkSync Sepolia (`eip155:300`)
* Base Mainnet (`eip155:8453`)
* Base Sepolia (`eip155:84532`)
* Immutable zkEVM (`eip155:13371`)
* Arbitrum Mainnet (`eip155:42161`)
* Avalanche Fuji (`eip155:43113`)
* Avalanche Mainnet (`eip155:43114`)
* Linea Mainnet (`eip155:59144`)
* Scroll Mainnet (`eip155:534352`)
* Zora Mainnet (`eip155:7777777`)
* Degen Mainnet (`eip155:666666666`)
* Solana Mainnet (`solana:5eykt4UsFv8P8NJdTREpY1vzqKqZKvdp`)
* Solana Devnet (`solana:EtWTRABZaYq6iMfeYKouRu166VU2xqa1`)

{% hint style="info" %}
When using CAIP-2 chain formats in the URL (e.g. `eip155:11155111`), ensure the URL is URI encoded to accommodate the "**:**" character.
{% endhint %}

#### Headers

| Name                                            | Type   | Description                   |
| ----------------------------------------------- | ------ | ----------------------------- |
| Authorization<mark style="color:red;">\*</mark> | String | Bearer **`<Client API Key>`** |
| Content-Type<mark style="color:red;">\*</mark>  | String | application/json              |

#### Query Parameters

| Name                                        | Type   | Description                                                                                                                                                       |
| ------------------------------------------- | ------ | ----------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| `chainId`<mark style="color:red;">\*</mark> | String | Must be in [CAIP-2 format](https://github.com/ChainAgnostic/CAIPs/blob/main/CAIPs/caip-2.md) (e.g. `eip155:11155111`, `solana:EtWTRABZaYq6iMfeYKouRu166VU2xqa1`). |

#### Body Parameters

{% tabs %}
{% tab title="EIP-155" %}
<table><thead><tr><th width="250">Name</th><th>Type</th><th>Description</th></tr></thead><tbody><tr><td><code>to</code><mark style="color:red;">*</mark></td><td>String</td><td>The recipient's address.</td></tr><tr><td><code>gas</code></td><td>String</td><td>The gas limit provided for the transaction.</td></tr><tr><td><code>gasPrice</code></td><td>String</td><td>The price per unit of gas the sender is willing to pay.</td></tr><tr><td><code>maxFeePerGas</code></td><td>String</td><td>The maximum total fee per gas unit the sender is willing to pay.</td></tr><tr><td><code>maxPriorityFeePerGas</code></td><td>String</td><td>The maximum priority fee per gas unit the sender is willing to pay.</td></tr><tr><td><code>value</code></td><td>String</td><td>The amount of native token to send with the transaction.</td></tr><tr><td><code>data</code></td><td>String</td><td>The input data to send with the transaction (used for contract calls).</td></tr><tr><td><code>operationType</code></td><td><p><code>"validation"</code>, <code>"simulation"</code>, or <code>"all"</code></p><p><br>Default: <code>"all"</code></p></td><td>The operation to perform with the transaction.</td></tr></tbody></table>
{% endtab %}

{% tab title="Solana" %}
| Name                                             | Type                                                                                                                     | Description                                         |
| ------------------------------------------------ | ------------------------------------------------------------------------------------------------------------------------ | --------------------------------------------------- |
| `transactions`<mark style="color:red;">\*</mark> | String\[]                                                                                                                | An array of base58 encoded serialized transactions. |
| `operationType`                                  | <p><code>"validation"</code>, <code>"simulation"</code>, or <code>"all"</code></p><p><br>Default: <code>"all"</code></p> | The operation to perform with the transaction.      |
{% endtab %}
{% endtabs %}

#### Example Request

Be sure to replace the `[token]` with either a Client API Key or a Client Session Token and the `chain` with a valid chain value.

```bash
curl --request POST \
  --url 'https://api.portalhq.io/api/v3/clients/me/evaluate-transaction?chainId=[chain]' \
  --header 'Authorization: Bearer [token]' \
  --header 'Content-Type: application/json' \
  --data '{
	"transactions": ["AQAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAABAAEDMVpKVHpo95fjtP7ysGKoVJSvwe6NDt2DwawHnAa5XccB1aziYgQ4rYMUc0A5K2y4oefHN83MNdiTvNPqWosjPAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAfW0SaDYLd80C2EifQJo2aExTPB0O4Kj02C/U1EleynMBAgIAAQwCAAAAQEIPAAAAAAA=", "AQAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAABAAEDMVpKVHpo95fjtP7ysGKoVJSvwe6NDt2DwawHnAa5XccB1aziYgQ4rYMUc0A5K2y4oefHN83MNdiTvNPqWosjPAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAfW0SaDYLd80C2EifQJo2aExTPB0O4Kj02C/U1EleynMBAgIAAQwCAAAAQEIPAAAAAAA="]
}'
```

#### Example Response

{% tabs %}
{% tab title="200: EIP-155 Success" %}
```json
{
  "chain": "ethereum",
  "validation": {
    "status": "Success",
    "resultType": "Malicious",
    "description": "A known malicious address is involved in the transaction",
    "reason": "raw_ether_transfer",
    "classification": "known_malicious",
    "features": []
  },
  "simulation": {
    "status": "Success",
    "assetsDiffs": {
      "0xfFf9976782d46CC05630D1f6eBAb18b2324d6B14": [
        {
          "asset": {
            "type": "NATIVE",
            "chainName": "sepolia",
            "decimals": 18,
            "chainId": 11155111,
            "logoUrl": "https://cdn.blockaid.io/chain/sepolia",
            "name": "Sepolia Ether",
            "symbol": "ETH"
          },
          "in": [
            {
              "summary": "Received 0 ETH",
              "value": "0.000000000000000001",
              "rawValue": "0x1"
            }
          ],
          "out": []
        },
        {
          "asset": {
            "type": "ERC20",
            "address": "0xfFf9976782d46CC05630D1f6eBAb18b2324d6B14",
            "logoUrl": "https://cdn.blockaid.io/token/0xfFf9976782d46CC05630D1f6eBAb18b2324d6B14/ethereum-sepolia",
            "decimals": 18,
            "name": "Wrapped Ether",
            "symbol": "WETH"
          },
          "in": [],
          "out": [
            {
              "summary": "Sent 0 WETH",
              "value": "0.000000000000000001",
              "rawValue": "0x1"
            }
          ]
        }
      ],
      "0x31A89E000E9029362213387FEDe26F7B543e9D1C": [
        {
          "asset": {
            "type": "NATIVE",
            "chainName": "sepolia",
            "decimals": 18,
            "chainId": 11155111,
            "logoUrl": "https://cdn.blockaid.io/chain/sepolia",
            "name": "Sepolia Ether",
            "symbol": "ETH"
          },
          "in": [],
          "out": [
            {
              "summary": "Sent 0 ETH",
              "value": "0.000000000000000001",
              "rawValue": "0x1"
            }
          ]
        },
        {
          "asset": {
            "type": "ERC20",
            "address": "0xfFf9976782d46CC05630D1f6eBAb18b2324d6B14",
            "logoUrl": "https://cdn.blockaid.io/token/0xfFf9976782d46CC05630D1f6eBAb18b2324d6B14/ethereum-sepolia",
            "decimals": 18,
            "name": "Wrapped Ether",
            "symbol": "WETH"
          },
          "in": [
            {
              "summary": "Received 0 WETH",
              "value": "0.000000000000000001",
              "rawValue": "0x1"
            }
          ],
          "out": []
        }
      ]
    },
    "totalUsdDiff": {},
    "exposures": {},
    "totalUsdExposure": {},
    "addressDetails": {},
    "accountSummary": {
      "assetsDiffs": [
        {
          "asset": {
            "type": "NATIVE",
            "chainName": "sepolia",
            "decimals": 18,
            "chainId": 11155111,
            "logoUrl": "https://cdn.blockaid.io/chain/sepolia",
            "name": "Sepolia Ether",
            "symbol": "ETH"
          },
          "in": [],
          "out": [
            {
              "summary": "Sent 0 ETH",
              "value": "0.000000000000000001",
              "rawValue": "0x1"
            }
          ]
        },
        {
          "asset": {
            "type": "ERC20",
            "address": "0xfFf9976782d46CC05630D1f6eBAb18b2324d6B14",
            "logoUrl": "https://cdn.blockaid.io/token/0xfFf9976782d46CC05630D1f6eBAb18b2324d6B14/ethereum-sepolia",
            "decimals": 18,
            "name": "Wrapped Ether",
            "symbol": "WETH"
          },
          "in": [
            {
              "summary": "Received 0 WETH",
              "value": "0.000000000000000001",
              "rawValue": "0x1"
            }
          ],
          "out": []
        }
      ],
      "exposures": [],
      "totalUsdExposure": {}
    }
  }
}
```
{% endtab %}

{% tab title="200: Solana Success" %}
```json
{
  "chain": "solana",
  "validation": {
    "status": "Success",
    "resultType": "Malicious",
    "reason": "known_attacker",
    "features": [
      {
        "description": "Account woN4W93ijGmYd9vdz1XujN4MkNvnhD1sXrvtRv3KHgD is known for being malicious"
      }
    ]
  },
  "simulation": {
    "accountAddress": "9G2CRh8pzicbRkiNLh8Xsp2DEP28UhwtHSyqetGYTCWD",
    "status": "Success",
    "accountSummary": {
      "assetsDiffs": [
        {
          "asset": {
            "type": "SOL",
            "decimals": 9
          },
          "in": [],
          "out": [
            {
              "usdPrice": "0.32128185400000003",
              "summary": "Lost approximately 0.32$",
              "value": "0.0021974",
              "rawValue": "0x218798"
            }
          ]
        },
        {
          "asset": {
            "address": "2b1kV6DkPAnxd5ixfnxCpjxmKwqjjaYmCZfHsFu24GXo",
            "symbol": "PYUSD",
            "name": "PayPal USD",
            "logo": "https://424565.fs1.hubspotusercontent-na1.net/hubfs/424565/PYUSDLOGO.png",
            "type": "TOKEN",
            "decimals": 6
          },
          "in": [],
          "out": [
            {
              "usdPrice": "0.0019997079999999998",
              "summary": "Lost approximately 0.0$",
              "value": "0.002",
              "rawValue": "0x7d0"
            }
          ]
        }
      ]
    },
    "addressDetails": {
      "9G2CRh8pzicbRkiNLh8Xsp2DEP28UhwtHSyqetGYTCWD": {
        "type": "SYSTEM_ACCOUNT",
        "accountAddress": "9G2CRh8pzicbRkiNLh8Xsp2DEP28UhwtHSyqetGYTCWD",
        "description": null,
        "wasWrittenTo": true
      },
      "woN4W93ijGmYd9vdz1XujN4MkNvnhD1sXrvtRv3KHgD": {
        "type": "SYSTEM_ACCOUNT",
        "accountAddress": "woN4W93ijGmYd9vdz1XujN4MkNvnhD1sXrvtRv3KHgD",
        "description": null,
        "wasWrittenTo": false
      },
      "2b1kV6DkPAnxd5ixfnxCpjxmKwqjjaYmCZfHsFu24GXo": {
        "type": "FUNGIBLE_MINT_ACCOUNT_2022",
        "accountAddress": "2b1kV6DkPAnxd5ixfnxCpjxmKwqjjaYmCZfHsFu24GXo",
        "description": "PayPal USD Mint Account",
        "wasWrittenTo": false,
        "name": "PayPal USD",
        "symbol": "PYUSD",
        "logo": "https://424565.fs1.hubspotusercontent-na1.net/hubfs/424565/PYUSDLOGO.png"
      },
      "HdhAViVJCG41UEpEMPt6WTyb9dDPJhZURPRacaSM8cJt": {
        "type": "TOKEN_2022_ACCOUNT",
        "accountAddress": "HdhAViVJCG41UEpEMPt6WTyb9dDPJhZURPRacaSM8cJt",
        "description": "PayPal USD's ($PYUSD) Token Account",
        "wasWrittenTo": true,
        "mintAddress": "2b1kV6DkPAnxd5ixfnxCpjxmKwqjjaYmCZfHsFu24GXo",
        "ownerAddress": "9G2CRh8pzicbRkiNLh8Xsp2DEP28UhwtHSyqetGYTCWD"
      },
      "GvLqZHDxshcYvW5HyPoaDFidNmRW6wft3NnKzhckgfWj": {
        "type": "TOKEN_2022_ACCOUNT",
        "accountAddress": "GvLqZHDxshcYvW5HyPoaDFidNmRW6wft3NnKzhckgfWj",
        "description": "PayPal USD's ($PYUSD) Token Account",
        "wasWrittenTo": true,
        "mintAddress": "2b1kV6DkPAnxd5ixfnxCpjxmKwqjjaYmCZfHsFu24GXo",
        "ownerAddress": "woN4W93ijGmYd9vdz1XujN4MkNvnhD1sXrvtRv3KHgD"
      },
      "TokenzQdBNbLqP5VEhdkAS6EPFLC1PHnBqCXEpPxuEb": {
        "type": "PROGRAM",
        "accountAddress": "TokenzQdBNbLqP5VEhdkAS6EPFLC1PHnBqCXEpPxuEb",
        "description": null,
        "wasWrittenTo": false
      },
      "ATokenGPvbdGVxr1b2hvZbsiqW5xWH25efTNsLJA8knL": {
        "type": "PROGRAM",
        "accountAddress": "ATokenGPvbdGVxr1b2hvZbsiqW5xWH25efTNsLJA8knL",
        "description": null,
        "wasWrittenTo": false
      },
      "11111111111111111111111111111111": {
        "type": "NATIVE_PROGRAM",
        "accountAddress": "11111111111111111111111111111111",
        "description": "System Program",
        "wasWrittenTo": false
      }
    },
    "assetsDiffs": {
      "woN4W93ijGmYd9vdz1XujN4MkNvnhD1sXrvtRv3KHgD": [
        {
          "asset": {
            "address": "2b1kV6DkPAnxd5ixfnxCpjxmKwqjjaYmCZfHsFu24GXo",
            "symbol": "PYUSD",
            "name": "PayPal USD",
            "logo": "https://424565.fs1.hubspotusercontent-na1.net/hubfs/424565/PYUSDLOGO.png",
            "type": "TOKEN",
            "decimals": 6
          },
          "in": [
            {
              "usdPrice": "0.0019997079999999998",
              "summary": "Gained approximately 0.0$",
              "value": "0.002",
              "rawValue": "0x7d0"
            }
          ],
          "out": []
        }
      ],
      "9G2CRh8pzicbRkiNLh8Xsp2DEP28UhwtHSyqetGYTCWD": [
        {
          "asset": {
            "type": "SOL",
            "decimals": 9
          },
          "in": [],
          "out": [
            {
              "usdPrice": "0.32128185400000003",
              "summary": "Lost approximately 0.32$",
              "value": "0.0021974",
              "rawValue": "0x218798"
            }
          ]
        },
        {
          "asset": {
            "address": "2b1kV6DkPAnxd5ixfnxCpjxmKwqjjaYmCZfHsFu24GXo",
            "symbol": "PYUSD",
            "name": "PayPal USD",
            "logo": "https://424565.fs1.hubspotusercontent-na1.net/hubfs/424565/PYUSDLOGO.png",
            "type": "TOKEN",
            "decimals": 6
          },
          "in": [],
          "out": [
            {
              "usdPrice": "0.0019997079999999998",
              "summary": "Lost approximately 0.0$",
              "value": "0.002",
              "rawValue": "0x7d0"
            }
          ]
        }
      ]
    },
    "solana": {
      "delegations": {},
      "assetsOwnershipDiff": {}
    }
  }
}
```
{% endtab %}

{% tab title="400: Bad Request" %}
```json
{
  "validation": {
    "status": "Error",
    "resultType": "Error",
    "description": "",
    "reason": "",
    "classification": "",
    "features": [],
    "error": "Error message here"
  },
  "simulation": {
    "status": "Error",
    "error": "Error message here"
  }
}
```
{% endtab %}

{% tab title="401: Unauthorized Request" %}
```json
{}
```
{% endtab %}
{% endtabs %}

## Client Shares

### Update the client's signing share pairs' statuses

<mark style="color:orange;">**`PATCH`**</mark> `https://api.portalhq.io/api/v3/clients/me/signing-share-pairs`

Updates a client's signing share pairs' statuses. Intended to be used after successfully storing the client signing share on the client's device.

{% hint style="warning" %}
Do not use this endpoint if you are using the Portal SDKs. The Portal SDKs already use this endpoint internally for your convenience.
{% endhint %}

#### Headers

| Name                                            | Type   | Description                   |
| ----------------------------------------------- | ------ | ----------------------------- |
| Authorization<mark style="color:red;">\*</mark> | String | Bearer **`<Client API Key>`** |
| Content-Type<mark style="color:red;">\*</mark>  | String | application/json              |

#### Body Parameters

| Name                                                    | Type              | Description                                    |
| ------------------------------------------------------- | ----------------- | ---------------------------------------------- |
| `signingSharePairIds`<mark style="color:red;">\*</mark> | String\[]         | An array of signing share pair IDs.            |
| `status`<mark style="color:red;">\*</mark>              | `"STORED_CLIENT"` | The updated status of the signing share pairs. |

#### Example Request

Be sure to replace the `[token]` with either a Client API Key or a Client Session Token.

```bash
curl --request PATCH \
  --url 'https://api.portalhq.io/api/v3/clients/me/signing-share-pairs' \
  --header 'Authorization: Bearer [token]' \
  --header 'Content-Type: application/json' \
  --data '{
	"signingSharePairIds": ["signingSharePairId1"],
	"status": "STORED_CLIENT"
}'
```

#### Example Response

{% tabs %}
{% tab title="204: Success, No Content" %}

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

### Update the client's backup share pairs' statuses

<mark style="color:orange;">**`PATCH`**</mark> `https://api.portalhq.io/api/v3/clients/me/backup-share-pairs`

Updates a client's backup share pairs' statuses. Intended to be used after successfully storing the encrypted client backup share.

{% hint style="warning" %}
Do not use this endpoint if you are using the Portal SDKs. The Portal SDKs already use this endpoint internally for your convenience.
{% endhint %}

#### Headers

| Name                                            | Type   | Description                   |
| ----------------------------------------------- | ------ | ----------------------------- |
| Authorization<mark style="color:red;">\*</mark> | String | Bearer **`<Client API Key>`** |
| Content-Type<mark style="color:red;">\*</mark>  | String | application/json              |

#### Body Parameters

| Name                                                   | Type                                                                 | Description                                   |
| ------------------------------------------------------ | -------------------------------------------------------------------- | --------------------------------------------- |
| `backupSharePairIds`<mark style="color:red;">\*</mark> | String\[]                                                            | An array of backup share pair IDs.            |
| `status`<mark style="color:red;">\*</mark>             | `"STORED_CLIENT_BACKUP_SHARE_KEY"` \| `"STORED_CLIENT_BACKUP_SHARE"` | The updated status of the backup share pairs. |

#### Example Request

Be sure to replace the `[token]` with either a Client API Key or a Client Session Token.

```bash
curl --request PATCH \
  --url https://api.portalhq.io/api/v3/clients/me/backup-share-pairs \
  --header 'Authorization: Bearer [token]' \
  --header 'Content-Type: application/json' \
  --data '{
	"status": "STORED_CLIENT_BACKUP_SHARE",
	"backupSharePairIds": ["backupSharePairId1"]
}
'
```

#### Example Response

{% tabs %}
{% tab title="204: Success, No Content" %}

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

### Store the client's encrypted backup share for Portal-Managed Backups

<mark style="color:orange;">**`PATCH`**</mark> `https://api.portalhq.io/api/v3/clients/me/backup-share-pairs/:backupSharePairId`

Stores a client's encrypted backup share for Portal-Managed Backups. This endpoint is used when running eject while using the Enclave MPC API with a Portal-Managed Backup configuration. Call this endpoint once for the SECP256K1 share and once for the ED25519 share.

#### Headers

| Name                                            | Type   | Description                   |
| ----------------------------------------------- | ------ | ----------------------------- |
| Authorization<mark style="color:red;">\*</mark> | String | Bearer **`<Client API Key>`** |
| Content-Type<mark style="color:red;">\*</mark>  | String | application/json              |

#### Body Parameters

| Name                                                 | Type   | Description                                    |
| ---------------------------------------------------- | ------ | ---------------------------------------------- |
| `clientCipherText`<mark style="color:red;">\*</mark> | String | The ciphertext from encrypting a backup share. |

#### Example Request

Be sure to replace the `[token]` with either a Client API Key or a Client Session Token and the `backupSharePairId` with the relevant backup share pair ID.

```bash
curl --request PATCH \
  --url https://api.portalhq.io/api/v3/clients/me/backup-share-pairs/[backupSharePairId] \
  --header 'Authorization: Bearer [token]' \
  --header 'Content-Type: application/json' \
  --data '{
	"clientCipherText": "clientBackupShareCipherText"
}
'
```

#### Example Response

{% tabs %}
{% tab title="200: Success" %}

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

### Get the encrypted client backup share for Portal-Managed Backups

<mark style="color:green;">**`GET`**</mark> `https://api.portalhq.io/api/v3/clients/me/backup-share-pairs/:backupSharePairId/cipher-text`

**Your custodian must be using Portal-Managed Backups to use this endpoint.** Fetches a client's encrypted backup share for Portal-Managed Backups. This endpoint is used when running eject while using the Enclave MPC API with a Portal-Managed Backup configuration.

#### Headers

| Name                                            | Type   | Description                   |
| ----------------------------------------------- | ------ | ----------------------------- |
| Authorization<mark style="color:red;">\*</mark> | String | Bearer **`<Client API Key>`** |
| Content-Type<mark style="color:red;">\*</mark>  | String | application/json              |

#### Example Request

Be sure to replace the `[token]` with either a Client API Key or a Client Session Token and the `backupSharePairId` with the relevant backup share pair ID.

```bash
curl --request GET \
  --url https://api.portalhq.io/api/v3/clients/me/backup-share-pairs/[backupSharePairId]/cipher-text \
  --header 'Authorization: Bearer [token]'
'
```

#### Example Response

{% tabs %}
{% tab title="200: Success" %}
```json
{
  "cipherText": "0351cf2baa54748662a6c89bb032..." 
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

### Get the ejectable backup shares for Portal-Managed Backups

<mark style="color:green;">**`GET`**</mark> `https://api.portalhq.io/api/v3/clients/me/wallets/:walletId/ejectable-backup-shares`

**You must have made the wallet ejectable before using this endpoint. Your custodian must be using Portal-Managed Backups to use this endpoint.** Fetches a client's encrypted backup share and their custodian backup share. This endpoint is used when running eject while using the Enclave MPC API with a Portal-Managed Backup configuration.

#### Headers

| Name                                            | Type   | Description                   |
| ----------------------------------------------- | ------ | ----------------------------- |
| Authorization<mark style="color:red;">\*</mark> | String | Bearer **`<Client API Key>`** |
| Content-Type<mark style="color:red;">\*</mark>  | String | application/json              |

#### Example Request

Be sure to replace the `[token]` with either a Client API Key or a Client Session Token and the `walletId` with the relevant Portal client wallet ID.

```bash
curl --request GET \
  --url ttps://api.portalhq.io/api/v3/clients/me/wallets/[walletId]/ejectable-backup-shares \
  --header 'Authorization: Bearer [token]'
'
```

#### Example Response

{% tabs %}
{% tab title="200: Success" %}
```json
{
  "encryptedClientBackupShare": "0351cf2baa54748662a6c89bb032...",
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

## Deprecated Endpoints:

### Get the client's NFTs by chain (@deprecated)

<mark style="color:green;">**`GET`**</mark> `https://api.portalhq.io/api/v3/clients/me/nfts`

This endpoint retrieves the NFTs for the client for a particular chain.

#### Supported Chains

* Ethereum Mainnet (`eip155:1`)
* Ethereum Goerli (`eip155:5`) // @deprecated
* Ethereum Sepolia (`eip155:11155111`)
* Base Mainnet (`eip155:8453`)
* Base Sepolia (`eip155:84532`)
* Polygon Mainnet (`eip155:137`)
* Polygon Mumbai (`eip155:80001`)
* Polygon Amoy (`eip155:80002`)

{% hint style="info" %}
When using CAIP-2 chain formats in the URL (e.g. `eip155:11155111`), ensure the URL is URI encoded to accommodate the "**:**" character.
{% endhint %}

#### Headers

| Name                                            | Type   | Description                   |
| ----------------------------------------------- | ------ | ----------------------------- |
| Authorization<mark style="color:red;">\*</mark> | String | Bearer **`<Client API Key>`** |
| Content-Type<mark style="color:red;">\*</mark>  | String | application/json              |

#### Query Parameters

| Name                                        | Type   | Description                                                                                                              |
| ------------------------------------------- | ------ | ------------------------------------------------------------------------------------------------------------------------ |
| `chainId`<mark style="color:red;">\*</mark> | String | Must be in [CAIP-2 format](https://github.com/ChainAgnostic/CAIPs/blob/main/CAIPs/caip-2.md) (e.g. `"eip155:11155111"`). |

#### Example Response

{% tabs %}
{% tab title="200: Success" %}
```json
[
  {
    "contract": {
      "address": "0xabcdef1234567890abcdef1234567890abcdef12"
    },
    "id": {
      "tokenId": "99",
      "tokenMetadata": {
        "tokenType": "ERC721"
      }
    },
    "title": "GalacticExplorer #99",
    "description": "Galactic Explorers venture into the unknown regions of space to discover new worlds and civilizations.",
    "tokenUri": {
      "raw": "https://galacticexplorers.io/api/explorers/99",
      "gateway": "https://galacticexplorers.io/api/explorers/99"
    },
    "media": [
      {
        "raw": "https://galacticexplorers.io/explorer_images/99.png",
        "gateway": "https://galacticexplorers.io/explorer_images/99.png"
      }
    ],
    "metadata": {
      "name": "GalacticExplorer #99",
      "description": "Galactic Explorers venture into the unknown regions of space to discover new worlds and civilizations.",
      "image": "https://galacticexplorers.io/explorer_images/99.png",
      "external_url": "https://galacticexplorers.io",
      "attributes": [
        {
          "value": "Space Suit (Blue)",
          "trait_type": "Clothes"
        },
        {
          "value": "Happy Smile",
          "trait_type": "Mouth"
        },
        {
          "value": "Space",
          "trait_type": "Background"
        }
      ]
    },
    "timeLastUpdated": "2023-01-01T10:00:00.000Z",
    "contractMetadata": {
      "name": "GalacticExplorers",
      "symbol": "GEXP",
      "totalSupply": "5000",
      "tokenType": "ERC721"
    }
  },
  ...
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

### Simulate a transaction ([@deprecated](v3-endpoints.md#evaluate-transaction))

<mark style="color:purple;">**`POST`**</mark> `https://api.portalhq.io/api/v3/clients/me/simulate-transaction`

Simulates a transaction for a specific chain.

#### Supported Chains

* Ethereum Mainnet (`eip155:1`)
* Ethereum Goerli (`eip155:5`) // @deprecated
* Ethereum Sepolia (`eip155:11155111`)
* Base Mainnet (`eip155:8453`)
* Base Sepolia (`eip155:84532`)
* Polygon Mainnet (`eip155:137`)
* Polygon Mumbai (`eip155:80001`)
* Polygon Amoy (`eip155:80002`)

{% hint style="info" %}
When using CAIP-2 chain formats in the URL (e.g. `eip155:11155111`), ensure the URL is URI encoded to accommodate the "**:**" character.
{% endhint %}

#### Headers

| Name                                            | Type   | Description                   |
| ----------------------------------------------- | ------ | ----------------------------- |
| Authorization<mark style="color:red;">\*</mark> | String | Bearer **`<Client API Key>`** |
| Content-Type<mark style="color:red;">\*</mark>  | String | application/json              |

#### Query Parameters

| Name                                        | Type   | Description                                                                                                              |
| ------------------------------------------- | ------ | ------------------------------------------------------------------------------------------------------------------------ |
| `chainId`<mark style="color:red;">\*</mark> | String | Must be in [CAIP-2 format](https://github.com/ChainAgnostic/CAIPs/blob/main/CAIPs/caip-2.md) (e.g. `"eip155:11155111"`). |

#### Body Parameters

| Name                                     | Type   | Description                                                               |
| ---------------------------------------- | ------ | ------------------------------------------------------------------------- |
| `from`<mark style="color:red;">\*</mark> | String | The sender's address.                                                     |
| `to`<mark style="color:red;">\*</mark>   | String | The recipient's address.                                                  |
| `gas`                                    | String | The gas limit provided for the transaction.                               |
| `gasPrice`                               | String | The price per unit of gas the sender is willing to pay.                   |
| `maxFeePerGas`                           | String | The maximum total fee per gas unit the sender is willing to pay.          |
| `maxPriorityFeePerGas`                   | String | The maximum priority fee per gas unit the sender is willing to pay.       |
| `value`                                  | String | The amount of native token to send with the transaction.                  |
| `data`                                   | String | The input data to send with the transaction (used for contract calls).    |
| `nonce`                                  | String | The transaction count of the sender's address, used to ensure uniqueness. |

#### Example Response

{% tabs %}
{% tab title="200: Success" %}
```json
{
  "changes": [
    {
      "amount": "0.000000000000000016",
      "assetType": "NATIVE",
      "changeType": "TRANSFER",
      "contractAddress": null,
      "decimals": 18,
      "from": "0x0282c3974e48e645d977758838a0211e5c11bd64",
      "name": "Ethereum",
      "rawAmount": "16",
      "symbol": "ETH",
      "to": "0x3fc91a3afd70395cd496c647d5a6cc9d4b2b7fad",
      "tokenId": null
    }
  ],
  "gasUsed": "0x5258"
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

### Get the client's ERC20 assets by chain ([@deprecated](v3-endpoints.md#get-assets-by-chain))

<mark style="color:green;">**`GET`**</mark> `https://api.portalhq.io/api/v3/clients/me/balances`

Retrieves the client's ERC20 balances for a specific chain.

#### Supported Chains

* Ethereum Mainnet (`eip155:1`)
* Ethereum Goerli (`eip155:5`) // @deprecated
* Ethereum Sepolia (`eip155:11155111`)
* Base Mainnet (`eip155:8453`)
* Base Sepolia (`eip155:84532`)
* Polygon Mainnet (`eip155:137`)
* Polygon Mumbai (`eip155:80001`)
* Polygon Amoy (`eip155:80002`)

{% hint style="info" %}
When using CAIP-2 chain formats in the URL (e.g. `eip155:11155111`), ensure the URL is URI encoded to accommodate the "**:**" character.
{% endhint %}

#### Headers

| Name                                            | Type   | Description                   |
| ----------------------------------------------- | ------ | ----------------------------- |
| Authorization<mark style="color:red;">\*</mark> | String | Bearer **`<Client API Key>`** |
| Content-Type<mark style="color:red;">\*</mark>  | String | application/json              |

#### Query Parameters

| Name                                        | Type   | Description                                                                                                              |
| ------------------------------------------- | ------ | ------------------------------------------------------------------------------------------------------------------------ |
| `chainId`<mark style="color:red;">\*</mark> | String | Must be in [CAIP-2 format](https://github.com/ChainAgnostic/CAIPs/blob/main/CAIPs/caip-2.md) (e.g. `"eip155:11155111"`). |

#### Example Response

{% tabs %}
{% tab title="200: Success" %}
```json
[
  {
    "contractAddress": "0x1f9840a85d5af5bf1d1762f925bdaddc4201f984",
    "balance": "0.000211681761928783"
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
