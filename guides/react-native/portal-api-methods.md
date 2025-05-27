---
description: >-
  Portal API's convenient helper methods are designed to streamline data
  retrieval from the Portal REST API.
---

# Portal API methods

### `portal.api`

The `api` property contains an instance of the `PortalApi` class, which has a number of helper methods to facilitate the retrieval of relevant application data from the Portal REST API.

#### **`portal.api.buildTransaction`**

Creates an unsigned eip155/solana transaction for transferring assets to another address on a specific chain. You can then use this unsigned eip155/solana transaction to sign and submit the eip155/solana transaction.

```typescript
const txDetails = await portal.api.buildTransaction(
  '0xDestinationAddress',
  'USDC', // Friendly token name, or token address (mint address for Solana)
  '1', // Sends 1 USDC
  'sepolia', // Friendly chain or CAIP-2 chainId
)
```

<details>

<summary>Example response (Sending USDC on Ethereum Sepolia)</summary>

```json
{
  "transaction": {
    "from": "0xc690a7F8F02D551287C049997C9A003Ffe65CAD5",
    "to": "0xf08A50178dfcDe18524640EA6618a1f965821715",
    "data": "0xa9059cbb000000000000000000000000dfd8302f44727a6348f702ff7b594f127de3a90200000000000000000000000000000000000000000000000000000000000186a0"
  },
  "metadata": {
    "amount": "0.1",
    "fromAddress": "0xc690a7F8F02D551287C049997C9A003Ffe65CAD5",
    "toAddress": "0xdFd8302f44727A6348F702fF7B594f127dE3A902",
    "tokenAddress": "0xf08A50178dfcDe18524640EA6618a1f965821715",
    "tokenDecimals": 6,
    "rawAmount": "100000"
  }
}
```

</details>

<details>

<summary>Example response (Sending USDC on Solana Devnet)</summary>

```json
{
  "transaction": "AQAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAABAAUIYOC3CyvldvId8bF28rNypn1C7OBqvhXqEYXLKLeWh6ou38jGPjCqlLFY3TlPbCHB6R+g/1F1WqGrCV4DL8GB9XhhA/bXqsIjAl2JBhtZTr77wOyNjGsBM3b+kC2y56V7AAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAA7RCyzkSFX8TqTPQE0KC0DK1/+zQGi2/G3eQYI3wAupwHVrOJiBDitgxRzQDkrbLih58c3zcw12JO80+paiyM8jJclj04kifG7PRApFI4NgwtaE5na/xCEBI572Nvp+FkG3fbh12Whk9nL4UbO63msHLSF7V9bN5E6jPWFfv8AqbQF5ecBPqhVgg4ZLgfMMY9c0oT1wlGamtcEtNX/+C0iAgYGAAEFBAMHAAcDAgEACQOghgEAAAAAAA==",
  "metadata": {
    "amount": "0.1",
    "fromAddress": "7XAsvsVfvzQu61gHpa1QDMFF5Zsz5DrU6u2LAizNrXPX",
    "toAddress": "8APEEA4SHrfGteABQcUmH2yEHy7nEe8DqgvmKGAStHR",
    "tokenMintAddress": "4zMMC9srt5Ri5X14GAgXhaHii3GnPAEERYPJgZJDncDU",
    "tokenDecimals": 6,
    "tokenProgramId": "TokenkegQfeZyiNwAJbNbGKPFXCWuBvf9Ss623VQ5DA",
    "tokenExtensions": [],
    "rawAmount": "100000",
    "lastValidBlockHeight": "342868333",
    "serializedTransactionBase64Encoded": "AQAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAABAAUIYOC3CyvldvId8bF28rNypn1C7OBqvhXqEYXLKLeWh6ou38jGPjCqlLFY3TlPbCHB6R+g/1F1WqGrCV4DL8GB9XhhA/bXqsIjAl2JBhtZTr77wOyNjGsBM3b+kC2y56V7AAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAA7RCyzkSFX8TqTPQE0KC0DK1/+zQGi2/G3eQYI3wAupwHVrOJiBDitgxRzQDkrbLih58c3zcw12JO80+paiyM8jJclj04kifG7PRApFI4NgwtaE5na/xCEBI572Nvp+FkG3fbh12Whk9nL4UbO63msHLSF7V9bN5E6jPWFfv8AqbQF5ecBPqhVgg4ZLgfMMY9c0oT1wlGamtcEtNX/+C0iAgYGAAEFBAMHAAcDAgEACQOghgEAAAAAAA==",
    "serializedTransactionBase58Encoded": "4c5ErVEQWyXMS7yGF2TJaJBf7Vk3bkdaXx6LrCS3xkeLUPWnJMde1CmjFFxEEwbwLjX6N2u2RNpNFhTPML7ow4ZDpUPB5NkxQpiNawgUhYUj9bBDeFQfXjYzFFjs7fVD6jRJzmqQSCrdWFiFP2rwMiHXiZqj4MFY7HFwnfymJp3zaXxfG2VxEXVJU5ii9ddvjynNg7jJvhZ4ysUpjTJkWAVdj3UCJAkaqjZoeapmFVm73113uAJV2arPqnNtoouj8vcHYNKLdA1NnG3T8ZSAeePWTmruEH9uCtMpXgiNefgwvA4szZ6DNKDCAREUWXH38cLH3XQjgjLDdRXmZ8xQHYacuwmtXVVjdUzV3rcVuC1NM4YmHkRnw8nfULm4PmrZoBxchnS7vNXxXHBAd9ysg1VQp5v21oBsgjegZMrxfo8wzr3HHvjXoBw3LeBJ1raWYAAa1S32oE19Hvqhm3EKFaF9txb46j4kHVDqbmPuVQDCzAHAjMZHiALqL2MCQJGimS86gfq7d",
    "unsignedTransactionMessage": {
      "signatures": null,
      "message": {
        "accountKeys": [
          "7XAsvsVfvzQu61gHpa1QDMFF5Zsz5DrU6u2LAizNrXPX",
          "49yfs2fpy9HhVRU6icApq3QvNdYRBrtcRJojTT7jnUAx",
          "96uj2xF4aT7JfkvsYXjeejaFMtzPYQwyMDti29QXWeux",
          "11111111111111111111111111111111",
          "4zMMC9srt5Ri5X14GAgXhaHii3GnPAEERYPJgZJDncDU",
          "8APEEA4SHrfGteABQcUmH2yEHy7nEe8DqgvmKGAStHR",
          "ATokenGPvbdGVxr1b2hvZbsiqW5xWH25efTNsLJA8knL",
          "TokenkegQfeZyiNwAJbNbGKPFXCWuBvf9Ss623VQ5DA"
        ],
        "header": {
          "numRequiredSignatures": 1,
          "numReadonlySignedAccounts": 0,
          "numReadonlyUnsignedAccounts": 5
        },
        "instructions": [
          {
            "programIdIndex": 6,
            "accounts": [
              0,
              1,
              5,
              4,
              3,
              7
            ],
            "data": ""
          },
          {
            "programIdIndex": 7,
            "accounts": [
              2,
              1,
              0
            ],
            "data": "3gJqkocMWaMm"
          }
        ],
        "recentBlockhash": "D7jbugcuXsPj7zejJ5i3xSHQU9Jj1mpsVw7hUTawLcrd"
      }
    }
  }
}
```

