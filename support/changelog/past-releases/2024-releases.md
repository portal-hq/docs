---
noRobotsIndex: true
layout:
  title:
    visible: true
  description:
    visible: true
  tableOfContents:
    visible: true
  outline:
    visible: true
  pagination:
    visible: true
---

# 2024 Releases

<details>

<summary>December 2024</summary>

### Dec 6, 2024

#### Android SDK v5.0.0

* Adds support for AppData folder backups with GDrive
* Adds support for wallet customization in the dapps
* Removes multiple deprecated constructors, fields and methods from main user facing classes
* Fixes incorrect signature being sent to Dapp browser
* Changed `mpc.backup()`method, it now returns a object with key and a callback that you the consumers must call to complete the backup
* Adds unit tests for GDrive related classes

### Dec 5, 2024

#### iOS SDK  v4.2.0

* Adds support for AppData folder backups with GDrive

</details>

<details>

<summary><strong>November 2024</strong></summary>

### Nov 20, 2024&#x20;

#### iOS SDK v4.1.0

* Changes `createWallet` to return non-optional eth and Solana addresses & recover wallet non-optional ETH address EIP-6963 Support in the Dapp Browser&#x20;
* Adds `wallet_getCapabilities` method to the provider&#x20;
* Throws errors for `isWalletOnDevice` instead of returning false for errors

#### Web SDK v3.3.1

* Adds `chainId` as optional field to Portal Class.
* Fixes compatibility with Lido sdk provider

### Nov 8, 2024

#### Android SDK v3.3.1

* Removed the ability for the Javascript to continue running after the WebView is closed

### Nov 4, 2024

#### Web SDK v3.3.0

* Added `evaluateTransaction` method for both EVM and Solana&#x20;
* Added `buildTransaction` method for both EVM and Solana&#x20;
* Added `getNFTAssets` method to get NFT assets by `chainId`
* Added `getAssets` method to get assets by `chainId`
* Added `isWalletBackedUp` method to check if the user has backed up their wallet
* Added `isWalletRecoverable` method to check if the user can recover their wallet
* Added `availableRecoveryMethods`method that returns a list of available recovery methods based on the user's backups&#x20;
* Deprecated `personalSign`, `getBalances`, `getNFTs`, and `simulateTransaction`

### Nov 1, 2024

#### iOS SDK v4.0.3

* Add `portal.getRpcUrl(forChainId)`

</details>

<details>

<summary>October 2024</summary>

### Oct 31, 2024

#### iOS SDK v4.0.2

* Add `buildTransaction` methods for ETH & SOL
* Add `getNFTAssets` method to get NFT assets by `chainId`
* Add `getAssets` method to get assets by `chainId`
* Enhance the errors to have a proper `localizedDescription`
* Fix for the GET Transactions API response.

#### Android SDK v4.2.0

* Add `buildTransaction` methods for ETH & SOL
* Add `getNFTAssets` method to get NFT assets by `chainId`
* Add `getAssets` method to get assets by `chainId`
* Remove SolanKt library from Portal SDK

### Oct 22, 2024

#### Android SDK v4.1.7

* Added `isWebSocketAllowed()` function

### Oct 16, 2024

#### iOS SDK v4.0.1

* Fixes cross-SDK recovery

#### Android SDK v4.1.6

* Fixes cross-SDK recovery

#### Web SDK v3.2.3

* Fixes cross-SDK recovery

#### React Native SDK v4.0.3

* Fixes cross-SDK recovery

### Oct 15, 2024

#### Web SDK v3.2.2

* Fixes some typing issues with the `chainId` when using the Portal Swaps implementation.

</details>

<details>

<summary>September 2024</summary>

### Sept 26, 2024

#### React Native SDK v4.0.2

* Android support is available again

### Sept 16, 2024

#### iOS SDK v4.0.0

* Breaking change to `PortalKeychain.metadata`to be instance member & thread-safe.
* Fixes the `Portal.isWalletBackedUp()` bug.
* Breaking change to handle the unauthorized session to throw `PortalRequestsError.unauthorized` instead of a generic error

### Sept 11, 2024

#### Android SDK v4.1.5

* Fix Gdrive backup current activity null bug

### Sept 9, 2024

#### Android SDK v4.1.4

* Moves the `PasskeyStorage` and `GoogleStorage` class initializations out of the Portal initialization path.&#x20;
  * These Storage adapters will now be initialized when their respective `portal.configureXxxxxxxxStorage()` functions are invoked

