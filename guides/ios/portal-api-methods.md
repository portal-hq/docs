---
description: >-
  Portal API's convenient helper methods are designed to streamline data
  retrieval from the Portal REST API.
---

# Portal API methods

#### **`portal.getNFTAssets`**

Fetches the asset balances (native and token balances) for a specified blockchain. It provides detailed information on the native balance and token balances held by a given address on the specified chain.

```swift
Task {
  do {
    let nfts = try await portal.getNftAssets(chainId)
  } catch {
    // Handle any errors.
  }
}
```

**Parameters**

* `chainId`: The chain identifier in CAIP-2 format (e.g., "eip155:1" for Ethereum mainnet, "solana:5eykt4UsFv8P8NJdTREpY1vzqKqZKvdp" for Solana mainnet)

<details>

<summary>Return type structure</summary>

An array of `NftAsset` objects, each containing:

* `nftID`: Unique identifier for the NFT
* `name`: NFT name
* `description`: NFT description
* `imageURL`: URL of the NFT image
* `chainID`: Blockchain identifier
* `contractAddress`: NFT contract address
* `tokenID`: Token identifier
* `collection`: Collection information
* `lastSale`: Last sale details containing:
  * `price`: Sale price
  * `currency`: Currency used
  * `date`: Sale date
* `rarity`: Rarity information:
  * `rank`: Rarity rank
  * `score`: Rarity score
* `floorPrice`: Current floor price information:
  * `price`: Floor price value
  * `currency`: Currency
* `detailedInfo`: Extended NFT information including:
  * `ownerCount`: Number of owners
  * `tokenCount`: Total tokens
  * `createdDate`: Creation date
  * `attributes`: NFT attributes
  * `owners`: Ownership details
  * `extendedCollectionInfo`: Collection metadata
  * `extendedSaleInfo`: Sale history
  * `marketplaceInfo`: Marketplace listings
  * `mediaInfo`: Media preview URLs and metadata

</details>

#### **`portal.getAssets`**

Retrieves a collection of assets (tokens and NFTs) for the specified blockchain. This method fetches all available assets associated with the wallet on the specified blockchain network, including both fungible tokens and NFTs.

```swift
Task {
  do {
    let nfts = try await portal.getAssets(chainId)
  } catch {
    // Handle any errors.
  }
}
```

**Parameters**

* `chainId`: The chain identifier in CAIP-2 format (e.g., "eip155:1" for Ethereum mainnet, "solana:5eykt4UsFv8P8NJdTREpY1vzqKqZKvdp" for Solana mainnet)

<details>

<summary>Return type structure</summary>

An `AssetsResponse` object containing the following fields:

* **nativeBalance**: A `NativeBalance` object representing the native balance of the wallet on the specified blockchain. Includes:
  * `balance`: The native balance as a string.
  * `decimals`: The number of decimals for the native token.
  * `name`: The name of the native token.
  * `rawBalance`: The raw balance as a string.
  * `symbol`: The symbol of the native token.
  * `metadata`: A `NativeBalanceMetadata` object containing:
    * `logo`: URL of the native token's logo.
    * `thumbnail`: URL of the native token's thumbnail.
* **tokenBalances**: An array of `TokenBalanceResponse` objects representing the fungible token balances. Each object includes:
  * `balance`: The token balance as a string.
  * `decimals`: The number of decimals for the token.
  * `name`: The name of the token.
  * `rawBalance`: The raw balance as a string.
  * `symbol`: The symbol of the token.
  * `metadata`: A `TokenBalanceMetadata` object containing:
    * `tokenAddress`: The contract address of the token.
    * `verifiedContract`: A boolean indicating if the contract is verified.
    * `totalSupply`: The total supply of the token as a string.
    * `rawTotalSupply`: The raw total supply of the token as a string.
    * `percentageRelativeToTotalSupply`: The percentage of the wallet's balance relative to the total supply.
