---
description: >-
  Retrieves the capabilities and supported features of the current wallet,
  particularly focusing on available services like paymaster support.
---

# getWalletCapabilities

**Function Signature**

```swift
public func getWalletCapabilities() async throws -> WalletCapabilitiesResponse
```

**Parameters**

None - This function doesn't require any parameters.

**Returns**

A `WalletCapabilitiesResponse` dictionary containing:

* Chain identifiers mapped to `WalletCapabilitiesValue` objects
* Each `WalletCapabilitiesValue` includes:
  * `paymasterService`: Information about paymaster support
    * `supported`: Boolean indicating if paymaster service is available

**Example Usage**

<pre class="language-swift"><code class="lang-swift">// Basic capabilities check
do {
    let capabilities = try await portal.getWalletCapabilities()
<strong>    
</strong>    // Check capabilities for Ethereum mainnet
    if let ethCapabilities = capabilities["eip155:1"] {
        if ethCapabilities.paymasterService.supported {
            // Handle supported paymaster
            print("Paymaster service available")
            proceedWithGaslessTransaction()
        } else {
            // Handle unsupported paymaster
            print("Regular transaction required")
            proceedWithRegularTransaction()
        }
    }
    
    // Check capabilities for multiple chains
    let chainIds = [
        "eip155:1",     // Ethereum
        "eip155:137",   // Polygon
        "eip155:56"     // BSC
    ]
    
    for chainId in chainIds {
        if let chainCapabilities = capabilities[chainId] {
            print("\(chainId) Capabilities:")
            print("Paymaster supported: \(chainCapabilities.paymasterService.supported)")
        }
    }
} catch {
    print("Failed to fetch capabilities: \(error)")
}

// Feature-gated transaction handling
do {
    let capabilities = try await portal.getWalletCapabilities()
    
    guard let chainCapabilities = capabilities["eip155:1"],
          chainCapabilities.paymasterService.supported else {
        throw WalletError.paymasterNotSupported
    }
    
    // Proceed with paymaster-dependent transaction
    await processGaslessTransaction()
} catch {
    // Handle unsupported feature
    switch error {
    case WalletError.paymasterNotSupported:
        print("Paymaster service not available")
        await requestGasFromUser()
    default:
        print("Unexpected error: \(error)")
    }
}
</code></pre>

**Implementation Notes**

1. Chain Support
   * Check capabilities per chain before feature-dependent operations
   * Handle missing chain capabilities gracefully
   * Verify feature support before initiating operations
2. Error Handling
   * Handle network failures when fetching capabilities
   * Verify capability existence before access
   * Consider fallback options for unsupported features
3. Capability Changes
   * Capabilities might change during runtime
   * Consider refreshing capabilities periodically
   * Don't cache capabilities for extended periods
