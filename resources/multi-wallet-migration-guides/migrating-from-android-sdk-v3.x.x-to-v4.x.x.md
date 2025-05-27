# Migrating from Android SDK v3.x.x to v4.x.x

When migrating from SDK V3.x.x to V4.x.x if you want to generate a Solana wallet then you have the following two options.

## 1. Generate Solana

You can just generate a Solana wallet and then continue using your normal backup and recover flows like you already did for V3.

```kotlin
@OptIn(UpgradeFromV3::class)
private fun generateSolana() {
  lifecycleScope.launch {
    val result = portal.generateSolanaWallet()
    if (result.isSuccess) {
      Timber.d("Generated solana address successfully! ${result.getOrNull()}")
    } else {
      Timber.e("Error generating solana address: ${result.exceptionOrNull()?.message}")
    }
  }
}
```

After this, if you call the Portal backup it will backup both Ethereum and Solana wallets. No change is needed from your side.

## 2. Generate and Backup Solana&#x20;

If you have already backed up your Ethereum wallet and now you are interested in generating a solana wallet but you want to combine the generation and backup steps into one function call then here is how you can do it.

```kotlin
@OptIn(UpgradeFromV3::class)
private fun generateSolanaAndBackup(backupMethod: BackupMethods) {
  lifecycleScope.launch {
    val backupConfigs = BackupConfigs(PasswordStorageConfig(password = "0000"))
    val result = portal.generateSolanaAndBackup(
      backupMethod = backupMethod,
      backupConfigs = backupConfigs
    )
    if (result.isSuccess) {
      val storeResult = storeCipherTextOnBackend(result.getOrNull()!!, backupMethod)
      if (storeResult.isSuccess) {
        Timber.d("Generated solana and backup successful")
      }
    } else {
      Timber.e("Error generating and backuping solana: ${result.exceptionOrNull()?.message}")
    }
  }
}
private suspend fun storeCipherTextOnBackend(cipherText: String, method: BackupMethods): Result<Result<Boolean>> {
  return withContext(Dispatchers.IO) {
    kotlin.runCatching {
      yourApi.storeCipherText(user.exchangeUserId, cipherText, backupMethod = method.method)
      portal.api.storedClientBackupShare(true, backupMethod = method)
    }
  }
}
```

## @OptIn(UpgradeFromV3::class)

FYI, you need to add @OptIn(UpgradeFromV3::class) to your code calling these functions otherwise your editor will report an error. This is to make sure developers are aware that this method should only be called when upgrading from v3 to v4.