</details>

#### **`portal.api.getNFTs`**

Fetches a list of non-fungible tokens (NFTs). The response is an array of objects where each object represents a unique NFT.

```typescript
const nfts = await portal.api.getNFTs()
```

<details>

<summary>Response structure</summary>

The response is a JSON array of objects. Each object represents a unique NFT and consists of the following fields:

* `contract`: An object which contains the `address` field, representing the contract address associated with the NFT.
* `id`: An object with the following fields:
  * `tokenId`: The unique identifier of the NFT within its contract.
  * `tokenMetadata`: An object containing metadata about the token type.
* `balance`: The balance of the NFT, represented as a string.
* `title`: The title of the NFT.
* `description`: A description of the NFT.
* `tokenUri`: An object with `gateway` and `raw` fields that hold URI links to access the token.
* `media`: An array of objects each representing a media file associated with the NFT. Each object includes:
  * `gateway`: The URL to access the media file.
  * `thumbnail`: The URL to access the thumbnail image of the media file.
  * `raw`: The raw link to the media file.
  * `format`: The format of the media file.
  * `bytes`: The size of the media file in bytes.
* `metadata`: An object which contains:
  * `name`: The name of the NFT.
  * `description`: The description of the NFT.
  * `image`: The URL to access the image associated with the NFT.
  * `external_url`: The external URL that provides additional information about the NFT.
* `timeLastUpdated`: The time when the NFT was last updated.
* `contractMetadata`: An object which contains additional metadata about the contract, such as:
  * `name`: The name of the contract.
  * `symbol`: The symbol of the contract.
  * `tokenType`: The type of the token. (e.g., ERC1155)
  * `contractDeployer`: The address of the contract deployer.
  * `deployedBlockNumber`: The number of the block where the contract was deployed.
  * `openSea`: An object which contains additional metadata from the OpenSea platform.

This is a general structure and it may contain more fields depending on the NFT and the contract. For example, `openSea` object in `contractMetadata` might include information like `collectionName`, `safelistRequestStatus`, `imageUrl`, `description`, `externalUrl`, `lastIngestedAt`, `floorPrice`, `twitterUsername`, `discordUrl`, etc. Always refer to the actual API response for the most accurate information.

</details>

<details>

<summary>Example response</summary>

Please note that this is purely fictitious response data.

