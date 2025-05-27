---
description: Follow this guide to integrate Portal in your app.
---

# React Native

Portal provides MPC **wallets** and dApp **connections** for organizations and their users.\
\
To integrate Portal, an organization adds a **client library** to their mobile app and a few **server API endpoints**.

![The Portal library connects your mobile app and server with the web3 ecosystem.](<../../.gitbook/assets/image (18).png>)

## Basic setup

The basic Portal setup consists of three packages:&#x20;

* `@portal-hq/core` - The core Portal library
* `@portal-hq/keychain` - An adapter for storing MPC signing shares on-device
* `@portal-hq/gdrive-storage` - An adapter for storing MPC backup shares off-device

These pieces allow you to initialize `Portal` in your app.

### Authentication

Follow this guide to gather all of the credentials you need to [Authenticate to Portal](../../resources/authentication-and-api-keys.md).

## Installation <a href="#installation" id="installation"></a>

{% tabs %}
{% tab title="yarn" %}
```bash
yarn add @portal-hq/core @portal-hq/keychain @portal-hq/gdrive-storage
```
{% endtab %}

{% tab title="npm" %}
```bash
npm install --save @portal-hq/core @portal-hq/keychain @portal-hq/gdrive-storage
```
{% endtab %}
{% endtabs %}

#### Dependency linking

Because these packages have native module dependencies there is some additional linking required to make it work with your React Native project.

Explicitly install the native module packages in your project.

{% tabs %}
{% tab title="yarn" %}
```bash
yarn add react-native-keychain
```
{% endtab %}

{% tab title="npm" %}
```bash
npm install --save react-native-keychain react-native-rsa-native
```
{% endtab %}
{% endtabs %}

#### MPC

The core Portal library relies on a native module to support MPC behaviors. In order to enable this functionality, you must properly link Portal's MPC client to your React Native project.

{% tabs %}
{% tab title="Android" %}
<pre class="language-java" data-title="app/build.gradle" data-overflow="wrap"><code class="lang-java">dependencies {
  ...

  runtimeOnly files("../../node_modules/@portal-hq/core/android/libs/mpc.aar")

<strong>  ...
</strong>}
</code></pre>
{% endtab %}

{% tab title="iOS" %}
```
cd ios && pod install && cd ..
```
{% endtab %}
{% endtabs %}

_To learn more about how to use Portal MPC, see:_ [_@portal-hq/core_](../../reference/react-native/portal-hq-core.md#mpc)

### Initializing Portal

{% code title="MyAppComponent.tsx" overflow="wrap" lineNumbers="true" %}
```typescript
import { useEffect, useState } from 'react'

// Portal imports
import { BackupMethods, Portal, PortalContextProvider } from '@portal-hq/core'
import Keychain from '@portal-hq/keychain'
import GoogleDriveStorage from '@portal-hq/gdrive-storage'
import { PasswordStorage } from '@portal-hq/utils/src/definitions'

const MyAppComponent = () => {
  // Store the Portal instance in the state
  const [portal, setPortal] = useState<Portal>(null)

  useEffect(() => {
    if (!portal) {
      // Initialize backup storage providers.
      const gDriveStorage = new GoogleDriveStorage({
        androidClientId: 'YOUR_ANDROID_CLIENT_ID',
        iosClientId: 'YOUR_IOS_CLIENT_ID',
      })
      const passwordStorage = new PasswordStorage()

      // Create a Portal instance
      setPortal(
        new Portal({
          autoApprove: true, // If you want to auto-approve transactions
          apiKey: 'YOUR_PORTAL_CLIENT_API_KEY',
          backup: {
            [BackupMethods.GoogleDrive]: gDriveStorage,
            [BackupMethods.Password] : passwordStorage
          },
          chainId: 1,
          gatewayConfig: {
            'eip155:1': 'https://mainnet.infura.io/v3/YOUR_INFURA_API_KEY',
          },
        }),
      )
    }
  }, [portal])

  return (
    {/* Expose your Portal instance to your app */}
    <PortalContextProvider value={portal as Portal}>
      {/** 
        * Now all children rendered in this scope 
        * will have access to the `Portal` instance 
        * via the `usePortal` hook 
        */}
    </PortalContextProvider>
  )
}

export default MyAppComponent
```
{% endcode %}

_For more information on how to use the Portal class, see:_ [_@portal-hq/core_](../../reference/react-native/portal-hq-core.md)

## Next Steps

Now that you've initialized your Portal instance, you can [generate a wallet](create-a-wallet.md)!

{% hint style="info" %}
If you are using [Client Session Tokens (CSTs)](../../resources/authentication-and-api-keys.md), this hint is for you.

When your user's CST expires, all Portal SDKs will throw an error on the next MPC Operation the user makes (e.g. creating a wallet, backing up a wallet, recovering a wallet, or signing). That error will include a code **`SESSION_EXPIRED`** in the SDK methods, which you can use as an indicator to refresh your CST.
{% endhint %}
