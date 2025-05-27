# 2023 Releases

<details>

<summary>December 2023</summary>

### Dec 11, 2023

#### Web SDK v0.0.8

* Adds typed imports to unblock Alchemy.

### Dec 9, 2023

#### iOS SDK v2.0.15

* Added convenience methods for `ethGetBalance`, `ethEstimateGas`, and `ethGasPrice`.&#x20;
* Updated example apps to use the Sepolia chain instead of Goerli by default.

#### Android SDK v2.0.13

* Added convenience methods for `ethGetBalance`, `ethEstimateGas`, and `ethGasPrice`.&#x20;
* Updated example apps to use the Sepolia chain instead of Goerli by default.

### Dec 8, 2023

#### Web SDK v0.0.7

* Added convenience methods for `ethGetBalance`, `ethEstimateGas`, and `ethGasPrice`.&#x20;
* Updated example apps to use the Sepolia chain instead of Goerli by default.

### Dec 1, 2023

#### iOS SDK v2.0.14

* Support for page loading callbacks in WebView.

#### Android SDK v2.0.12

* Support for page loading callbacks in WebView.

#### React Native SDK v2.0.12

* Security improvements Improved WebView dApp support for auto-connect

</details>

<details>

<summary>November 2023</summary>

### Nov 21, 2023

#### Web SDK v0.0.6

* Fixes opposite check on chainIds in gateway config to prevent false positive errors.

### Nov 18, 2023

#### iOS SDK v2.0.13

* Adds support for setting a custom nonce to iOS.&#x20;
* Makes the `PortalWebView.webView` and `PortalWebView.webViewContentIsLoaded` properties public in iOS.

#### Android SDK v2.0.11

* Adds support for setting a custom nonce to Android.&#x20;
* Makes the `PortalWebView.webView` and `PortalWebView.webViewContentIsLoaded` properties public in Android.

### Nov 17, 2023

#### React Native SDK v2.0.11

* Adds support for setting a custom nonce to iOS and Android.&#x20;
* Makes the `PortalWebView.webView` and `PortalWebView.webViewContentIsLoaded`properties public in iOS and Android.
* Updates dependencies in React Native.

### Nov 10, 2023

#### iOS SDK v2.0.12

Added auto-connect functionality for the following dApps in the mobile WebViews:&#x20;

