---
description: >-
  Builds an EIP-155 compliant transaction for Ethereum-compatible chains. This
  method constructs a transaction object that includes replay protection as
  specified in EIP-155.
---

# buildEip155Transaction

**Function Signature**

```swift
swiftCopypublic func buildEip155Transaction(
    chainId: String,
    params: BuildTransactionParam
) async throws -> BuildEip115TransactionResponse
```

**Parameters**

1. `chainId`: The chain identifier in CAIP-2 format (e.g., "eip155:1" for Ethereum mainnet)
2. `params`: A `BuildTransactionParam` object containing:
   * `to`: Recipient's address
   * `token`: Token identifier or contract address
   * `amount`: Amount to transfer as a string

**Returns**

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

**Example Usage**

```swift
// Native ETH transfer
do {
    let params = BuildTransactionParam(
        to: "0x1234567890123456789012345678901234567890",
        token: "ETH",
        amount: "1000000000000000000" // 1 ETH
    )
    
    let response = try await portal.buildEip155Transaction(
        chainId: "eip155:1",
        params: params
    )
    
    print("Transaction details:")
    print("From: \(response.transaction.from)")
    print("To: \(response.transaction.to)")
    print("Value: \(response.transaction.value ?? "0")")
    
    print("\nMetadata:")
    print("Amount: \(response.metadata.amount)")
    print("Token Symbol: \(response.metadata.tokenSymbol ?? "ETH")")
    
    if let error = response.error {
        print("Warning: \(error)")
    }
} catch {
    print("Failed to build transaction: \(error)")
}

// ERC20 token transfer
do {
    // Example: Transfer 100 USDC
    let params = BuildTransactionParam(
        to: "0x1234567890123456789012345678901234567890",
        token: "0xA0b86991c6218b36c1d19D4a2e9Eb0cE3606eB48", // USDC contract address
        amount: "100000000" // 100 USDC (6 decimals)
    )
    
    let response = try await portal.buildEip155Transaction(
        chainId: "eip155:1",
        params: params
    )
    
    // Verify the built transaction
    print("Token Transfer Details:")
    print("Contract Address: \(response.metadata.tokenAddress ?? "N/A")")
    print("Amount: \(response.metadata.amount) \(response.metadata.tokenSymbol ?? "tokens")")
    print("From: \(response.metadata.fromAddress)")
    print("To: \(response.metadata.toAddress)")
    print("Raw Amount: \(response.metadata.rawAmount)")
} catch {
    print("Failed to build token transfer: \(error)")
}

// Example with transaction validation
func buildAndValidateTransaction(
    to: String,
    token: String,
    amount: String
) async throws {
    // Build the transaction
    let params = BuildTransactionParam(
        to: to,
        token: token,
        amount: amount
    )
    
    let response = try await portal.buildEip155Transaction(
        chainId: "eip155:1",
        params: params
    )
    
    // Validate the response
    guard response.error == nil else {
        throw TransactionError.buildFailed(response.error!)
    }
    
    // Validate addresses
    guard response.metadata.fromAddress.starts(with: "0x"),
          response.metadata.toAddress.starts(with: "0x") else {
        throw TransactionError.invalidAddress
    }
    
    // Validate amount
    guard let rawAmount = Double(response.metadata.rawAmount),
          rawAmount > 0 else {
        throw TransactionError.invalidAmount
    }
    
    print("Transaction validated successfully")
    print("Ready to send: \(response.metadata.amount) \(response.metadata.tokenSymbol ?? "tokens")")
}
```

**Implementation Notes**

1. Transaction Types:
   * For native ETH transfers, use "ETH" as the token identifier
   * For ERC20 transfers, use the token's contract address as the token identifier
   * Amount should be provided in the token's smallest unit (wei for ETH, token-specific decimal places for ERC20)
2. Token Handling:
   * Native ETH is identified by "ETH" token value
   * ERC20 tokens are identified by their contract address
   * Amount decimal places must match the token's decimal configuration
3. Amount Formats:
   * ETH amounts should be in wei (1 ETH = 1e18 wei)
   * Token amounts should account for the token's decimal places
   * All amounts should be provided as strings to preserve precision
4. Error Handling:
   * Check response.error for build-specific issues
   * Validate addresses are in correct Ethereum format
   * Verify amount format matches token decimals
   * Handle network-specific errors during transaction building

**Related Documentation**

For more information about transaction building, see:

* [Submitting an EVM Transaction](https://docs.portalhq.io/getting-started/sdk-quick-starts#submitting-an-evm-transaction)
