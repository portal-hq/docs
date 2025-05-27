---
description: >-
  Builds a Solana transaction for token transfers, handling both native SOL and
  SPL tokens with required program instructions.
---

# buildSolanaTransaction

**Function Signature**

```swift
public func buildSolanaTransaction(
    chainId: String,
    params: BuildTransactionParam
) async throws -> BuildSolanaTransactionResponse
```

**Parameters**

* `chainId`: The chain identifier in CAIP-2 format (e.g., "solana:5eykt4UsFv8P8NJdTREpY1vzqKqZKvdp" for Solana mainnet)
* `params`: A `BuildTransactionParam` object containing:
  * `to`: Recipient's Solana address
  * `token`: Token identifier ("SOL" for native transfers, mint address for SPL tokens)
  * `amount`: Amount to transfer as a string (in lamports for SOL, raw amount for SPL tokens)

**Returns**

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

**Example Usage**

1. Native SOL Transfer:

```swift
do {
    let params = BuildTransactionParam(
        to: "GxvUWHWwMpep8e6tXvVdaRmwEzKcUjhLYLhpXhS6wQtY",
        token: "SOL",
        amount: "1000000000" // 1 SOL = 1e9 lamports
    )
    
    let response = try await portal.buildSolanaTransaction(
        chainId: "solana:5eykt4UsFv8P8NJdTREpY1vzqKqZKvdp",
        params: params
    )
    
    print("Transaction details:")
    print("Amount: \(response.metadata.amount) SOL")
    print("From: \(response.metadata.fromAddress)")
    print("To: \(response.metadata.toAddress)")
    print("Raw amount: \(response.metadata.rawAmount)")
    
    // Store transaction for signing
    let serializedTx = response.transaction
} catch {
    print("Failed to build SOL transfer: \(error)")
}
```

2. SPL Token Transfer:

```swift
do {
    let params = BuildTransactionParam(
        to: "GxvUWHWwMpep8e6tXvVdaRmwEzKcUjhLYLhpXhS6wQtY",
        token: "EPjFWdd5AufqSSqeM2qN1xzybapC8G4wEGGkZwyTDt1v", // USDC mint address
        amount: "100000000" // 100 USDC (6 decimals)
    )
    
    let response = try await portal.buildSolanaTransaction(
        chainId: "solana:5eykt4UsFv8P8NJdTREpY1vzqKqZKvdp",
        params: params
    )
    
    print("SPL Token Transfer Details:")
    print("Token: \(response.metadata.tokenSymbol ?? "Unknown")")
    print("Token Address: \(response.metadata.tokenAddress ?? "N/A")")
    print("Amount: \(response.metadata.amount)")
    print("Decimals: \(response.metadata.tokenDecimals)")
} catch {
    print("Failed to build token transfer: \(error)")
}
```

**Implementation Notes**

1. Amount Formatting
   *   For native SOL:

       ```swift
       // SOL has 9 decimal places
       "1000000000"  // 1 SOL
       "500000000"   // 0.5 SOL
       ```
   *   For SPL tokens:

       ```swift
       // USDC has 6 decimal places
       "1000000"     // 1 USDC
       "500000"      // 0.5 USDC
       ```
2.  Token Identifiers

    ```swift
    // Native SOL
    token: "SOL"

    // Common SPL Tokens
    token: "EPjFWdd5AufqSSqeM2qN1xzybapC8G4wEGGkZwyTDt1v"  // USDC
    token: "Es9vMFrzaCERmJfrF4H2FYD4KCoNkY11McCe8BenwNYB"  // USDT
    ```
3. Address Validation
   * Solana addresses must be Base58 encoded
   * Must be between 32 and 44 characters in length
   *   Example validation:

       ```swift
       func isValidSolanaAddress(_ address: String) -> Bool {
           return address.count >= 32 && address.count <= 44
       }
       ```
4.  Error Handling

    ```swift
    if let error = response.error {
        // Build-specific error
        throw TransactionError.buildFailed(error)
    }

    // Validate address format
    guard isValidSolanaAddress(response.metadata.toAddress) else {
        throw TransactionError.invalidAddress
    }
    ```

**Related Documentation**

For more information about Solana transaction building, see:

* #### [Submitting a Solana Transaction](https://docs.portalhq.io/getting-started/sdk-quick-starts#submitting-a-solana-transaction) <a href="#submitting-a-solana-transaction" id="submitting-a-solana-transaction"></a>
