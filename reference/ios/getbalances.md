---
description: >-
  Retrieves token balances for the specified blockchain. This method fetches all
  token balances (native and ERC-20 or SPL) associated with the wallet on the
  specified blockchain network.
---

# getBalances

**Function Signature**

```swift
public func getBalances(
    _ chainId: String
) async throws -> [FetchedBalance]
```

**Parameters**

* `chainId`: The chain identifier in CAIP-2 format (e.g., "eip155:1" for Ethereum mainnet, "solana:5eykt4UsFv8P8NJdTREpY1vzqKqZKvdp" for Solana mainnet)

**Returns**

An array of `FetchedBalance` objects containing token balance information. Each object includes:

* `contractAddress`: The contract address of the token
* `balance`: The balance of the token as a string
* `name`: Optional token name
* `symbol`: Optional token symbol

**Throws**

Various API-related errors if the balance retrieval fails

**Example Usage**

```swift
// Basic balance check
do {
    // Get Ethereum balances
    let ethBalances = try await portal.getBalances("eip155:1")
    for balance in ethBalances {
        print("Token: \(balance.symbol)")
        print("Balance: \(balance.balance)")
        print("Decimals: \(balance.decimals)")
    }
    
    // Get Solana balances
    let solBalances = try await portal.getBalances("solana:5eykt4UsFv8P8NJdTREpY1vzqKqZKvdp")
    for balance in solBalances {
        print("Token: \(balance.symbol)")
        print("Balance: \(balance.balance)")
    }
} catch {
    print("Error fetching balances: \(error)")
}

// Complete example with balance formatting
do {
    let balances = try await portal.getBalances("eip155:1")
    
    // Format and display balances
    for balance in balances {
        // Parse and format the balance string
        if let balanceValue = Double(balance.balance) {
            // Format with appropriate decimal places
            let formatter = NumberFormatter()
            formatter.minimumFractionDigits = 2
            formatter.maximumFractionDigits = 6
            
            if let formatted = formatter.string(from: NSNumber(value: balanceValue)),
               let symbol = balance.symbol {
                print("\(symbol): \(formatted)")
            }
        }
    }
} catch {
    print("Error processing balances: \(error)")
}

// Example with balance monitoring
class BalanceMonitor {
    private var timer: Timer?
    private let portal: Portal
    private let chainId: String
    
    init(portal: Portal, chainId: String) {
        self.portal = portal
        self.chainId = chainId
    }
    
    func startMonitoring() {
        // Check balances every 30 seconds
        timer = Timer.scheduledTimer(withTimeInterval: 30, repeats: true) { [weak self] _ in
            Task {
                await self?.checkBalances()
            }
        }
    }
    
    private func checkBalances() async {
        do {
            let balances = try await portal.getBalances(chainId)
            // Process and display balances
            for balance in balances {
                if balance.balance > 0 {
                    print("New balance for \(balance.symbol): \(balance.balance)")
                }
            }
        } catch {
            print("Balance check failed: \(error)")
        }
    }
    
    func stopMonitoring() {
        timer?.invalidate()
        timer = nil
    }
}
```

**Implementation Notes**

* Token balances are returned in their smallest unit (e.g., wei for ETH)
* Need to consider token decimals when displaying balances
* Method returns all tokens with non-zero balances
* Consider rate limiting when polling balances frequently
* Currently, EVM and Solana chains are supported by this method.

**Usage in Portfolio Tracking**

```swift
func calculatePortfolioValue(balances: [FetchedBalance], prices: [String: Double]) -> Double {
    return balances.reduce(0) { total, balance in
        guard 
            let symbol = balance.symbol,
            let balanceValue = Double(balance.balance)
        else { return total }
        
        return total + (balanceValue * (prices[symbol] ?? 0))
    }
}

// Usage example
do {
    let balances = try await portal.getBalances("eip155:1")
    let prices = try await fetchCurrentPrices() // Your price fetching implementation
    let portfolioValue = calculatePortfolioValue(balances: balances, prices: prices)
    print("Total portfolio value: $\(portfolioValue)")
} catch {
    print("Error calculating portfolio value: \(error)")
}
```
