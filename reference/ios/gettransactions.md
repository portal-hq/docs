---
description: Retrieves transaction history for the specified blockchain.
---

# getTransactions

**Function Signature**

```swift
public func getTransactions(
    _ chainId: String,
    limit: Int? = nil,
    offset: Int? = nil,
    order: TransactionOrder? = nil
) async throws -> [FetchedTransaction]
```

**Parameters**

* `chainId`: The chain identifier in CAIP-2 format (e.g., "eip155:1" for Ethereum mainnet, "solana:5eykt4UsFv8P8NJdTREpY1vzqKqZKvdp" for Solana mainnet)
* `limit`: Optional maximum number of transactions to return. If nil, returns all transactions.
* `offset`: Optional number of transactions to skip for pagination. If nil, starts from the beginning.
* `order`: Optional `TransactionOrder` to specify the sort order of transactions: `ASC` or `DESC`.

**Returns**

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

**Example Usage**

```swift
// Basic transaction fetch
do {
    let transactions = try await portal.getTransactions(
        "eip155:1",
        limit: 10,
        order: .DESC
    )
    
    for tx in transactions {
        print("\nTransaction: \(tx.hash)")
        print("Block: \(tx.blockNum)")
        print("From: \(tx.from)")
        print("To: \(tx.to)")
        
        if let value = tx.value {
            print("Value: \(value)")
        }
        
        if let asset = tx.asset {
            print("Asset: \(asset)")
        }
        
        print("Time: \(tx.metadata.blockTimestamp)")
        
        // Handle NFT transfers
        if let erc721Id = tx.erc721TokenId {
            print("NFT Token ID: \(erc721Id)")
        }
        
        if let erc1155Data = tx.erc1155Metadata {
            print("ERC1155 Transfers:")
            for metadata in erc1155Data {
                if let tokenId = metadata?.tokenId,
                   let value = metadata?.value {
                    print("Token ID: \(tokenId), Amount: \(value)")
                }
            }
        }
        
        // Contract interaction details
        if let contract = tx.rawContract {
            print("\nContract Details:")
            if let address = contract.address {
                print("Contract Address: \(address)")
            }
            if let value = contract.value {
                print("Contract Value: \(value)")
            }
        }
    }
} catch {
    print("Error fetching transactions: \(error)")
}

// Example with transaction analysis
do {
    let transactions = try await portal.getTransactions("eip155:1")
    
    // Group transactions by category
    let groupedTxs = Dictionary(grouping: transactions) { $0.category }
    
    for (category, txs) in groupedTxs {
        print("\nCategory: \(category)")
        print("Count: \(txs.count)")
        
        // Calculate total value for the category
        let totalValue = txs.compactMap { $0.value }.reduce(0, +)
        print("Total Value: \(totalValue)")
        
        // Count token transfers
        let nftTransfers = txs.filter { $0.erc721TokenId != nil }.count
        print("NFT Transfers: \(nftTransfers)")
        
        let erc1155Transfers = txs.filter { $0.erc1155Metadata != nil }.count
        print("ERC1155 Transfers: \(erc1155Transfers)")
    }
} catch {
    print("Error analyzing transactions: \(error)")
}

// Example with pagination and date filtering
func loadTransactions(fromDate: Date) async {
    let dateFormatter = ISO8601DateFormatter()
    var offset = 0
    let pageSize = 20
    var hasMore = true
    
    while hasMore {
        do {
            let txs = try await portal.getTransactions(
                "eip155:1",
                limit: pageSize,
                offset: offset,
                order: .DESC
            )
            
            // Filter transactions by date
            for tx in txs {
                let txDate = dateFormatter.date(from: tx.metadata.blockTimestamp)
                if let date = txDate, date >= fromDate {
                    print("Processing transaction: \(tx.hash)")
                } else {
                    hasMore = false
                    break
                }
            }
            
            if txs.count < pageSize {
                hasMore = false
            } else {
                offset += pageSize
            }
        } catch {
            print("Error loading transactions: \(error)")
            hasMore = false
        }
    }
}
```

**Implementation Notes**

* Transaction values and timestamps should be carefully parsed
* Consider implementing caching for recent transactions
* The `uniqueId` field can be used for deduplication
* ERC721 and ERC1155 transfers include additional token metadata
* Block timestamps are in ISO format and need proper parsing