### Sept 6, 2024

#### Web SDK v3.2.1

* Added support for `rawSign`

</details>

<details>

<summary>August 2024</summary>

### Aug 30, 2024

#### iOS SDK v3.2.3

* PortalConnect Update&#x20;
  * Adds `emitGetSessionRequest()` to Connect class
  * Allows customers to retrieve previous session requests from dApps.&#x20;
  * This is primarily meant to be used in conjunction with mobile deeplinking to properly respond to a session request. Read more on [Swift](https://docs.portalhq.io/guides/native-ios/connect-with-walletconnect#retrieve-session-request) and [Kotlin](https://docs.portalhq.io/guides/native-android/connect-with-walletconnect#retrieve-session-request).&#x20;
* Adds thread safe access to client&#x20;
* Adds evaluate transaction function using evaluate endpoint&#x20;
  * Refer to the [Portal API Documentation](https://docs.portalhq.io/reference/client-api/v3-endpoints#evaluate-a-transaction) for more details&#x20;
* Adds `sol_getTransaction` RPC request

### Aug 29, 2024

#### Android SDK v4.1.3

* PortalConnect Update&#x20;
  * Adds `emitGetSessionRequest()` to Connect class
  * Allows customers to retrieve previous session requests from dApps.&#x20;
  * This is primarily meant to be used in conjunction with mobile deeplinking to properly respond to a session request. Read more on [Swift](https://docs.portalhq.io/guides/native-ios/connect-with-walletconnect#retrieve-session-request) and [Kotlin](https://docs.portalhq.io/guides/native-android/connect-with-walletconnect#retrieve-session-request).&#x20;
* Fix `chainId` in signing request callback
* Fix `relyingPartyId` default overwrites in passkey backup
* Adds `PortalHttpUnauthorizedException` to throw when user token expires or is invalid

### Aug 23, 2024

#### React Native SDK v4.0.1

* Fixes an issue when signing after recovering

### Aug 15, 2024

#### iOS SDK v3.2.2

* Fix eject error handling&#x20;
* Default rpc urls&#x20;
* Webview made public

#### Android SDK v3.3.0

* Add `canUsePasskeys()` helper method

### Aug 14, 2024

#### Android SDK v4.1.2

* Add default value for RPC Config in Portal constructor&#x20;
* Add `canUsePasskeys()` helper method to Portal class
* Add wallet lifecycle methods ([docs](https://docs.portalhq.io/guides/android/manage-wallet-lifecycle-states))&#x20;
* Disable logs in production builds with R8/proguard enabled apps.

### Aug 12, 2024

#### React Native SDK v4.1.0

* Add Backup with Portal support

### Aug 5, 2024

#### iOS SDK v3.2.1

* Hot-Fix: recovering bug after solana generate with no backup.

#### Android SDK v4.1.1

* Fixes solana share is erased when recovering

### Aug 1, 2024

#### Android SDK v3.2.8

* GDrive upgrades and bug fixes

</details>

<details>

<summary>July 2024</summary>

### Jul 31, 2024

#### iOS SDK v3.2.0

* Solana migration support for pre-multi wallet sdk versions ([docs](https://docs.portalhq.io/resources/multi-wallet-migration-guides))&#x20;
* Solana Eject ([iOS docs](https://docs.portalhq.io/guides/ios/eject-a-wallet))

#### Android SDK v4.1.0

* Solana migration support for pre-multi wallet sdk versions ([docs](https://docs.portalhq.io/resources/multi-wallet-migration-guides))&#x20;
* Solana Eject ([Android docs](https://docs.portalhq.io/guides/android/eject-a-wallet))

### Jul 30, 2024

#### iOS SDK v3.1.12

* Includes `chainId` on the payload provided to all `Provider.request()` approval hooks

### Jul 29, 2024

#### iOS SDK v3.1.11

* Resolves issues with `featureFlag` propagation during backup and recovery

### Jul 26, 2024

#### iOS SDK v3.1.10

* Resolves issues with `continuation.resume()` falling through

### Jul 24, 2024

#### Web SDK v3.2.0

* Adds support for Backup with Portal

### Jul 22, 2024

#### iOS SDK v3.1.8

* Resolves an issue with signing share lookup when upgrading from 3.0.x to 3.1.x

### Jul 17, 2024

#### React Native SDK v4.0.0

* New Features:&#x20;
  * Solana Support! (details [here](https://docs.portalhq.io/guides/react-native/sign-a-transaction#signing-a-solana-transaction))&#x20;
  * Eject for `ed25519` (Solana) (details [here](https://docs.portalhq.io/guides/react-native/eject-a-wallet))&#x20;
  * Raw Signature (details [here](https://docs.portalhq.io/guides/react-native/sign-a-transaction#raw-sign))&#x20;
  * Chain Agnostic Provider pass any chain the `portal.request` method to interact with that chain without worrying about switching the chain on the portal object.&#x20;
* Breaking changes:
  * Generate returns addresses (`eip155` and `solana`)&#x20;
  * Portal connect instance manages its own `chainId`
  * Gateway config uses new network format of `Namespace:reference` ie. eip155:1 for mainnet&#x20;
* Note: RN version 4.0.0 currently only supports iOS. Android support will follow along soon!

### Jul 11, 2024

#### iOS SDK v3.1.7

* PortalConnect Update&#x20;
  * Adds `emitGetSessionRequest()` to Connect class
  * Allows customers to retrieve previous session requests from dApps.&#x20;
  * This is primarily meant to be used in conjunction with mobile deeplinking to properly respond to a session request. Read more on [Swift](https://docs.portalhq.io/guides/native-ios/connect-with-walletconnect#retrieve-session-request) and [Kotlin](https://docs.portalhq.io/guides/native-android/connect-with-walletconnect#retrieve-session-request).&#x20;
* Tracking SDK version on requests to Portal

#### iOS SDK v3.0.13

* PortalConnect Update&#x20;
  * Adds `emitGetSessionRequest()` to Connect class
  * Allows customers to retrieve previous session requests from dApps.&#x20;
  * This is primarily meant to be used in conjunction with mobile deeplinking to properly respond to a session request. Read more on [Swift](https://docs.portalhq.io/guides/native-ios/connect-with-walletconnect#retrieve-session-request) and [Kotlin](https://docs.portalhq.io/guides/native-android/connect-with-walletconnect#retrieve-session-request).&#x20;
* Tracking SDK version on requests to Portal

#### Android SDK v4.0.3

* PortalConnect Update&#x20;
  * Adds `emitGetSessionRequest()` to Connect class
  * Allows customers to retrieve previous session requests from dApps.&#x20;
  * This is primarily meant to be used in conjunction with mobile deeplinking to properly respond to a session request. Read more on [Swift](https://docs.portalhq.io/guides/native-ios/connect-with-walletconnect#retrieve-session-request) and [Kotlin](https://docs.portalhq.io/guides/native-android/connect-with-walletconnect#retrieve-session-request).&#x20;

- Tracking SDK version on requests to Portal
- Fixed GDrive bug&#x20;
  * No longer creates an empty folder if a user attempts to recover with the incorrect gmail account.

### Jul 8, 2024

#### Android SDK v3.2.6

* `PortalWebView` is now a proper View and can be used and controlled independently without using a fragment&#x20;
* `PortalWebViewFragment` now uses this new `PortalWebView` fragment while providing the same old functionality so no changes are needed on consumers' side.



</details>

<details>

<summary>June 2024</summary>

### Jun 26, 2024

#### Android SDK v4.0.2

* Stabilize multi-wallet support
* Add send Solana helper function SDK API requests now also send the exact SDK version
* SDK now fully supports minification and have all the consumer proguard rules inside it.
  * Consumers of the SDK don't need to worry about Portal SDK proguard/R8 rules.
* Remove Context from the Keychain constructor Multiple SDK bug fixes

### Jun 25, 2024

#### Android SDK v3.2.5

* Fix R8 Build failure&#x20;
* Add more missing Progaurd rules

### Jun 21, 2024

#### Web SDK v3.1.0

* Removes unnecessary `featureFlags.optimzed` flag&#x20;
* Update error handling&#x20;
* Captures the SDK version for server-side logging during MPC operations

### Jun 18, 2024

#### iOS SDK v3.1.6

* Updated `swiftformat` rules&#x20;
* Fixed crash while setting `self.events[event] = event`
* Added test case for `PortalConnect.swift`
* Created new class for `EventBus` unit test&#x20;
* Removed apiKey constraint&#x20;
* Added support for updating RPC URL on chain changed

#### iOS SDK v3.0.12

* Resolves once listener bug&#x20;
* Resolves disconnect event emit bug&#x20;
* Adds check to params for requests&#x20;
* `apiKey` is now a private variable&#x20;
* Adds count check to params for requests

### Jun 17, 2024

#### Android SDK v3.2.2

* Publish v3 version of the sdk to maven&#x20;
* Bump maven version name&#x20;
* Make `PortalErrorCodeTypes` public&#x20;
* Fixes codes is not a class member field so it is not accessible once the class has been initialized

### Jun 12, 2024

#### iOS SDK v3.0.11

* Resolves once listener bug
* Resolves disconnect event emit bug

#### iOS SDK v3.1.5

* Resolves once listener bug
* Resolves disconnect event emit bug
* Adds check to params for requests
* `apiKey` is now a private variable
* Adds count check to params for requests

### Jun 10, 2024

#### Android SDK v4.0.1

* GDrive share recovery issue due to cached access token&#x20;
* Fix broken auto-approve feature

</details>

<details>

<summary>May 2024</summary>

### May 29, 2024

#### Web SDK v3.0.2

* Fixes issues with the Google auth prompt not firing properly

### May 28, 2024

#### Web SDK v3.0.1

* Replaces our Google oAuth implementation with the GSI library to address Google Drive permissions issues.

### May 25, 2024

#### Android SDK v3.2.0

* Added `ActivityResultManager` internally to the sdk&#x20;
  * This is a breaking change that removes the need for you to call our sdk in the `onActivityResult` to receive results from Gdrive backup.&#x20;
  * This also removes the need to pass the context into the GoogleStorage and PasskeyStorage objects.&#x20;
  * This is all utilizing `ActivityLifeCycleCallbacks`
* Fixed a crash in `storedClientBackupShare`&#x20;
* Improved `PasskeyStorage`&#x20;
* Removed callbacks and added proper coroutines&#x20;
* Added a field `preferImmediatelyAvailableCredentials` to allow for configuring this option
* Added a check to error if the android device does not have the required android API version to run passkeys

### May 18, 2024

#### iOS SDK v3.1.4

* Solana Support&#x20;
  * Adds a helper `sendSol` function&#x20;
  * Adds a `createSolanaRequest` function&#x20;
  * Adds all of the Solana RPC methods to our Provider&#x20;
  * Adds the following Solana signing methods&#x20;
    * `sol_signAndConfirmTransaction`
    * `` sol_signAndSendTransactio` ``
    * `sol_signMessage`&#x20;
    * `sol_signTransaction`
* Increase the min deployment version to iOS 15&#x20;
* Adds the `solanaSwift` library as a dependency

#### Android SDK v3.0.9

* Add missing consumer proguard rules
* Update SDK to use latest Android libraries
* Fix a passkey flow crash

### May 15, 2024

#### Web SDK v3.0.0

This release includes major changes to our Web SDK, and specifically it includes support for Solana!&#x20;

We also completely rewrote the documentation for the Web SDK, so we recommend reviewing them from the beginning as you upgrade to 3.0.0!&#x20;

* Added `portal.sendSol`
* Renames `portal.ejectPrivateKey`to `portal.eject`, also fixes eject functionality `new Portal(config)`, config excludes `chainId`, changes property `gatewayConfigto rpcConfig‍`
* Added `portal.getEip155Address‍`
* Added `portal.getSolanaAddress‍`
* Added `portal.checkSharesOnDevice‍`
* Added `portal.doesWalletExist‍`
* Added `portal.isWalletOnDevice‍`
* `portal.ethEstimateGas` now has `chainId` as the first argument, added deprecation warning
* `portal.ethGasPrice` now has `chainId` as the first argument, added deprecation warning
* `portal.ethGetBalance` now has `chainId` as the first argument, added deprecation warning
* `portal.ethSendTransaction` now has `chainId` as the first argument, added deprecation warning
* `portal.ethSign` now has `chainId` as the first argument, added deprecation warning
* `portal.ethSignTransaction` now has `chainId` as the first argument, added deprecation warning
* `portal.ethSignTypedData` now has `chainId` as the first argument, added deprecation warning
* _`portal.ethSignTypedDataV3`_ now has `chainId` as the first argument, added deprecation warning
* `portal.ethSignTypedDataV4` now has `chainId` as the first argument, added deprecation warning
* _`portal.personalSign`_ now has `chainId` as the first argument, added deprecation warning
* `portal.getBalances` now has `chainId` _&#x61;_&#x73; the first argument
* Removed `portal.getBackupShareMetadata` (in favor of `portal.getClient`)
* Removed `portal.getSigningShareMetadata` (in favor of `portal.getClient`)
* `portal.getNFTs` now has `chainId` as the first argument
* `portal.getTransactions` now has `chainId` as the first argument
* `portal.simulateTransaction` now has `chainId` as the first argument
* `portal.getQuote` now has `chainId` as required
* `portal.getSources` now has `chainId` as required
* `portal.getRpcUrl` now has `chainId` as an optional argument

### May 10, 2024

#### &#x20;Android SDK v.3.0.8

* Fix Json parsing type issues for HTTP requests&#x20;
* Made gas field optional/nullable in transaction params

### May 3, 2024

#### Web SDK v2.0.1

* Changed the EMM endpoints for v1 to v1/sdk

</details>

<details>

<summary>April 2024</summary>

### Apr 26, 2024

#### Web SDK v2.0.0

* Adds multi-wallet support (a prerequisite for Solana support!)&#x20;
* `portal.backup()` is now returning a BackupResponse type, so this release includes a breaking change.

### Apr 21, 2024

#### React Native SDK v3.0.6

* Passkey + Enclave Storage&#x20;
  * Check out the docs to learn more.&#x20;
* Public NPM packages&#x20;
  * You no longer need an npm token to use the react native sdk 🙂

### Apr 12, 2024

#### iOS SDK v3.0.7

* Fixes WebView's `wallet_switchEthereumChain` handler for Base (and any chainId greater than 255).

### Apr 11, 2024

#### iOS SDK v3.0.6

* Adds support for development in Xcode 15.3



</details>

<details>

<summary>March 2024</summary>

### Mar 22, 2024

#### Android SDK v3.0.7

* Adds optional field to Google Storage class: `signoutAfterUse`&#x20;
  * This will sign the user out of their google account that was used to run backup or recover. It requires the user to select the google account each time they run google backup or recover.

### Mar 15, 2024

#### Android SDK v3.0.6

* Adds support for Passkeys backup&#x20;
  * You can read more about setting up Passkey backups in the Android Backup Methods guide in our docs&#x20;
* Updates to README

### Mar 8, 2024

#### iOS SDK v3.0.5

* Removes `portal.legacyRecover` (deprecated)
* Removes WalletConnect v1 implementation (deprecated)

#### Android SDK v3.0.5

* Removes `portal.legacyRecover` (deprecated)&#x20;
* Removes WalletConnect v1 implementation (deprecated)

#### Web SDK v1.0.4

* Updated binary

#### React Native SDK v3.0.4

* Removes `portal.legacyRecover` (deprecated)

### Mar 7, 2024

#### iOS SDK v3.0.4

* Fixes a bug that prompted users for permission to access the local network.

</details>

<details>

<summary>February 2024</summary>

### Feb 29, 2024

#### Web SDK v1.0.3

* Added the `featureflag.isMultiBackupEnabled` if present to `storedClientBackupShare` and `storedClientBackupShareKey` requests

#### React Native SDK v3.0.3

* Adds `isMultiBackupEnabled` to featureFlags and attaches it to metadata when calling MPC Client binary methods.&#x20;
* MPC Binary Release for version: 4.0.28

### Feb 23, 2024

#### Web SDK v1.0.2

* Multi-backup is here in all SDKs! Read how to get started using it with the [Multi-Backup Migration Guide](https://docs.portalhq.io/resources/multi-backup-migration-guide).

#### React Native SDK v3.0.2

* Multi-backup is here in all SDKs! Read how to get started using it with the [Multi-Backup Migration Guide](https://docs.portalhq.io/resources/multi-backup-migration-guide).

### Feb 17, 2024

#### iOS SDK v3.0.1

* Added `.getBackupShareMetadata()`to get backup shares' details for a user.&#x20;
* Added `.getSigningShareMetadata()`to get signing shares' details for a user.
* Configurable relying party for Passkeys&#x20;
  * Set your own domain as the relying party for your passkeys!

#### Android SDK v3.0.1

* Added `.getBackupShareMetadata()` to get backup shares' details for a user.&#x20;
* Added `.getSigningShareMetadata()` to get signing shares' details for a user.

### Feb 16, 2024

#### Web SDK v1.0.1

* Added `.getBackupShareMetadata()` to get backup shares' details for a user.&#x20;
* Added `.getSigningShareMetadata()` to get signing shares' details for a user.&#x20;
* 3X Performance optimizations for all MPC operations!!!&#x20;
* Adds support for multi-backup. (Details to follow next week!)&#x20;
* Configurable relying party for Passkeys Resolves session management for local development

#### React Native SDK v3.0.1

* Added `.getBackupShareMetadata()` to get backup shares' details for a user.&#x20;
* Added `.getSigningShareMetadata()` to get signing shares' details for a user.

### Feb 12, 2024

#### React Native SDK v3.0.0

* Adds support for multi-backup. (Details to follow later this week!)
* Updated staging domains for example app.

### Feb 10, 2024

#### iOS SDK v3.0.0

* Adds support for multi-backup.
* Updated staging domains for example app.

#### Android SDK v3.0.0

* Adds support for multi-backup.&#x20;
* Updated staging domains for example app.&#x20;
* Fix for Google Drive permission cancellation during backup.

### Feb 2, 2024

#### iOS SDK v2.1.2

* Support for client attestation when using the `optimized: true` feature flag&#x20;
* Support for changing chains on a dApp in the WebView from the app Provider&#x20;
  * This allows for the `setChainId` or `updateChain` functions in your app to also control the chain of a `PortalWebView`&#x20;
* Addition of eject support to eject an MPC wallet's private key

#### Web SDK v0.1.2

* Support for client attestation when using the `optimized: true` feature flag

#### React Native SDK v2.1.2

* Support for client attestation when using the `optimized: true` feature flag&#x20;
* Support for changing chains on a dApp in the WebView from the app Provider&#x20;
  * This allows for the `setChainId` or `updateChain` functions in your app to also control the chain of a `PortalWebView`&#x20;

</details>

<details>

<summary>January 2024</summary>

### Jan 27, 2024

#### iOS SDK v2.1.1

* Adds support for switching chains in the `PortalWebView.`&#x20;
* Fixes a bug with transaction rejection.

#### Android SDK v2.1.1

* Adds support for switching chains in the `PortalWebView.`

### Jan 26, 2024

#### Web SDK v0.1.1

* This release includes access to the Passkeys via our Web SDK!&#x20;
* Fixes bug with webview switch chain Passkey + Enclave Storage (Alpha) check out the docs to learn more

#### React Native SDK v2.1.1

* Fixes bug with webview switch chain support

### Jan 20, 2024

#### iOS SDK v2.1.0

* Eject feature available via the `portal.mpc.ejectPrivateKey` function.&#x20;
* Fixes bug with request approvals and rejections the `PortalWebView`

#### Android SDK v2.1.0

* Eject feature available via the `portal.mpc.ejectPrivateKey` function.&#x20;

### Jan 19, 2024

#### Web SDK v0.1.0

This release includes access to the Eject Feature via our SDKs!&#x20;

* Eject enables users to choose to migrate from MPC (with two key shares) to their own non-custodial wallet with a single private key. You can learn more about the Eject Feature on our docs.&#x20;
* Eject feature available via the `portal.ejectPrivateKey` function

#### React Native SDK v2.1.0

This release includes access to the Eject Feature via our SDKs!&#x20;

* Eject enables users to choose to migrate from MPC (with two key shares) to their own non-custodial wallet with a single private key. You can learn more about the Eject Feature on our docs.&#x20;
* Eject feature available via the `portal.ejectPrivateKey` function

### Jan 13, 2024

#### iOS SDK v2.0.18

* Fixes auto-connect to Aave in the `PortalWebView`.&#x20;
* Adds other `PortalWebView`improvements:&#x20;
  * Adds support for opening new tabs in the same view.&#x20;
  * Fixes a force unwrap bug.&#x20;
  * Improves script inject point for more reliable auto-connect.&#x20;
  * Removes session persistence between sessions by default, but can be configured to be enabled.

### Jan 12, 2024

#### React Native SDK v2.0.14

* Fixes auto-connect to Aave in the `PortalWebView`.

### Jan 5, 2024

#### React Native SDK v2.0.13

* Added convenience methods for `ethGetBalance`, `ethEstimateGas`, and `ethGasPrice`.&#x20;
* Updated example apps to use the Sepolia chain instead of Goerli by default.

### Jan 4, 2024

#### iOS SDK v2.0.16

* Passkey + Enclave Storage (Alpha)

</details>
