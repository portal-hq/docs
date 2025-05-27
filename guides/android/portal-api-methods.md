---
description: >-
  Portal API's convenient helper methods are designed to streamline data
  retrieval from the Portal REST API.
---

# Portal API methods

### `portal.api`

The `api` property contains an instance of the `PortalApi` class, which has a number of helper methods to facilitate the retrieval of relevant application data from the Portal REST API.

#### **`portal.api.getNFTs`**

Fetches a list of non-fungible tokens (NFTs). The response is an array of objects where each object represents a unique NFT.

```kotlin
val nftAssets = portal.api.getNftAssets(chainId)
```

<details>

<summary>Example API response</summary>

Please note that this is purely fictitious response data.

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

</details>

#### **`portal.api.getAssets`**

This endpoint retrieves the asset balances (native and token balances) for a specified blockchain. It provides detailed information on the native balance and token balances held by a given address on the specified chain.

```kotlin
val assets = portal.api.getAssets(chainId)
```

<details>

<summary>Example API response</summary>

Please note that this is purely fictitious response data.

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

</details>

#### **`portal.api.getTransactions`**

Fetches a list of the client's transaction history ordered by `blockTimestamp` descending (latest transactions will come first). This includes both inbound and outbound transactions.

```kotlin
val transactions = portal.api.getTransactions(chainId = "CHAIN_ID")
```

{% hint style="info" %}
Optional arguments can also be provided:

* `limit`: (Optional) The maximum number of transactions to return.
* `offset`: (Optional) The number of transactions to skip before starting to return.
* `order`: (Optional) Order in which to return the transactions. Either `"asc"` or `"desc"`.
* `chainId`: (Optional) ID of the chain to retrieve transactions from. Defaults to your Portal instance's chainId if not provided.
{% endhint %}

<details>

<summary>Response structure</summary>

The response from the API request is a JSON array of objects. Each object represents a unique transaction and consists of the following fields:

* `blockNum`: A string representing the hexadecimal number of the block in which the transaction occurred.
* `uniqueId`: A unique string that uniquely identifies this transaction. It is constructed from the transaction's hash and category.
* `hash`: A string that represents the hash of the transaction. This hash can be used as a unique identifier for the transaction on the blockchain.
* `from`: A string that represents the address of the sender of the transaction.
* `to`: A string that represents the address of the receiver of the transaction.
* `value`: A floating-point number that represents the value of the transaction in the asset type mentioned in the `asset` field.
* `erc721TokenId`: This field is null for non-ERC721 transactions. For ERC721 transactions, this field contains the unique identifier of the ERC721 token.
* `erc1155Metadata`: This field is null for non-ERC1155 transactions. For ERC1155 transactions, this field contains any metadata associated with the ERC1155 token.
* `tokenId`: This field is null for non-token transactions. For token transactions, it represents the unique identifier of the token involved in the transaction.
* `asset`: A string that represents the type of asset that was transferred in the transaction. For example, the asset type could be "ETH" when transferring ETH from one address to another one.
* `category`: A string that represents the category of the transaction. Can be any of the following: "external", "internal", "erc20", "erc721", "erc1155", or "specialnft".
* `rawContract`: An object that contains raw contract details:
  * `value`: A hexadecimal string representing the raw value of the transaction.
  * `address`: The address of the contract. This is null for non-contract transactions.
  * `decimal`: A hexadecimal string representing the number of decimal places that the `value` field should be calculated to.
* `metadata`: An object that contains additional details about the transaction:
  * `blockTimestamp`: Timestamp of the block from which the transaction event originated (ISO-formatted timestamp).
* `chainId`: An integer denoting the blockchain network identifier.

In Kotlin, this function returns `List<Transaction>`.

</details>

<details>

<summary>Example API response</summary>

Please note that this is purely fictitious response data.

```json
[
  {
    "blockNum": "0x1a2b3c",
    "uniqueId": "0xabc123def456ghi789jkl012mno345pqr678stu901vwx234yza567b:internal",
    "hash": "0xabc123def456ghi789jkl012mno345pqr678stu901vwx234yza567b",
    "from": "0x1b2c3d4e5f6a7b8c9d0e1f2a3b4c5d6e7f8a9b0",
    "to": "0x0a9b8c7d6e5f4a3b2c1d0e9f8a7b6c5d4e3f2a1",
    "value": 0.257842,
    "erc721TokenId": null,
    "erc1155Metadata": null,
    "tokenId": null,
    "asset": "ETH",
    "category": "internal",
    "rawContract": {
      "value": "0x37fa23dbc90000",
      "address": null,
      "decimal": "0x12"
    },
    "metadata": {
      "blockTimestamp": "2023-08-30T23:11:24.000Z"
    }
  },
  {
    "blockNum": "0x2b3c4d",
    "uniqueId": "0xdef456ghi789jkl012mno345pqr678stu901vwx234yza567bca123:internal",
    "hash": "0xdef456ghi789jkl012mno345pqr678stu901vwx234yza567bca123",
    "from": "0x2c3d4e5f6a7b8c9d0e1f2a3b4c5d6e7f8a9b0c1",
    "to": "0x1a9b8c7d6e5f4a3b2c1d0e9f8a7b6c5d4e3f2a1",
    "value": 0.367925,
    "erc721TokenId": null,
    "erc1155Metadata": null,
    "tokenId": null,
    "asset": "ETH",
    "category": "internal",
    "rawContract": {
      "value": "0x50ca24dbfc4000",
      "address": null,
      "decimal": "0x12"
    },
    "metadata": {
      "blockTimestamp": "2023-08-30T23:11:24.000Z"
    }
  }
]
```

</details>

{% include "../../.gitbook/includes/portal.api.buildeip155trans....md" %}

#### **`portal.api.getBackupShareMetadata`**

Fetches a list of the client's backup shares' metadata, such as the backup method, when it was created, and the backup share's `id`.

```kotlin
val backupSharePairs = portal.api.getBackupShareMetadata()
```

<details>

<summary>Response structure</summary>

The response is a JSON array of backup share pairs. Each backup share pair object provides information about the backup method, when it was created, and its `id`.

Each backup share pair object contains the following fields:

* `backupMethod`: A string representing the backup method that was used for this backup run. One of: `CUSTOM`, `GDRIVE`, `ICLOUD`, `PASSWORD`, `PASSKEY`, or `UNKNOWN`.
* `id`: A string representing the "backup share pair ID" for the client's specific backup run. This is the same ID that can be found within the backup share JSON string we send to your `/backup` webhook endpoint.
* `createdAt`: A string representing the datetime that the backup was created.

Note: This endpoint only returns back backup shares' metadata that successfully completed the backup process.

In Kotlin, the completion handler returns `List<BackupSharePair>`.&#x20;

</details>

<details>

<summary>Example API response</summary>

Please note that this is purely fictitious response data.

```json
[
  {
    "backupMethod": "UNKNOWN", // Legacy (v1-v5)
    "id": "cuid1",
    "createdAt": "2023-11-28T21:55:13.630Z"
  },
  {
    "backupMethod": "PASSWORD",
    "id": "cuid2",
    "createdAt": "2023-11-28T21:55:13.630Z"
  },
  {
    "backupMethod": "GDRIVE",
    "id": "cuid3",
    "createdAt": "2023-11-28T21:55:13.630Z"
  },
  {
    "backupMethod": "ICLOUD",
    "id": "cuid4",
    "createdAt": "2023-11-28T21:55:13.630Z"
  },
  {
    "backupMethod": "PASSKEY",
    "id": "cuid5",
    "createdAt": "2023-11-28T21:55:13.630Z"
  },
]
```

</details>
