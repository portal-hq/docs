---
description: Follow this guide to integrate Portal in your web app.
---

# Web

Portal provides MPC **wallets** and dApp **connections** for organizations and their users.\
\
To integrate Portal, an organization adds a **client library** to their web app, configures a `CNAME` record, and adds a few **server API endpoints**.

## Basic setup

The basic Portal setup consists of a single packages:&#x20;

* `@portal-hq/web` - The core Portal library

This allows you to initialize `Portal` in your app. We recommend only initializing one Portal instance per user.

### Authentication

Follow this guide to gather all of the credentials you need to [Authenticate to Portal](../../resources/authentication-and-api-keys.md).

## Installation <a href="#installation" id="installation"></a>

{% tabs %}
{% tab title="yarn" %}
```bash
yarn add @portal-hq/web
```
{% endtab %}

{% tab title="npm" %}
```bash
npm install --save @portal-hq/web
```
{% endtab %}
{% endtabs %}

### Update TSConfig

The Web SDK uses the [TSConfig `lib` configuration](https://www.typescriptlang.org/tsconfig#lib) to properly type interaction with the DOM. Update your TSConfig file to include this:

```
// tsconfig.json
{
  "compilerOptions": {
    "lib": ["es2022", "dom"],
    ...
  },
  ...
}
```

### Initializing Portal

<pre class="language-typescript"><code class="lang-typescript">import Portal from '@portal-hq/web'

<strong>export const portal = new Portal({ 
</strong>    apiKey: 'YOUR-CLIENT-API-KEY',
    autoApprove: true,
    rpcConfig: {
      'eip155:1': 'YOUR-INFURA-OR-ALCHEMY-URL',
      'solana:5eykt4UsFv8P8NJdTREpY1vzqKqZKvdp': 'https://api.mainnet-beta.solana.com',
    },
    host: 'YOUR-CUSTOM-SUBDOMAN' // Set this once you've defined your custom subdomain
})
</code></pre>

{% hint style="warning" %}
We recommend only initializing one Portal instance per user. Do **not** modify **`portal.apiKey`** directly.
{% endhint %}

{% hint style="info" %}
If you are using [Client Session Tokens (CSTs)](../../resources/authentication-and-api-keys.md), this hint is for you.

When your user's CST expires, all Portal SDKs will throw an error on the next MPC Operation the user makes (e.g. creating a wallet, backing up a wallet, recovering a wallet, or signing). That error will include a code **`SESSION_EXPIRED`** in the SDK methods, which you can use as an indicator to refresh your CST.
{% endhint %}

### Custom Subdomain

In order to support usage on Safari or other browsers with third-party cookie restrictions, you need to configure a custom subdomain for the Portal Web API.&#x20;

Follow the instructions in our [configure-a-custom-subdomain.md](configure-a-custom-subdomain.md "mention") guide to complete setup for the Web SDK!

### Web SDK Architecture

The Web SDK leverages our [Enclave MPC API](../enclave-mpc-api/) for efficient MPC operation performance. Utilizing confidential computing using [AWS Nitro Enclaves](https://docs.aws.amazon.com/enclaves/latest/user/nitro-enclave.html), the Enclave MPC API provides the same MPC client-server distribution of responsibilities with the speed and efficiency of server side compute.

