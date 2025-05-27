---
description: >-
  Evaluates a transaction for security and risk assessment, providing both
  validation and simulation results.
---

# evaluateTransaction

**Function Signature**

```swift
public func evaluateTransaction(
    chainId: String,
    transaction: EvaluateTransactionParam,
    operationType: EvaluateTransactionOperationType? = nil
) async throws -> BlockaidValidateTrxRes
```

**Parameters**

1. `chainId`: The chain identifier in CAIP-2 format (e.g., "eip155:1" for Ethereum mainnet)
2. `transaction`: An `EvaluateTransactionParam` object containing:
   * `to`: (Required) Destination address
   * `value`: (Optional) Transaction value in wei
   * `data`: (Optional) Transaction data for contract interactions
   * `maxFeePerGas`: (Optional) Maximum total fee per gas unit
   * `maxPriorityFeePerGas`: (Optional) Maximum priority fee per gas unit
   * `gas`: (Optional) Gas limit
   * `gasPrice`: (Optional) Gas price for legacy transactions
3. `operationType`: Optional `EvaluateTransactionOperationType` with values:
   * `.validation`: Perform security validation only
   * `.simulation`: Perform transaction simulation only
   * `.all`: Perform both validation and simulation&#x20;

**Returns**

A `BlockaidValidateTrxRes` containing:

* `validation`: Security validation results including:
  * `classification`: Type of potential security issue
  * `description`: Detailed description of the validation results
  * `features`: Array of detected security features, each containing:
    * `type`: Feature type
    * `featureId`: Unique identifier
    * `description`: Feature description
    * `address`: Related address if applicable
  * `reason`: Explanation of the validation result
  * `resultType`: Type of result
  * `status`: Validation status
* `simulation`: Transaction simulation results including:
  * `accountAddress`: Address being analyzed
  * `accountSummary`: Summary of account state changes
  * `addressDetails`: Detailed information about involved addresses
  * `assetsDiffs`: Asset balance changes, containing:
    * `asset`: Asset information
    * `in`: Incoming transfers
    * `out`: Outgoing transfers
  * `exposures`: Risk exposure analysis
  * `status`: Simulation status
  * `totalUsdDiff`: Total USD value change
  * `totalUsdExposure`: Total USD value at risk
* `block`: Block number used for evaluation
* `chain`: Chain identifier

**Example Usage**

```swift
swiftCopy// Basic transaction evaluation
do {
    let transaction = EvaluateTransactionParam(
        to: "0x742d35Cc6634C0532925a3b844Bc454e4438f44e",
        value: "1000000000000000000", // 1 ETH
        data: nil,
        maxFeePerGas: "30000000000",
        maxPriorityFeePerGas: "1500000000",
        gas: "21000",
        gasPrice: nil
    )
    
    let evaluation = try await portal.evaluateTransaction(
        chainId: "eip155:1",
        transaction: transaction,
        operationType: .all
    )
    
    // Check validation results
    if let validation = evaluation.validation {
        print("Security Status: \(validation.status)")
        
        if validation.status != "VALIDATED" {
            print("Warning: \(validation.description ?? "Unknown issue")")
            
            // Print detected security features
            validation.features.forEach { feature in
                print("Security Feature: \(feature.type)")
                print("Description: \(feature.description)")
            }
        }
    }
    
    // Check simulation results
    if let simulation = evaluation.simulation {
        print("Simulation Status: \(simulation.status)")
        
        // Print asset changes
        simulation.assetsDiffs.forEach { (token, diffs) in
            print("\nToken: \(token)")
            diffs.forEach { diff in
                print("Incoming transfers:")
                diff.in.forEach { transfer in
                    print(transfer)
                }
                print("Outgoing transfers:")
                diff.out.forEach { transfer in
                    print(transfer)
                }
            }
        }
    }
} catch {
    print("Evaluation failed: \(error)")
}

// Contract interaction evaluation
do {
    let transaction = EvaluateTransactionParam(
        to: "0x742d35Cc6634C0532925a3b844Bc454e4438f44e",
        value: "0",
        data: "0xa9059cbb000000000000000000000000742d35cc6634c0532925a3b844bc454e4438f44e0000000000000000000000000000000000000000000000000de0b6b3a7640000", // ERC20 transfer
        maxFeePerGas: nil,
        maxPriorityFeePerGas: nil,
        gas: "65000",
        gasPrice: "20000000000"
    )
    
    // Validate only
    let validation = try await portal.evaluateTransaction(
        chainId: "eip155:1",
        transaction: transaction,
        operationType: .validation
    )
    
    if let validationResult = validation.validation {
        // Handle potential security issues
        switch validationResult.status {
        case "VALIDATED":
            print("Transaction is safe")
        case "WARNING":
            print("Warning: \(validationResult.description ?? "Unknown warning")")
        case "BLOCKED":
            print("Blocked: \(validationResult.reason ?? "Unknown reason")")
        default:
            print("Unknown status: \(validationResult.status)")
        }
    }
} catch {
    print("Validation failed: \(error)")
}
```

**Implementation Notes**

* Always evaluate transactions involving large amounts or contract interactions
* Consider both validation and simulation results for comprehensive security
* Handle different validation statuses appropriately in your UI
* Gas parameters should be adjusted based on current network conditions
* For contract interactions, carefully validate the data field
