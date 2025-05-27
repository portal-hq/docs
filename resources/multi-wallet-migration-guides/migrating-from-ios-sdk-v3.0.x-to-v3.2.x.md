# Migrating from iOS SDK v3.0.x to v3.2.x

When migrating from SDK V3.0.x to V3.2.x if you want to generate a Solana wallet then you have the following two options.

## 1. Generate Solana

You can just generate a Solana wallet and then continue using your normal backup and recover flows like you already did for V3.

```swift
public func generateSolana() async throws -> String {
guard let portal else {
  self.logger.error("PortalWrapper.generateSolana() - Portal not initialized. Please call registerPortal().")
  throw PortalExampleAppError.portalNotInitialized()
}

return try await portal.createSolanaWallet()
}
```

After this, if you call the Portal backup it will backup both Ethereum and Solana wallets. No change is needed from your side.

## 2. Generate and Backup Solana&#x20;

If you have already backed up your Ethereum wallet and now you are interested in generating a Solana wallet but you want to combine the generation and backup steps into one function call then here is how you can do it.

```swift
private func generateSolanaWalletAndBackup(withMethod: BackupMethods) async throws -> (solanaAddress: String, cipherText: String) {

let generateSolanaResult = try await portal.generateSolanaWalletAndBackupShares(.iCloud)

// Store the cipherText on your server
try await storeClientCipherText(generateSolanaResult.cipherText, backupMethod: withMethod.rawValue)

try await generateSolanaResult.storageCallback()

return (generateSolanaResult.solanaAddress, generateSolanaResult.cipherText)
}
```