```json
[
  {
    "contract": {
      "address": "0x9aabbcc1122abc22332bcda1123ef123"
    },
    "id": {
      "tokenId": "0x0000000000000000000000000000000000000000000000000000000000001234",
      "tokenMetadata": {
        "tokenType": "ERC721"
      }
    },
    "balance": "1",
    "title": "Astral Codex #3784",
    "description": "The mystical realms of Astral Codex. A collection of 10,000 NFTs.",
    "tokenUri": {
      "gateway": "https://ipfs.io/ipfs/QmABCDE1234cD2aB1C2d3A1b2C3d4A1b2C3d4A1b2C3d4A1b2C3d4A/3784.json",
      "raw": "ipfs://QmABCDE1234cD2aB1C2d3A1b2C3d4A1b2C3d4A1b2C3d4A1b2C3d4A/3784.json"
    },
    "media": [
      {
        "gateway": "https://nft-cdn.alchemy.com/eth-mainnet/abc123abc123abc123abc123abc123",
        "thumbnail": "https://res.cloudinary.com/alchemyapi/image/upload/thumbnailv2/eth-mainnet/abc123abc123abc123abc123abc123",
        "raw": "ipfs://QmABCDE1234cD2aB1C2d3A1b2C3d4A1b2C3d4A1b2C3d4A1b2C3d4A/3784.png",
        "format": "png",
        "bytes": 352374
      }
    ],
    "metadata": {
      "date": 1649727051672,
      "image": "ipfs://QmABCDE1234cD2aB1C2d3A1b2C3d4A1b2C3d4A1b2C3d4A1b2C3d4A/3784.png",
      "dna": "957652df6de72ad2ef7d2390def23f20a11081aa",
      "name": "Astral Codex #3784",
      "description": "The mystical realms of Astral Codex. A collection of 10,000 NFTs.",
      "edition": 3784,
      "attributes": [
        {
          "value": "Aqua",
          "trait_type": "Color"
        },
        {
          "value": "Owl",
          "trait_type": "Animal"
        },
        {
          "value": "Stars",
          "trait_type": "Background"
        }
      ]
    },
    "timeLastUpdated": "2023-05-15T17:42:13.256Z",
    "contractMetadata": {
      "name": "Astral Codex",
      "symbol": "AC",
      "totalSupply": "10000",
      "tokenType": "ERC721",
      "contractDeployer": "0x1b580d3041cc953adfc2348e5be6c1c893ccb9e2",
      "deployedBlockNumber": 15973106,
      "openSea": {
        "floorPrice": "0.5",
        "collectionName": "Astral Codex",
        "safelistRequestStatus": "approved",
        "imageUrl": "https://ipfs.io/ipfs/QmABCDE1234cD2aB1C2d3A1b2C3d4A1b2C3d4A1b2C3d4A1b2C3d4A/3784.png",
        "description": "The mystical realms of Astral Codex. A collection of 10,000 NFTs.",
        "externalUrl": "https://www.astralcodexnfts.com/",
        "lastIngestedAt": "2023-05-15T17:42:13.256Z"
      }
    }
  }
]
```

</details>

#### **`portal.api.getTransactions`**

Fetches a list of the client's transaction history ordered by `blockTimestamp` descending (latest transactions will come first). This includes both inbound and outbound transactions.

```typescript
const transactions = await portal.api.getTransactions()
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

The response is a JSON array of objects. Each object represents a unique transaction and consists of the following fields:

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

</details>

<details>

<summary>Example response</summary>

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

#### **`portal.api.getBalances`**

Fetches a list of the client's ERC20 token balances.

```typescript
const tokenBalances = await portal.api.getBalances()
```

<details>

<summary>Response structure</summary>

The response is a JSON array of balance objects. Each balance object provides information about the balance of a specific asset that the client owns on different contracts.

Each balance object contains the following fields:

* `contractAddress`: A string representing the address of the smart contract which manages the asset.
* `balance`: A string representing the balance of the asset that the user owns, held at the respective contract address. It's in the smallest denomination of the asset (e.g. 'wei' in the case of Ether and many ERC20 tokens).

</details>

<details>

<summary>Example response</summary>

Please note that this is purely fictitious response data.

```json
[
  {
    "contractAddress": "0xdac17f958d2ee523a2206206994597c13d831ec7",
    "balance": "0"
  },
  {
    "contractAddress": "0xA0b86991c6218b36c1d19D4a2e9Eb0cE3606eB48",
    "balance": "12345"
  }
]
```

</details>

#### **`portal.api.getBackupShareMetadata`**

Fetches a list of the client's backup shares' metadata, such as the backup method, when it was created, and the backup share's `id`.

```typescript
const backupSharePairs = await portal.api.getBackupShareMetadata()
```

<details>

<summary>Response structure</summary>

The response is a JSON array of backup share pairs. Each backup share pair object provides information about the backup method, when it was created, and its `id`.

Each backup share pair object contains the following fields:

* `backupMethod`: A string representing the backup method that was used for this backup run. One of: `CUSTOM`, `GDRIVE`, `ICLOUD`, `PASSWORD`, `PASSKEY`, or `UNKNOWN`.
* `id`: A string representing the "backup share pair ID" for the client's specific backup run. This is the same ID that can be found within the backup share JSON string we send to your `/backup` webhook endpoint.
* `createdAt`: A string representing the datetime that the backup was created.

Note: This endpoint only returns back backup shares' metadata that successfully completed the backup process.

</details>

<details>

<summary>Example response</summary>

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
