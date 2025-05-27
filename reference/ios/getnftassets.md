---
description: >-
  Retrieves NFT assets for the specified blockchain. This method fetches all
  NFTs (Non-Fungible Tokens) owned by the wallet.
---

# getNftAssets

**Function Signature**

```swift
public func getNftAssets(
    _ chainId: String
) async throws -> [NftAsset]
```

**Parameters**

* `chainId`: The chain identifier in CAIP-2 format (e.g., "eip155:1" for Ethereum mainnet, "solana:5eykt4UsFv8P8NJdTREpY1vzqKqZKvdp" for Solana mainnet)

**Returns**

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

**Example Usage**

```swift
// Basic NFT fetch and display
do {
    let nfts = try await portal.getNftAssets("eip155:1")
    
    print("Your NFT Collection:")
    for nft in nfts {
        print("\nNFT: \(nft.name ?? "Unnamed")")
        if let description = nft.description {
            print("Description: \(description)")
        }
        if let imageURL = nft.imageURL {
            print("Image: \(imageURL)")
        }
        
        // Display rarity information if available
        if let rarity = nft.rarity {
            if let rank = rarity.rank {
                print("Rarity Rank: #\(rank)")
            }
            if let score = rarity.score {
                print("Rarity Score: \(score)")
            }
        }
        
        // Display current market value
        if let floor = nft.floorPrice,
           let price = floor.price,
           let currency = floor.currency {
            print("Floor Price: \(price) \(currency)")
        }
    }
} catch {
    print("Error fetching NFTs: \(error)")
}

// Example with marketplace information
do {
    let nfts = try await portal.getNftAssets("eip155:1")
    
    for nft in nfts {
        print("\nNFT: \(nft.name ?? "Unnamed")")
        
        // Display marketplace listings
        if let marketplaceInfo = nft.detailedInfo?.marketplaceInfo {
            print("Listed on:")
            for listing in marketplaceInfo {
                if let name = listing.marketplaceName,
                   let floorPrice = listing.floorPrice,
                   let price = floorPrice.value,
                   let token = floorPrice.paymentToken {
                    print("- \(name): \(price) \(token.symbol ?? "")")
                    if let usdValue = floorPrice.valueUsdCents {
                        print("  USD Value: $\(Double(usdValue) / 100)")
                    }
                }
            }
        }
        
        // Display media previews
        if let previews = nft.detailedInfo?.mediaInfo?.previews {
            print("\nMedia Previews:")
            if let small = previews.imageSmallURL {
                print("Small: \(small)")
            }
            if let medium = previews.imageMediumURL {
                print("Medium: \(medium)")
            }
            if let large = previews.imageLargeURL {
                print("Large: \(large)")
            }
        }
    }
} catch {
    print("Error processing NFTs: \(error)")
}

// Example with collection statistics
do {
    let nfts = try await portal.getNftAssets("eip155:1")
    
    // Group NFTs by collection
    let collections = Dictionary(grouping: nfts) { 
        $0.collection?.name ?? "Unknown Collection" 
    }
    
    for (collectionName, collectionNFTs) in collections {
        print("\nCollection: \(collectionName)")
        
        if let firstNFT = collectionNFTs.first,
           let collectionInfo = firstNFT.detailedInfo?.extendedCollectionInfo {
            print("Statistics:")
            print("Distinct Owners: \(collectionInfo.distinctOwnerCount ?? 0)")
            print("Distinct NFTs: \(collectionInfo.distinctNftCount ?? 0)")
            print("Total Supply: \(collectionInfo.totalQuantity ?? 0)")
            
            // Display social links
            var socials: [String] = []
            if let twitter = collectionInfo.twitterUsername {
                socials.append("Twitter: @\(twitter)")
            }
            if let discord = collectionInfo.discordURL {
                socials.append("Discord: \(discord)")
            }
            if let instagram = collectionInfo.instagramUsername {
                socials.append("Instagram: @\(instagram)")
            }
            
            if !socials.isEmpty {
                print("\nSocial Links:")
                socials.forEach { print($0) }
            }
        }
        
        // Calculate collection value
        let totalValue = collectionNFTs.compactMap { 
            $0.floorPrice?.price 
        }.reduce(0, +)
        
        if totalValue > 0 {
            print("\nEstimated Collection Value: \(totalValue)")
        }
    }
} catch {
    print("Error analyzing collections: \(error)")
}
```

**Implementation Notes**

* All fields in the NFT response are optional and should be safely unwrapped
* The method provides rich metadata useful for marketplace integration
* Floor prices and sale history can be used for portfolio valuation
* Media previews include multiple resolutions for efficient loading
