---
description: >-
  Retrieves a collection of assets (tokens and NFTs) for the specified
  blockchain.
---

# getAssets

**Function Signature**

```swift
public func getAssets(
    _ chainId: String
) async throws -> AssetsResponse
```

**Parameters**

* `chainId`: The chain identifier in CAIP-2 format (e.g., "eip155:1" for Ethereum mainnet, "solana:5eykt4UsFv8P8NJdTREpY1vzqKqZKvdp" for Solana mainnet)

**Returns**

An `AssetsResponse` object containing:

* `nativeBalance`: Native token balance information (e.g., ETH for Ethereum)
  * `balance`: Current balance as string
  * `decimals`: Number of decimal places
  * `name`: Token name
  * `symbol`: Token symbol
  * `rawBalance`: Raw balance value
  * `metadata`: Contains token logo and thumbnail URLs
* `tokenBalances`: Array of ERC20 token balances, each containing:
  * `balance`: Current balance as string
  * `decimals`: Number of decimal places
  * `name`: Token name
  * `symbol`: Token symbol
  * `rawBalance`: Raw balance value
  * `metadata`: Additional token information including:
    * `tokenAddress`: Contract address
    * `verifiedContract`: Contract verification status
    * `totalSupply`: Total token supply
    * `percentageRelativeToTotalSupply`: Holder's percentage of total supply
* `nfts`: Array of NFT assets, each containing:
  * `nftID`: Unique identifier
  * `name`: NFT name
  * `description`: NFT description
  * `imageURL`: NFT image URL
  * `chainID`: Blockchain identifier
  * `contractAddress`: NFT contract address
  * `tokenID`: Token identifier
  * `collection`: Collection information
  * `lastSale`: Last sale information (price, currency, date)
  * `rarity`: Rarity information (rank and score)
  * `floorPrice`: Current floor price information
  * `detailedInfo`: Extended NFT information including:
    * Ownership details
    * Collection statistics
    * Marketplace information
    * Media previews and metadata

**Example Usage**

```swift
// Fetch and display all assets
do {
    let assets = try await portal.getAssets("eip155:1")
    
    // Display native token balance
    if let native = assets.nativeBalance {
        print("Native Token:")
        print("Name: \(native.name ?? "Unknown")")
        print("Balance: \(native.balance ?? "0")")
        print("Symbol: \(native.symbol ?? "?")")
    }
    
    // Display ERC20 token balances
    print("\nERC20 Tokens:")
    assets.tokenBalances?.forEach { token in
        guard let name = token.name,
              let symbol = token.symbol,
              let balance = token.balance else {
            return
        }
        print("\(name) (\(symbol)): \(balance)")
    }
    
    // Display NFTs with detailed information
    print("\nNFTs:")
    assets.nfts?.forEach { nft in
        print("\nNFT: \(nft.name ?? "Unnamed")")
        print("ID: \(nft.nftID ?? "Unknown")")
        
        if let lastSale = nft.lastSale,
           let price = lastSale.price,
           let currency = lastSale.currency {
            print("Last Sale: \(price) \(currency)")
        }
        
        if let floorPrice = nft.floorPrice,
           let price = floorPrice.price,
           let currency = floorPrice.currency {
            print("Floor Price: \(price) \(currency)")
        }
        
        // Display marketplace information
        nft.detailedInfo?.marketplaceInfo?.forEach { marketplace in
            if let name = marketplace.marketplaceName {
                print("Listed on: \(name)")
            }
        }
    }
} catch {
    print("Error fetching assets: \(error)")
}

// Example with portfolio value calculation
do {
    let assets = try await portal.getAssets("eip155:1")
    var totalValue = 0.0
    
    // Add native token value
    if let nativeBalance = assets.nativeBalance?.balance,
       let nativePrice = await getCurrentPrice(symbol: assets.nativeBalance?.symbol ?? "ETH"),
       let balance = Double(nativeBalance) {
        totalValue += balance * nativePrice
    }
    
    // Add ERC20 token values
    assets.tokenBalances?.forEach { token in
        if let balance = token.balance,
           let symbol = token.symbol,
           let price = await getCurrentPrice(symbol: symbol),
           let balanceValue = Double(balance) {
            totalValue += balanceValue * price
        }
    }
    
    // Add NFT values using floor prices
    assets.nfts?.forEach { nft in
        if let floorPrice = nft.floorPrice?.price {
            totalValue += floorPrice
        }
    }
    
    print("Total Portfolio Value: $\(String(format: "%.2f", totalValue))")
} catch {
    print("Error calculating portfolio value: \(error)")
}
```

**Implementation Notes**

* All balance fields are optional and should be safely unwrapped
* Token balances are returned as strings to maintain precision
* NFT data includes extensive metadata useful for marketplace integration