* Aave (https://app.aave.com/)&#x20;
* Arbitrum Bridge (https://bridge.arbitrum.io/)&#x20;
* Compound Finance (https://app.compound.finance/?market=usdc-mainnet)&#x20;
* Convex Finance (https://www.convexfinance.com/stake)&#x20;
* MakerDAO (https://app.spark.fi/)&#x20;
* Optimism Gateway (https://app.optimism.io/bridge/deposit)&#x20;
* Rarible (https://rarible.com/)&#x20;
* RocketPool (https://stake.rocketpool.net/)&#x20;
* Uniswap (https://app.uniswap.org/#/swap)&#x20;
* summer.fi (https://summer.fi/)

#### Android SDK v2.0.10

Added auto-connect functionality for the following dApps in the mobile WebViews:&#x20;

* Aave (https://app.aave.com/)&#x20;
* Arbitrum Bridge (https://bridge.arbitrum.io/)&#x20;
* Compound Finance (https://app.compound.finance/?market=usdc-mainnet)&#x20;
* Convex Finance (https://www.convexfinance.com/stake)&#x20;
* MakerDAO (https://app.spark.fi/)&#x20;
* Optimism Gateway (https://app.optimism.io/bridge/deposit)&#x20;
* Rarible (https://rarible.com/)&#x20;
* RocketPool (https://stake.rocketpool.net/)&#x20;
* Uniswap (https://app.uniswap.org/#/swap)&#x20;
* summer.fi (https://summer.fi/)

### Nov 7, 2023

#### iOS SDK v2.0.11

* Adds support for SPM directly in the PortalSwift repo&#x20;
* Makes the PortalSwift repo public to work better with CI tools

### Nov 4, 2023

#### iOS SDK v2.0.10

* Users can now create multiple signing shares across all of the Portal SDKs.&#x20;
  * For example, a user can create a wallet on an iOS device and continue using that wallet on your web app!&#x20;
  * Use `portal.provisionWallet` (or `portal.recoverWallet`) to enable your users to create multiple signing shares across all of the Portal SDKs.&#x20;
* Improved auto-connection for Native iOS WebView.

#### Android SDK v2.0.9

Users can now create multiple signing shares across all of the Portal SDKs.&#x20;

* For example, a user can create a wallet on an iOS device and continue using that wallet on your web app!&#x20;
* Use `portal.provisionWallet` (or `portal.recoverWallet`)&#x20;

### Nov 3, 2023

#### Web SDK v0.0.5

* Use `portal.provisionWallet` (or `portal.recoverWallet`) to enable your users to create multiple signing shares across all of the Portal SDKs.&#x20;

#### React Native SDK v2.0.10

* Use `portal.provisionWallet` (or `portal.recoverWallet`) to enable your users to create multiple signing shares across all of the Portal SDKs. y

</details>

<details>

<summary>October 2023</summary>

### Oct 27, 2023

#### iOS SDK v2.0.9

* Enhanced `portal.getTransactions` to support optional arguments: `limit`, `offset`, `order`, and `chainId`.&#x20;
* Transactions from `portal.getTransactions` now include `metadata.blockTimestamp` and `chainId`.
* Added support for `allowanceTarget` in Swap quotes.

#### Android SDK v2.0.8

* Enhanced `portal.getTransactions` to support optional arguments: `limit`, `offset`, `order`, and `chainId`.&#x20;
* Transactions from `portal.getTransactions` now include `metadata.blockTimestamp` and `chainId`.
* Added support for `allowanceTarget` in Swap quotes.
* Introduced `PortalWebView`.&#x20;
* Alpha release of Password/PIN Backups.

#### Web SDK v0.0.4

* Enhanced `portal.getTransactions` to support optional arguments: `limit`, `offset`, `order`, and `chainId`.&#x20;
* Transactions from `portal.getTransactions` now include `metadata.blockTimestamp` and `chainId`.
* Added support for `allowanceTarget` in Swap quotes.
* New helper methods for `getBalances`, `getTransactions`, and `getNFTs`.&#x20;
* Introduced Swaps support.

#### React Native SDK v2.0.9

* Enhanced `portal.getTransactions` to support optional arguments: `limit`, `offset`, `order`, and `chainId`.&#x20;
* Transactions from `portal.getTransactions` now include `metadata.blockTimestamp` and `chainId`.
* Added support for `allowanceTarget` in Swap quotes.&#x20;
* Alpha release of Password/PIN Backups.

### Oct 25, 2023

#### Web SDK v0.0.3

* Fixes issues with how `worker.js` is reading and writing `localStorage` items.&#x20;
  * Updates this process to use the same methods we're using in core by requesting values via the postMessage api.&#x20;
* Monorepo refactor browser - the npm package source code that will ultimately run in the browser within the customer's DOM iframe - the source code that is bundled and pushed up to GCP during the release process and ultimately run within an iframe living on web(-staging).portalhq.io&#x20;
  * This will ensure that the import path for iframe files and the npm package remain isolated, which should prevent mistakes and missed errors (particularly in reference to imports) between local development and production.

### Oct 23, 2023

#### Web SDK v0.0.2

* Centralizes our use of the Keychain instance for accessing localStorage and fixes the issues with how this class was reading and writing data.&#x20;
  * Updating the keys used in the Keychain instance Adding support for fetching client data in the Keychain class&#x20;
  * Ensuring the Keychain class was using the updated keys (with the clientId appended) to read and write data

### Oct 21, 2023

#### iOS SDK v2.0.8

* Resolves issues with WalletConnect when dApps exclude `requiredNamespaces` fields in their session proposal.&#x20;
* The alpha release of our Password/PIN Backups is now included.&#x20;
* Improved memory management for WalletConnect WebSocket connections.

#### Android SDK v2.0.7

* Resolves issues with WalletConnect when dApps exclude `requiredNamespaces` fields in their session proposal.&#x20;
* Improved memory management for WalletConnect WebSocket connections

### Oct 20, 2023

#### Web SDK v0.0.1

* Resolves issues with WalletConnect when dApps exclude `requiredNamespaces` fields in their session proposal&#x20;
* Sets max-age on Web SDK cookies
* Improved logging&#x20;
* The alpha release of our Password/PIN Backups is now included

#### React Native SDK v2.0.8

* Resolves issues with WalletConnect when dApps exclude `requiredNamespaces` fields in their session proposal&#x20;

### Oct 17, 2023

#### Web SDK v0.0.1-rc2n

* Resolved build issues with version 0.0.1-rc2m

### Oct 13, 2023

#### Web SDK v0.0.1-rc2m

* Added `backupStatus` and `signingStatus` in `portal.api.getClient` & `portal.client` returned values.&#x20;
* Updated types for `/clients/me` endpoint.&#x20;
* Added `portal.getTransactions`, `portal.getClient`.&#x20;
* Origin validation for postMessages.&#x20;
* Coding style unified via linters.

#### React Native SDK v2.0.7

* Added types for stringified MPC metadata.

### Oct 6, 2023

#### React Native SDK v2.0.6 & v2.0.5

* Added `backupStatus` and `signingStatus` in `portal.api.getClient` & `portal.client` returned values.&#x20;
* Updated types for `/clients/me` endpoint
* Made `portal.updateChain` async.&#x20;
* Typo fix in `portal_signatureReceived` event.&#x20;
* Coding style unified via linters.

</details>

<details>

<summary>September 2023</summary>

### Sep 30, 2023

#### iOS SDK v2.0.5

* Optimization: Introduce an optional featureFlag into the Portal instance.&#x20;
  * Set `optimized: true` for a 10x speed boost for generate, backup, and recover!&#x20;
* Correction: Changed `eth_sendTransaction` to `eth_signTypedData`.

#### Android SDK v2.0.4

* Optimization: Introduce an optional featureFlag into the Portal instance.&#x20;
  * Set `optimized: true` for a 10x speed boost for generate, backup, and recover!&#x20;
* Added `UserRecoverableGDriveException` in the Gdrive storage class.&#x20;
  * This exception streamlines the process of catching user permission or authentication issues.

### Sep 29, 2023

#### Web SDK v0.0.1-rc2l

* Security Update: Transitioned from static Paillier key usage to dynamic Paillier key retrieval.

#### React Native SDK v2.0.4

* Optimization: Introduce an optional featureFlag into the Portal instance.&#x20;
  * Set `optimized: true` for a 10x speed boost for generate, backup, and recover!&#x20;

### Sep 22, 2023

#### iOS SDK v2.0.4

* Enhanced MPC & API call tracking.&#x20;
* Refreshed binary.&#x20;
* Created SPM package.

#### Android SDK v2.0.3

* Enhanced MPC & API call tracking
* Refreshed binary.

#### Web SDK v0.0.1-rc2l

* Enhanced MPC & API call tracking.&#x20;
* Refreshed binary.&#x20;
* Recovery Update: Breaking Change:&#x20;
  * The recover function now solely updates signing shares, returning the wallet address similar to generate. Earlier versions updated both signing and backup shares, with potentially confusing error handling. For those wanting the older method, use the new legacyRecover function.

#### React Native SDK v2.0.3

* Introduced a `addChainsToProposal` method for better dApp proposal responses.&#x20;
* New `portal_connectError` event to report errors from Portal Connect.

### Sep 15, 2023

#### iOS SDK v2.0.3

* Added support for the following RPC methods:&#x20;
  * `eth_getBlockByNumber`
  * `eth_getBlockTransactionCountByHash`&#x20;
  * `eth_getBlockTransactionCountByNumber`&#x20;
  * `eth_getLogs`&#x20;
  * `eth_getTransactionByBlockHashAndIndex`
  * \`eth\_getTransactionByBlockNumberAndIndex\`
  * `eth_getBlockByHash`&#x20;
  * `eth_getTransactionByHash` \`eth\_getUncleByBlockHashAndIndex\`
  * &#x20;`eth_newBlockFilter`
  * `eth_newFilter`&#x20;
  * `eth_uninstallFilter`
  * `net_listening`&#x20;
  * `web3_clientVersion`
  * `web3_sha3`&#x20;
* Fixed issue passing `chainId` from Portal Connect Server

#### Android SDK v2.0.2

* Fixed issue passing `chainId` from Portal Connect Server.

#### React Native SDK v2.0.2

* Updated Portal UI components.

### Sep 11, 2023

#### React Native SDK v2.0.1

* Portal UI Components&#x20;
* Minor bug fixes&#x20;
* Style and theme update

### Sep 9, 2023

#### iOS SDK v2.0.2

* Portal Connect&#x20;
* Adds a `addChainsToProposal`method.&#x20;
  * This will allow you to add all the chains in your gateway config to the proposal that you get from a dapp when you approve the connection&#x20;
* Adds error codes

#### Android SDK v2.0.2

* Portal Connect&#x20;
* Adds a `addChainsToProposal`method.&#x20;
  * This will allow you to add all the chains in your gateway config to the proposal that you get from a dapp when you approve the connection&#x20;
* Adds error codes

### Sep 2, 2023

#### iOS SDK v2.0.0

* Recovery Update: Breaking Change:&#x20;
  * The recover function now solely updates signing shares, returning the wallet address similar to generate. Earlier versions updated both signing and backup shares, with potentially confusing error handling. For those wanting the older method, use the new legacyRecover function.&#x20;
* PortalConnect Enhancements: Boosted reliability with a new ping interval and advanced reconnection logic.

#### Android SDK v2.0.0

* Recovery Update: Breaking Change:&#x20;
  * The recover function now solely updates signing shares, returning the wallet address similar to generate. Earlier versions updated both signing and backup shares, with potentially confusing error handling. For those wanting the older method, use the new legacyRecover function.&#x20;
* PortalConnect Enhancements: Boosted reliability with a new ping interval and advanced reconnection logic.

### Sep 1, 2023

#### React Native SDK v2.0.0

* Recovery Update: Breaking Change:&#x20;
  * The recover function now solely updates signing shares, returning the wallet address similar to generate. Earlier versions updated both signing and backup shares, with potentially confusing error handling. For those wanting the older method, use the new legacyRecover function.&#x20;
* PortalConnect Enhancements: Boosted reliability with a new ping interval and advanced reconnection logic.

</details>

<details>

<summary>August 2023</summary>

### Aug 25, 2023

#### iOS SDK v1.1.8

* Adds support for transaction simulation

#### Android SDK v1.1.4

* Adds support for transaction simulation

#### Web SDK v0.0.1-rc2d to 0.0.1-rc2j

* Added `portal.api.simulateTransaction` Request using `portal.api.storedClientBackupShareKey` on backup and recover following successfully saving the client backup share encryption key to the user's backup method.&#x20;
* Add unit tests for Google Drive.

#### React Native SDK v1.1.3

* Adds support for `backupMethod` argument when calling `api.storedClientBackupShare()`
* Adds support for transaction simulation

### Aug 18, 2023

#### iOS SDK v1.1.7

* Fixed Portal Connect typing issue for some dapps sending no optional namespaces.&#x20;
* More errors delivered through the `portal_connectError` event.&#x20;
* Updated MPC binary.

### Aug 11, 2023

#### iOS SDK v1.1.6

* Added Request using `portal.api.storedClientBackupShareKey` on backup and recover following successfully saving the client backup share encryption key to the user's backup method.&#x20;
* Added unit tests for wallet safeguarding.&#x20;
* Added e2e tests for the provider.
* Updated MPC binary.

#### Android SDK v1.1.3

* Added Request using `portal.api.storedClientBackupShareKey` on backup and recover following successfully saving the client backup share encryption key to the user's backup method.
* Updated MPC binary.

#### React Native SDK v1.1.2

* Added Request using `portal.api.storedClientBackupShareKey` on backup and recover following successfully saving the client backup share encryption key to the user's backup method.&#x20;
* Added unit tests for generate, backup, and recover.&#x20;
* Changed GitHub workflows&#x20;
* Removes `IPortalConnect` references from classes to prevent breaking changes in test updates.

</details>

<details>

<summary>July 2023</summary>

### Jul 21, 2023

#### React Native SDK v1.1.0

* Added helper methods to Portal for the following:&#x20;
  * MPC: `createWallet`, `backupWallet`, `recoverWallet`&#x20;
  * Provider: `emit`, `ethSendTransaction`, `ethSign`, `ethSignTransaction`, `ethSignTypedData`, `on`, `once`, `personalSign`, `request`, `updateChain`&#x20;
  * Keychain: `deleteAddress`, `deleteSigningShare`
  * PortalConnect: `createPortalConnectInstance`
* Enabled disconnect from PortalConnect.&#x20;
* Fixed Refactor classes to enforce the following: Provider is the only class that manages chainid Keychain is the only class that manages address&#x20;
* Refactor PortalConnect to manage its own Provider instance&#x20;
* Add state management to PortalConnect class&#x20;
* Base the connected property of PortalConnect on the connectionState

</details>

<details>

<summary>June 2023</summary>

### Jun 11, 2023

#### iOS SDK v1.1.2

* Fixed multiple event listeners firing for a single signing Request&#x20;
* Fixed only firing event handlers for that specific request and removing handlers for events that have already been fired

#### React Native SDK v1.0.1

* Adds support for `connect.on('portal_dappSessionRequested', eventHandler)` event handlers to manage approval flows for Portal Connect sessions
* Adds support for `connect.on('portal_dappSessionRequestedV1', eventHandler)` event handlers to manage approval flows for Portal Connect sessions
* Adds support for `connect.emit('portal_dappSessionApproved', data)` event emitting to approve Portal Connect sessions
* Adds support for `connect.emit('portal_dappSessionRejected', data)`event emitting to reject Portal Connect sessions&#x20;
* Improves support for delivery of signatures to dApps through Portal Connect
* Improves support for delivery of signing rejections to dApps through Portal Connect

### Jun 9, 2023

#### iOS SDK v1.0.0

* Added `portal.keychain.validateOperations`:&#x20;
  * Checks if you can write, read, and delete from keychain.&#x20;
* Added validation checks for Keychain before running generate and recover.&#x20;
* Added validation checks for the selected backup method before running backup and recover.&#x20;
* Added `portal.api.storedClientBackupShare`, `portal.api.storedClientSigningShare` and wallet modification in-progress checks before running generate, backup, and recover.&#x20;
* Bump from v3 to v4 for default MPC version in classes.&#x20;
* Changed `portal.mpc.generate` & `portal.mpc.recover`to use `portal.api.storedClientSigningShare` when keychain completes storage
* Removed `getAvailability` from icloud storage

#### &#x20;Android SDK v1.0.0

* Added `portal.keychain.validateOperations`:&#x20;
  * Checks if you can write, read, and delete from keychain.&#x20;
* Added validation checks for Keychain before running generate and recover.&#x20;
* Added validation checks for the selected backup method before running backup and recover.&#x20;
* Added `portal.api.storedClientBackupShare`, `portal.api.storedClientSigningShare` and wallet modification in-progress checks before running generate, backup, and recover.&#x20;
* Bump from v3 to v4 for default MPC version in classes.&#x20;
* Changed `portal.mpc.generate` & `portal.mpc.recover`to use `portal.api.storedClientSigningShare` when keychain completes storage

### Jun 2, 2023

#### iOS SDK v0.2.4

* Improved error messaging
* Internal mpc binary improvement

#### Android SDK v0.9.2

* Improved error messaging&#x20;
* Added 0x Swaps integration
* Added extensions to `portal.api`: `getNFT`, `getBalance`, `getTransactions`&#x20;
* Internal mpc binary improvement

#### React Native SDK v0.3.8

* Improved error messaging
* Fixed MPC progress callback&#x20;
* Internal mpc binary improvement

</details>

<details>

<summary>May 2023</summary>



### May 26, 2024

#### iOS SDK v0.2.3

* Added extension to `portal.api`: `getNFT`, `getTransactions`, `getBalances`
* Added 0x Swaps integration&#x20;
* Fixed missing error check

#### React Native SDK v0.3.7

* Added progress call backs for mpc operations&#x20;
* Added 0x Swaps integration&#x20;
* Added extensions to `portal.api`: `getNFT`, `getBalance`, `getTransactions`

</details>

<details>

<summary>April 2023</summary>

### Apr 21, 2023

#### iOS SDK v0.1.9

* Added progress callbacks for MPC operations&#x20;
* Added completion handler for keychain operations&#x20;
* Added Webview signing on background thread&#x20;
* Added support for `signTypedData_v3` and `signTypedData_v4`&#x20;
* Changed v3 of MPC server&#x20;
* Fixed signing share being nil on backup MPC operation&#x20;
* Fixed blocking the webview UI when signing

#### Android SDK v0.8.4

* Added support for  `signTypedData_v3` and `signTypedData_v4` &#x20;
* Added support for EIP-1559 transactions&#x20;
* Changed v3 of MPC server

#### React Native SDK v0.3.6

* Added support for  `signTypedData_v3` and `signTypedData_v4` &#x20;
* Changed v3 of MPC server

</details>