* **nfts**: An array of `Nft` objects representing the non-fungible tokens (NFTs). Each object includes:
  * `nftID`: Unique identifier for the NFT.
  * `name`: The name of the NFT.
  * `description`: The description of the NFT.
  * `imageURL`: URL of the NFT image.
  * `chainID`: The blockchain identifier.
  * `contractAddress`: The contract address of the NFT.
  * `tokenID`: The token identifier.
  * `collection`: A `Collection` object containing collection information.
  * `lastSale`: A `LastSale` object containing details of the last sale:
    * `price`: The sale price.
    * `currency`: The currency used.
    * `date`: The date of the sale.
  * `rarity`: A `Rarity` object containing rarity information:
    * `rank`: The rarity rank.
    * `score`: The rarity score.
  * `floorPrice`: A `NftFloorPrice` object containing the current floor price:
    * `price`: The floor price value.
    * `currency`: The currency used.
  * `detailedInfo`: A `DetailedInfo` object containing extended NFT information:
    * `ownerCount`: The number of owners.
    * `tokenCount`: The total number of tokens.
    * `createdDate`: The creation date of the NFT.
    * `attributes`: An array of `Attribute` objects representing the NFT attributes.
    * `owners`: An array of `Owner` objects representing ownership details:
      * `ownerAddress`: The address of the owner.
      * `quantity`: The quantity of the NFT owned.
      * `firstAcquiredDate`: The date the NFT was first acquired.
      * `lastAcquiredDate`: The date the NFT was last acquired.
    * `extendedCollectionInfo`: An `ExtendedCollectionInfo` object containing collection metadata:
      * `bannerImageURL`: URL of the collection's banner image.
      * `externalURL`: External URL associated with the collection.
      * `twitterUsername`: Twitter username of the collection.
      * `discordURL`: Discord URL of the collection.
      * `instagramUsername`: Instagram username of the collection.
      * `mediumUsername`: Medium username of the collection.
      * `telegramURL`: Telegram URL of the collection.
      * `distinctOwnerCount`: The number of distinct owners in the collection.
      * `distinctNftCount`: The number of distinct NFTs in the collection.
      * `totalQuantity`: The total quantity of NFTs in the collection.
    * `extendedSaleInfo`: An `ExtendedSaleInfo` object containing sale history:
      * `fromAddress`: The address of the seller.
      * `toAddress`: The address of the buyer.
      * `priceUsdCents`: The sale price in USD cents.
      * `transaction`: The transaction hash.
      * `marketplaceID`: The ID of the marketplace.
      * `marketplaceName`: The name of the marketplace.
    * `marketplaceInfo`: An array of `MarketplaceInfo` objects containing marketplace listings:
      * `marketplaceID`: The ID of the marketplace.
      * `marketplaceName`: The name of the marketplace.
      * `marketplaceCollectionID`: The ID of the collection on the marketplace.
      * `nftURL`: URL of the NFT on the marketplace.
      * `collectionURL`: URL of the collection on the marketplace.
      * `verified`: A boolean indicating if the NFT is verified.
      * `floorPrice`: A `MarketplaceInfoFloorPrice` object containing the floor price:
        * `value`: The floor price value.
        * `paymentToken`: A `PaymentToken` object containing payment token details:
          * `paymentTokenID`: The ID of the payment token.
          * `name`: The name of the payment token.
          * `symbol`: The symbol of the payment token.
          * `address`: The address of the payment token.
          * `decimals`: The number of decimals for the payment token.
        * `valueUsdCents`: The floor price value in USD cents.
    * `mediaInfo`: A `MediaInfo` object containing media preview URLs and metadata:
      * `previews`: A `Previews` object containing preview URLs:
        * `imageSmallURL`: URL of the small preview image.
        * `imageMediumURL`: URL of the medium preview image.
        * `imageLargeURL`: URL of the large preview image.
        * `imageOpengraphURL`: URL of the OpenGraph preview image.
        * `blurhash`: The Blurhash string for the image.
        * `predominantColor`: The predominant color of the image.
      * `animationURL`: URL of the animation (if applicable).
      * `backgroundColor`: The background color of the media.

</details>

#### **`portal.getTransactions`**

Fetches a list of the client's transaction history ordered by `blockTimestamp` descending (latest transactions will come first). This includes both inbound and outbound transactions.

```swift
Task {
  do {
    let transactions = try await portal.getTransactions(chainId)
  } catch {
    // Handle any errors.
  }
}
```

**Parameters**

* `chainId`: The chain identifier in CAIP-2 format (e.g., "eip155:1" for Ethereum mainnet, "solana:5eykt4UsFv8P8NJdTREpY1vzqKqZKvdp" for Solana mainnet)
* `limit`: Optional maximum number of transactions to return. If nil, returns all transactions.
* `offset`: Optional number of transactions to skip for pagination. If nil, starts from the beginning.
* `order`: Optional `TransactionOrder` to specify the sort order of transactions: `ASC` or `DESC`.

<details>

<summary>Return type structure</summary>

An array of `FetchedTransaction` objects, each containing:

* `blockNum`: Block number in which the transaction was included
* `uniqueId`: Unique identifier of the transaction
* `hash`: Hash of the transaction
* `from`: Address that initiated the transaction
* `to`: Address that the transaction was sent to
* `value`: Optional value transferred in the transaction as Float
* `erc721TokenId`: Optional token ID for ERC721 NFT transactions
* `erc1155Metadata`: Optional array of ERC1155 token metadata, each containing:
  * `tokenId`: Token identifier
  * `value`: Token value
* `tokenId`: Optional general token identifier
* `asset`: Optional type of asset involved (e.g., "ETH")
* `category`: Category of the transaction (e.g., "external")
* `rawContract`: Optional contract details containing:
  * `value`: Contract value
  * `address`: Contract address
  * `decimal`: Decimal representation of the contract value
* `metadata`: Transaction metadata containing:
  * `blockTimestamp`: ISO format timestamp of the block
* `chainId`: ID of the chain associated with the transaction

</details>

#### **`portal.getBalances`**

Fetches a list of the client's ERC20 token balances.

```swift
Task {
  do {
    let erc20Balances = try await portal.getBalances(chainId)
  } catch {
    // Handle any errors.
  }
}
```

**Parameters**

* `chainId`: The chain identifier in CAIP-2 format (e.g., "eip155:1" for Ethereum mainnet, "solana:5eykt4UsFv8P8NJdTREpY1vzqKqZKvdp" for Solana mainnet)

<details>

<summary>Return type structure</summary>

An array of `FetchedBalance` objects containing token balance information. Each object includes:

* `contractAddress`: The contract address of the token
* `balance`: The balance of the token as a string
* `name`: Optional token name
* `symbol`: Optional token symbol

</details>

#### **`poportal.buildEip155Transaction`**

Creates an unsigned eip155 transaction for transferring assets to another address on a specific chain. You can then use this unsigned eip155 transaction to sign and submit the eip155 transaction.

```swift
Task {
  do {
    let buildTransactionParam = BuildTransactionParam(
        to: "eip155 address",
        token: "ETH",
        amount: "1.0"
      )
    let eip155Transaction = try await portal.buildEip155Transaction(chainId: chainId, params: buildTransactionParam)
  } catch {
    // Handle any errors.
  }
}
```

**Parameters**

1. `chainId`: The chain identifier in CAIP-2 format (e.g., "eip155:1" for Ethereum mainnet)
2. `params`: A `BuildTransactionParam` object containing:
   * `to`: Recipient's address
   * `token`: Token identifier or contract address
   * `amount`: Amount to transfer as a string

<details>

<summary>Return type structure</summary>

A `BuildEip115TransactionResponse` containing:

* `transaction`: An `Eip115Transaction` object with:
  * `from`: Sender's address
  * `to`: Recipient's address
  * `data`: Optional transaction data for contract interactions
  * `value`: Optional transaction value in wei
* `metadata`: A `BuildTransactionMetaData` object containing:
  * `amount`: Formatted transaction amount
  * `fromAddress`: Sender's address
  * `toAddress`: Recipient's address
  * `tokenAddress`: Optional token contract address for ERC20 transfers
  * `tokenDecimals`: Number of decimal places for the token
  * `tokenSymbol`: Optional token symbol
  * `rawAmount`: Raw transaction amount value
* `error`: Optional error message if the build process encounters issues

</details>

#### **`portal.buildSolanaTransaction`**

Creates an unsigned Solana transaction for transferring assets to another address on a specific chain. You can then use this unsigned Solana transaction to sign and submit the Solana transaction.

```swift
Task {
  do {
    let buildTransactionParam = BuildTransactionParam(
        to: "solana address",
        token: "SOL",
        amount: "1.0"
      )
    let solanaTransaction = try await portal.buildSolanaTransaction(chainId: chainId, params: buildTransactionParam)
  } catch {
    // Handle any errors.
  }
}
```

**Parameters**

* `chainId`: The chain identifier in CAIP-2 format (e.g., "solana:5eykt4UsFv8P8NJdTREpY1vzqKqZKvdp" for Solana mainnet)
* `params`: A `BuildTransactionParam` object containing:
  * `to`: Recipient's Solana address
  * `token`: Token identifier ("SOL" for native transfers, mint address for SPL tokens)
  * `amount`: Amount to transfer as a string (in lamports for SOL, raw amount for SPL tokens)

<details>

<summary>Return type structure</summary>

A `BuildSolanaTransactionResponse` containing:

* `transaction`: The serialized transaction string ready for signing
* `metadata`: A `BuildTransactionMetaData` object containing:
  * `amount`: Formatted transaction amount
  * `fromAddress`: Sender's address
  * `toAddress`: Recipient's address
  * `tokenAddress`: Optional token mint address for SPL tokens
  * `tokenDecimals`: Number of decimal places for the token
  * `tokenSymbol`: Optional token symbol
  * `rawAmount`: Raw transaction amount value
* `error`: Optional error message if the build process encounters issues

</details>

#### **`portal.getWalletCapabilities`**

Retrieves the capabilities of the current wallet. This method fetches information about what features and operations are supported by the wallet, including supported chains and operations.

```swift
Task {
  do {
    let capabilities = try await portal.getWalletCapabilities()
  } catch {
    // Handle any errors.
  }
}
```

<details>

<summary>Return type structure</summary>

A `WalletCapabilitiesResponse` object, which is a dictionary where:

* The **key** is a `String` representing a chain identifier (e.g., `"eip155:1"` for Ethereum mainnet).
* The **value** is a `WalletCapabilitiesValue` object containing information about the wallet's capabilities for that chain.

Each `WalletCapabilitiesValue` object includes:

* **paymasterService**: A `PaymasterService` object indicating whether the wallet supports paymaster services for the specified chain. It contains:
  * `supported`: A boolean value (`true` or `false`) indicating whether paymaster services are supported.

</details>

#### **`portal.evaluateTransaction`**

Evaluates a transaction for security and risk assessment, providing both validation and simulation results. This method can be used to assess the safety and potential risks of a transaction before execution.

```swift
Task {
  do {
    let evaluationResult = try await portal.evaluateTransaction(
      chainId: chainId,
      transaction: transaction,
      operationType: operationType
    )
  } catch {
    // Handle any errors.
  }
}
```

**Parameters**

* `chainId`: The chain identifier in CAIP-2 format (e.g., `"eip155:1"` for Ethereum mainnet).
* `transaction`: An `EvaluateTransactionParam` object containing the transaction details:
  * `to`: (Required) The destination address.
  * `value`: (Optional) The transaction value in wei.
  * `data`: (Optional) The transaction data for contract interactions.
  * `maxFeePerGas`: (Optional) The maximum total fee per gas unit.
  * `maxPriorityFeePerGas`: (Optional) The maximum priority fee per gas unit.
  * `gas`: (Optional) The gas limit.
  * `gasPrice`: (Optional) The gas price for legacy transactions.
* `operationType`: Optional `EvaluateTransactionOperationType` to specify the type of evaluation:
  * `.validation`: Perform security validation only.
  * `.simulation`: Perform transaction simulation only.
  * `.all`: Perform both validation and simulation.

<details>

<summary>Return type structure</summary>

A `BlockaidValidateTrxRes` object containing the following fields:

* **validation**: Security validation results, including:
  * `classification`: The type of potential security issue.
  * `description`: A detailed description of the validation results.
  * `features`: An array of detected security features, each containing:
    * `type`: The type of feature.
    * `featureId`: A unique identifier for the feature.
    * `description`: A description of the feature.
    * `address`: The related address, if applicable.
    * `reason`: An explanation of the validation result.
  * `resultType`: The type of result.
  * `status`: The validation status.
* **simulation**: Transaction simulation results, including:
  * `accountAddress`: The address being analyzed.
  * `accountSummary`: A summary of account state changes.
  * `addressDetails`: Detailed information about involved addresses.
  * `assetsDiffs`: Asset balance changes, containing:
    * `asset`: Information about the asset.
    * `in`: Details of incoming transfers.
    * `out`: Details of outgoing transfers.
  * `exposures`: Risk exposure analysis.
  * `status`: The simulation status.
  * `totalUsdDiff`: The total USD value change.
  * `totalUsdExposure`: The total USD value at risk.
  * `block`: The block number used for evaluation.
  * `chain`: The chain identifier.

</details>



**Related Documentation**

* [getNftAssets function reference](../../reference/ios/getnftassets.md)
* [getTransactions function reference](../../reference/ios/gettransactions.md)
* [getBalances function refernce](../../reference/ios/getbalances.md)
* [buildEip155Transaction function reference](../../reference/ios/buildeip155transaction.md)
* [buildSolanaTransaction function reference](../../reference/ios/buildsolanatransaction.md)
* [getWalletCapabilities function reference](../../reference/ios/getwalletcapabilities.md)
* [evaluateTransaction function reference](../../reference/ios/evaluatetransaction.md)
