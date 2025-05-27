# Passkey + Enclave

Allow customers to create a native passkey on their device that is used to authenticate into a secure enclave that holds the encryption key for the user. Customers passkeys are backed up to the native cloud storage for their device.&#x20;

#### Implementation Requirements

1. Initialize passkey storage as a backup option in the Portal Config Object.&#x20;
2. Add `portalhq.io` as a trusted credential domain in your app.
3. Share your app id with the Portal Team.

Check out our quick start guides for implementing password storage in our sdks:

iOS SDK - [#passkey-+-enclave-backup](../../guides/ios/legacy-documentation/back-up-a-wallet/backup-options.md#passkey-+-enclave-backup "mention")

Android SDK - [#passkey--enclave-backup](../../guides/android/back-up-a-wallet.md#passkey--enclave-backup "mention")

React Native SDK - [#passkey--enclave](../../guides/react-native/back-up-a-wallet.md#passkey--enclave "mention")

Web SDK - [#passkey--enclave-backup](../../guides/web/legacy-documentation/back-up-a-wallet/backup-options.md#passkey--enclave-backup "mention")

