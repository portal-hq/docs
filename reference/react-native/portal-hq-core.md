# @portal-hq/core

The `@portal-hq/core` package includes a class constructor for the `Portal` class, a React Context Provider for the `PortalContext`, the `usePortal` hook for use within child components, and some helpful types and enums for working with Portal in your app. These pieces allow you to initialize `Portal` in your app, expose the instance to your component tree, and consume the instance in your child components.

## The Portal class

The `Portal` class that houses three main sub-classes

* `api` makes requests to the Portal api
* `mpc` facilitates the generation, backup, and recovery of MPC wallets
* `provider` the core Portal provider ([EIP-1193](https://eips.ethereum.org/EIPS/eip-1193) compliant)

### Instantiation

When instantiating the `Portal` class, you must provide a `PortalOptions` object. This object is used to initialize all of the sub-classes (and their sub-classes).

#### Example instantiation

```typescript
import { BackupMethods, Portal } from '@portal-hq/core'

const portal = new Portal({
  apiKey: 'YOUR_PORTAL_CLIENT_API_KEY',
  backup: {
    // Expects an instance of @portal-hq/gdrive-storage
    [BackupMethods.GoogleDrive]: gDriveStorage, 
    // Expects an instance of @portal-hq/icloud-storage
    [BackupMethods.iCloud]: iCloudStorage,
  },
  chainId: 1,
  gatewayConfig: 'https://mainnet.infura.io/v3/YOUR_INFURA_API_KEY',
  isSimulator: DeviceInfo.isEmulator(),
  // Expects an instance of @portal-hq/keychain
  // or @portal-hq/mobile-key-values
  keychain: keychain,
})
```

#### PortalOptions

The `PortalOptions` object is where you configure your instance of `Portal`. It contains a set of required properties and a set of optional properties.

**Required properties**

* `apiKey` **string** a valid Portal Client API Key created via the [Portal REST API](https://api.portalhq.io/api/clients)
* `backup` **object**
  * `key` **BackupMethods**  - an entry from the `BackupMethods` enum (exported by `@portal-hq/core`
  * `value` **CloudStorageAdapter** - an instance of either [@portal-hq/gdrive-storage](storage-adapters/cloud-storage/portal-hq-gdrive-storage.md) or [@portal-hq/icloud-storage](storage-adapters/cloud-storage/portal-hq-icloud-storage.md)
* `chainId` **number** the ID of the current Ethereum chain you'd like to perform actions on
  * You can update this property on your `Portal` instance later – if your app requires this – by setting `portal.chainId` property
* `gatewayConfig` **string** or **GatewayConfig** the base url (including your API key) you'd like us to use for Gateway requests (reading from and writing to chain) _**Note: this will be required in the future**_
  * `GatewayConfig` – if you don't want to use the same url for all requests, you can instead provide a chain-level config for all Gateway RPC calls this can be passed in as an object with key/value pairs where the `key` is the `chainId` as a number and the value is the base url you'd like to use when performing actions on this `chainId`
* `isSimulator` **boolean** whether or not you're currently running the app in a simulator (this is required to ensure that keychain storage is configured appropriately for the current device)
* `keychain` **MobileStorageAdapter** - an instance of either [@portal-hq/keychain](storage-adapters/mobile-storage/portal-hq-keychain.md) or [@portal-hq/mobile-key-values](storage-adapters/mobile-storage/portal-hq-mobile-key-values.md)

**Optional properties**

* `autoApprove` **boolean** (default: `false`) whether you'd like the provider to auto-approve transactions

### `portal.api`

The `api` property contains an instance of the `PortalApi` class, which has a number of helper methods to facilitate the retrieval of relevant application data from the Portal REST API.

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
const transactions = await portal.api.getTransactions(limit, offset)
```

{% hint style="info" %}
`limit` and `offset` can optionally be provided as arguments to paginate the list of transactions.
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

### `portal.mpc`

The `mpc` property contains an instance of the `PortalMpc` class, which has a number of helper methods to facilitate the management of Portal MPC wallets.

#### Methods

#### **`portal.mpc.generate`**

Performs the MPC generate process to create an MPC wallet and its signing shares

**Example usage**

```typescript
const createWallet = async () => {
  await portal.mpc.generate()
}
```

#### **`portal.mpc.backup`**

Performs the MPC backup process to create the backup shares for the generated MPC wallet.

**Example usage**

```typescript
const backupWallet = async () => {
  await portal.mpc.backup()
}
```

#### **`portal.mpc.recover`**

Performs the MPC recovery process to generate new signing shares for the MPC wallet.

**Example usage**

```typescript
const recoverWallet = async () => {
  await portal.mpc.recover()
}
```

### `portal.provider`

The `provider` property contains an [EIP-1193](https://eips.ethereum.org/EIPS/eip-1193) compliant provider.

#### `portal.provider.request`

In order to perform basic web3 operations, such as `eth_accounts` and `eth_sendTransaction`, you can use the provider's `request` method.

This method conforms to [EIP-1193](https://eips.ethereum.org/EIPS/eip-1193).

For a full list of supported JSON RPC methods and their associated params, please see [Ethereum's JSON-RPC API docs](https://ethereum.org/en/developers/docs/apis/json-rpc/).

**Example usage**

```typescript
const transaction = {
  data: '',
  to: toAddress,
  value: BigNumber.from('1').toHexString(),
  gasPrice: '0x6000',
  from: portal.address, // The address of the current connected wallet
}

const txHash = await portal.provider.request({
  method: 'eth_sendTransaction',
  params: transaction,
})
```

### **`portal.setChainId`**

This method allows you to specify the chain ID of the blockchain network that your Portal instance should interact with. This method is particularly useful in a multi-chain or cross-chain context where your application might need to switch between different blockchains.

```typescript
const mainnet = 1
// const goerli = 5

await portal.setChainId(mainnet)
// await portal.setChainId(goerli)
```

## The Portal context

`PortalContext` is used to persist an instance of `Portal` within your application. This allows you to initialize `Portal` once, and easily share this instance between all of the components within a given scope in your app.

The exported members allow for 2 ways to set the `PortalContext` and one way to get the `PortalContext`.

### Setters

You only need to choose one of these when implementing the `PortalContext` in your app.

* `PortalContextProvider` a React Context Provider to provide your `Portal` instance to the components in your app

### Getters

* `usePortal` a React hook to use the `Portal` instance in any component within the `PortalContextProvider` scope (any component being rendered as either a shallow or deep child of the `PortalContextProvider`)

_For more info on working with React Context,_ [_check out the docs_](https://reactjs.org/docs/context.html)_._

### `PortalContextProvider`

The `PortalContextProvider` allows you to share your instance of the Portal class with all components in your component tree. Providing a `value` prop with your Portal instance and wrapping your components in the `PortalContextProvider` enables this behavior. All children of the `PortalContextProvider` can access the Portal instance using the `usePortal` hook (see below).

_**Note**: The easiest way to ensure you're exposing Portal to all components in your component tree is to use it in your App component_

```tsx
// MyRootComponent.tsx

import { useEffect, useState } from 'react'
import { Portal, PortalContextProvider } from '@portal-hq/core'

const MyRootComponent = () => {
  const [portal, setPortal] = useState<Portal>(null)

  useEffect(() => {
    if (!portal) {
      setPortal(
        new Portal({
          // ...configuration options
        }),
      )
    }
  }, [portal])

  return (
    <PortalContextProvider value={portal}>
      {/* 
        Now all children rendered in this scope 
        will have access to the `Portal` instance 
        via the `usePortal` hook 
      */}
    </PortalContextProvider>
  )
}
```

### `usePortal`

The `usePortal` hook allows any child in the component tree below a `PortalContextProvider` to access the Portal instance.

```tsx
// MyChildComponent.tsx

import { usePortal } from '@portal-hq/core'

const MyChildComponent = () => {
  const portal = usePortal()

  // You can now do things with the `Portal` instance directly, such as:
  // - `await portal.api.getEnabledDapps()`
  // - `await portal.mpc.generate()`
  //
  // For more information on this, please see the `Portal` docs in `src/lib/portal`

  ...
}
```

## Additional exports

In addition to the `Portal` class and context exports, the `@portal-hq/core` package exports some helpful types and enums for use when building your app.

The additional exports are as follows:

* `BackupMethods` – an enum of supported storage methods for backup MPC key shares
* `Contract` - a type representing Portal Contract records
* `Dapp` - a type representing Portal Dapp records returns `Dapp[]`
* `PortalOptions` - a type representing the object used to configure the `Portal` class on initialization
