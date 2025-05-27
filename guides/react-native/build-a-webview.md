---
description: >-
  Our ready-to-use WebView component helps you quickly integrate Portal into
  your app.
---

# Build a WebView

{% hint style="info" %}
Before using the Portal mobile components, be sure you have run `portal.mpc.generate() to generate a wallet.`&#x20;
{% endhint %}

For more information on generating MPC signing shares, see: [Generating a wallet](create-a-wallet.md).

## Installation

{% tabs %}
{% tab title="yarn" %}
```bash
yarn add @portal-hq/webview
```
{% endtab %}

{% tab title="npm" %}
```bash
npm install --save @portal-hq/webview
```
{% endtab %}
{% endtabs %}

### Dependency linking

Because these packages have native module dependencies there is some additional linking required to make it work with your React Native project.

Explicitly install the native module packages in your project.

{% tabs %}
{% tab title="yarn" %}
```bash
yarn add react-native-webview
```
{% endtab %}

{% tab title="npm" %}
```bash
npm install --save react-native-webview
```
{% endtab %}
{% endtabs %}

## Basic Usage

```typescript

import React, { FC } from 'react'

// Portal imports
import { usePortal } from '@portal-hq/core'
import Webview from '@portal-hq/webview'

const MyBrowser: FC = () => {
  // Store the Portal instance in the state
  const portal = usePortal()
  
  return (
    <View style={{ width: '100%', height: '100%' }}>
      {portal && 
        <Webview 
          url="https://app.uniswap.org/#/swap" // Any URL for a dapp
          onNavigationStateChange?: (event: WebViewNavigation) => void
          onSigningRequested?: (approvalRequest: any) => void // only needed if auto-approve is set to false
           />}
    </View>
  )
}

export default MyBrowser
```

## Next Steps

You now have a working dApp Browser example!
