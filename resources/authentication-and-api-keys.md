---
description: >-
  This guide will walk you through the two different credentials you need to
  work with Portal.
---

# Authentication and API Keys

There are two different types of authentication used with Portal integration: a **Portal API Key** for server side requests and a **Client Session Token** (also shown as Client API Key) for authenticating SDK users.

The **Portal API Key** is used server side to create new Client Session Tokens, fetch information about clients, or access other endpoints in the [Custodian API](../reference/custodian-api/).

The **Client API Key** is used to initialize the Portal SDKs in order to authenticate an end user ("client"). It can also be used with the Enclave MPC API or other endpoints in the [Client API](../reference/client-api/).

{% hint style="info" %}
Reach out to us on [Slack](https://join.slack.com/t/portalcommunity/shared_invite/zt-2obhgakbb-TGGNAutwgHF4VCGbr0tCYA) to get access to the Admin Dashboard and start developing!
{% endhint %}

## Portal API Keys

This is the API key used by your server to make requests to Portal.

### Creating a Portal API Key

1. Log in to [app.portalhq.io](http://app.portalhq.io/).
2. Navigate to Settings -> Custodian API Key.
3. Press "New" to create a new Portal API Key.

<figure><img src="../.gitbook/assets/image (8).png" alt=""><figcaption></figcaption></figure>

{% hint style="warning" %}
Before going live with real users, always ensure that you create clients using a Portal API Key from your Portal's **Production** environment. Read more [here](going-to-production.md) on going live with real users.
{% endhint %}

## Client Session Tokens

Users (clients) are required to authenticate their devices to Portal in order to create wallets and submit transactions. **Client Session Tokens** **(CST)** are _short-lived_, _auto-refreshing_ tokens used to authenticate users to Portal.

**Short-Lived**\
CSTs expire after 24 hours of no activity. After this, a new CST must be requested from your backend.

**Auto-Refreshing**\
Every authenticated request with a CST updates the token's expiry to 24 hours from that request time. There's a max refresh duration of 7 days, after which a CST can no longer be auto-refreshed.

The goal of this system is keep sessions short (reducing attack windows) while minimizing the number of requests needed to create sessions.&#x20;

#### In Practice:

* **Active User**:  If a user uses Portal daily, their CST expiry extends by 24 hours each day. They'll need a new CST from your backend only once per week (after hitting the 7-day max refresh duration).
* **Inactive User**: Users logging in after a few days or weeks will need a new CST from your backend on every login since CSTs expire after 24 hours.

### Creating a Test Client API Key

If you'd like to quickly test out a Portal SDK or the [Enclave MPC API](../guides/enclave-mpc-api/) you can generate a Client _API Key_ from the Portal Admin dashboard! It has the same permissions as a Client Session Token, except that it doesn't expire. This is helpful for development!

{% hint style="info" %}
Client API Keys are associated with a single wallet! So you will need to create multiple test credentials if you want to generate more than one wallet.
{% endhint %}

1. Log in to [app.portalhq.io](http://app.portalhq.io/).
2. Navigate to Settings -> Test Client API Keys.
3. Press "New" to create a new Client API Key.

<figure><img src="../.gitbook/assets/image (7).png" alt=""><figcaption></figcaption></figure>

### Creating Client Session Tokens in your Application

To authenticate mobile users with Portal, they need a unique Client Session Token. Request this token from your backend using the Portal API Key, then share it with mobile users after they log in.

<figure><img src="../.gitbook/assets/image (5).png" alt=""><figcaption></figcaption></figure>

#### Initial Registration

When a user first registers for your mobile app, use your Portal API Key to create a Client Session Token. Remember to associate the returned `clientId` with the user in your system.

{% code title="portal.ts" lineNumbers="true" %}
```typescript
const response = await axios.post(
    `https://api.portalhq.io/api/v1/custodians/clients`,
    {}, 
    { headers: { Authorization: `Bearer ${<PORTAL_API_KEY>}` } }
);
const clientSessionToken = response.data.clientSessionToken;
const clientId = response.data.id;
```
{% endcode %}

{% hint style="info" %}
Ensure the **`clientId`** is stored securely as it is crucial for future operations.
{% endhint %}

#### Subsequent Logins

When the token needs refreshing (during later logins), use the following endpoint:

```typescript
const response = await axios.post(
    `https://api.portalhq.io/api/v1/custodians/clients/${clientId}/session`,
    {}, 
    { headers: { Authorization: `Bearer ${<PORTAL_API_KEY>}` } }
);
const refreshedClientSessionToken = response.data.clientSessionToken;
```

Your user can now use this refreshed `clientSessionToken`.

{% hint style="info" %}
When your user's CST expires, all Portal SDKs will throw an error on the next MPC Operation the user makes (e.g. creating a wallet, backing up a wallet, recovering a wallet, or signing). That error will include [the error code **`INVALID_API_KEY`**](https://docs.portalhq.io/resources/error-codes/network-errors) in the SDK methods, which you can use as an indicator to refresh your CST.
{% endhint %}
