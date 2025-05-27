---
description: >-
  Sends SOL tokens to a specified Solana address. This method constructs and
  sends a Solana transfer transaction.
---

# sendSol

**Function Signature**

```swift
public func sendSol(
    _ lamports: UInt64,
    to: String,
    withChainId chainId: String
) async throws -> String
```

**Parameters**

* `lamports`: Amount of lamports to send (1 SOL = 1,000,000,000 lamports)
* `to`: The recipient's Solana address
* `withChainId`: The CAIP-2 chain identifier (e.g., "solana:5eykt4UsFv8P8NJdTREpY1vzqKqZKvdp" for Solana mainnet)

**Returns**

The transaction hash (signature) as a string

**Throws**

* `MpcError.addressNotFound` if no Solana address is found for the wallet
* `PortalSolError.failedToGetLatestBlockhash` if unable to get the latest blockhash
* `PortalSolError.failedToGetTransactionHash` if transaction signing/sending fails
* Errors from `SolanaSwift.PublicKey` initialization if addresses are invalid

**Example Usage**

```swift
// Basic SOL transfer
do {
    // Send 1 SOL (1 SOL = 1,000,000,000 lamports)
    let txHash = try await portal.sendSol(
        1_000_000_000,
        to: "recipient_solana_address",
        withChainId: "solana:5eykt4UsFv8P8NJdTREpY1vzqKqZKvdp"
    )
    print("Transaction sent: \(txHash)")
} catch {
    print("Error sending SOL: \(error)")
}

// Example with amount calculation
func sendSol(amount: Double, to recipient: String) async throws {
    // Convert SOL to lamports (1 SOL = 1,000,000,000 lamports)
    let lamports = UInt64(amount * 1_000_000_000)
    
    do {
        let txHash = try await portal.sendSol(
            lamports,
            to: recipient,
            withChainId: "solana:5eykt4UsFv8P8NJdTREpY1vzqKqZKvdp"
        )
        print("Sent \(amount) SOL")
        print("Transaction hash: \(txHash)")
    } catch MpcError.addressNotFound {
        print("No Solana wallet found. Please create or recover a wallet first.")
    } catch {
        print("Transaction failed: \(error)")
    }
}

// Complete example with balance check
func sendSolWithBalanceCheck(
    amount: Double,
    to recipient: String
) async throws {
    // Get current balance first
    let assets = try await portal.getAssets("solana:5eykt4UsFv8P8NJdTREpY1vzqKqZKvdp")
    
    guard let nativeBalance = assets.nativeBalance,
          let balanceStr = nativeBalance.balance,
          let balance = Double(balanceStr) else {
        throw CustomError.balanceCheckFailed
    }
    
    // Check if we have enough balance (including some for fees)
    let requiredAmount = amount + 0.001 // Add some for fees
    guard balance >= requiredAmount else {
        throw CustomError.insufficientBalance(
            has: balance,
            needs: requiredAmount
        )
    }
    
    // Convert SOL to lamports
    let lamports = UInt64(amount * 1_000_000_000)
    
    // Send transaction
    let txHash = try await portal.sendSol(
        lamports,
        to: recipient,
        withChainId: "solana:5eykt4UsFv8P8NJdTREpY1vzqKqZKvdp"
    )
    
    print("Transaction successful!")
    print("Amount: \(amount) SOL")
    print("To: \(recipient)")
    print("Hash: \(txHash)")
}

// Example usage in a view controller
class SendSolViewController: UIViewController {
    @IBAction func sendButtonTapped() {
        Task {
            do {
                try await sendSolWithBalanceCheck(
                    amount: 0.1,
                    to: "recipient_address"
                )
                
                await MainActor.run {
                    showSuccess("Transaction sent successfully!")
                }
            } catch let error as CustomError {
                await MainActor.run {
                    handleCustomError(error)
                }
            } catch {
                await MainActor.run {
                    showError("Transaction failed: \(error.localizedDescription)")
                }
            }
        }
    }
}
```

**Implementation Notes**

* Run `generate`or `recover` if needed.
* Amount must be specified in lamports (1 SOL = 1,000,000,000 lamports)
* The transaction is automatically signed and sent to the network
* The fee is paid by the sender's address
* Consider adding extra amount for transaction fees
* The function waits for transaction submission but not confirmation
