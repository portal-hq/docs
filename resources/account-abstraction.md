---
description: >-
  This guide will walk you through the process of configuring Account
  Abstraction for your organization and your users.
---

# Account abstraction

This document serves as a comprehensive guide for enabling and configuring Account Abstraction (AA) within your organization. This currently allows your organization to sponsor gas fees for your clients, using specified policies and chains.

The Account Abstraction feature is available on all mobile and web SDKs.

{% hint style="warning" %}
While AA clients can make Solana/Tron/Stellar/etc sign requests, these transactions are not gas subsidized. This is because AA clients on Solana/Tron/Stellar/etc utilize a standard MPC wallet under the hood, which doesn't support gas sponsorship in the same way as EVM-based chains.
{% endhint %}

## Supported Networks

This list shows the blockchain networks that Portal supports for account abstraction.

| Chain                     | Chain ID             | Type    |
| ------------------------- | -------------------- | ------- |
| Ethereum                  | `eip155:1`           | Mainnet |
| Ethereum Goerli           | `eip155:5`           | Testnet |
| Optimism Mainnet          | `eip155:10`          | Mainnet |
| BSC Mainnet               | `eip155:56`          | Mainnet |
| Gnosis Mainnet            | `eip155:100`         | Mainnet |
| Polygon Mainnet           | `eip155:137`         | Mainnet |
| opBNB Mainnet             | `eip155:204`         | Mainnet |
| Optimism Goerli Testnet   | `eip155:420`         | Testnet |
| Astar ZKEvm               | `eip155:3776`        | Mainnet |
| Cyber Mainnet             | `eip155:7560`        | Mainnet |
| Base Mainnet              | `eip155:8453`        | Mainnet |
| Gelato OP Testnet         | `eip155:42069`       | Testnet |
| Arbitrum One Mainnet      | `eip155:42161`       | Mainnet |
| Arbitrum Nova             | `eip155:42170`       | Testnet |
| Celo                      | `eip155:42220`       | Mainnet |
| Avalanche Fuji Testnet    | `eip155:43113`       | Testnet |
| Avalanche C-Chain Mainnet | `eip155:43114`       | Mainnet |
| Celo Alfajores Testnet    | `eip155:44787`       | Testnet |
| Linea Testnet             | `eip155:59140`       | Testnet |
| Linea Mainnet             | `eip155:59144`       | Mainnet |
| Polygon Mumbai Testnet    | `eip155:80001`       | Testnet |
| Polygon Amoy Testnet      | `eip155:80002`       | Testnet |
| Blast                     | `eip155:81457`       | Mainnet |
| Base Goerli Testnet       | `eip155:84531`       | Testnet |
| Base Sepolia Testnet      | `eip155:84532`       | Testnet |
| Arbitrum Goerli Testnet   | `eip155:421613`      | Testnet |
| Arbitrum Sepolia Testnet  | `eip155:421614`      | Testnet |
| Astar ZKatana Testnet     | `eip155:1261120`     | Testnet |
| Astar zKyoto Testnet      | `eip155:6038361`     | Testnet |
| Ethereum Sepolia          | `eip155:11155111`    | Testnet |
| Optimism Sepolia Testnet  | `eip155:11155420`    | Testnet |
| Polygon Blackberry        | `eip155:94204209`    | Testnet |
| Cyber Testnet             | `eip155:111557560`   | Testnet |
| OP Celestia Raspberry     | `eip155:123420111`   | Testnet |
| Blast Sepolia             | `eip155:168587773`   | Testnet |
| Degen Mainnet             | `eip155:666666666`   | Mainnet |
| Arbitrum Blueberry        | `eip155:88153591557` | Testnet |

{% hint style="info" %}
For Solana networks, account abstraction is supported through the use of the `feePayer` field in transactions.
{% endhint %}

{% hint style="warning" %}
If you set a policy for a mainnet chain, your account will be charged for gas subsidization even if you are on your Portal Development environment. Please set policies for Testnet chains while you are still building or testing to avoid unnecessary charges.
{% endhint %}

## Generating smart contract wallets for your users

Generating smart contract wallets for your users is incredibly easy with Portal.

### Step 1: Get access to the feature

Reach out to the Portal team to request access.

### Step 2: Create Clients with Account Abstraction

After Portal enables Account Abstraction for your organization, you are ready to create your first clients. This can be achieved by hitting [the **Create a new client** endpoint](../reference/custodian-api/v1-endpoints.md#create-a-new-client) with this as the request body:

```json
{
  "isAccountAbstracted": true
}
```

### Step 3: Generate a Smart Contract Address!

Now that you have Account Abstraction enabled for your client, you can [use the SDKs as you normally would](../guides/react-native/create-a-wallet.md) to create a wallet, back it up, recover it, and sign. Since Account Abstraction is now enabled for the client, the address returned after creating a wallet is a smart contract address.

With the completion of the above steps, you would have successfully created a client with Account Abstraction. Congratulations! :tada: You can now proceed to use the Portal SDKs and services as usual.

{% hint style="info" %}
The client's smart contract wallet is only deployed upon their first successful **`eth_sendTransaction`** request.
{% endhint %}

{% hint style="info" %}
The hash from signing with an Account Abstraction client is a[ user operation hash](https://eips.ethereum.org/EIPS/eip-4337), not a transaction hash. Use tools like[ Jiffy Scan](https://www.jiffyscan.xyz/?network=goerli) to explore user operations.
{% endhint %}

{% hint style="info" %}
Clients using Account Abstraction require dApps they interact with to support[ EIP-1271](https://eips.ethereum.org/EIPS/eip-1271) for verifying signed messages.
{% endhint %}

## Configure Gas Subsidization Policy

### Step 1: Modify Gas Subsidization Policy

In order to configure your Account Abstraction settings, log into [the Portal Admin Dashboard](https://app.portalhq.io). Navigate to the **Settings** page and scroll down to find the **Account Abstraction Configuration** section.

<figure><img src="../.gitbook/assets/image (45).png" alt=""><figcaption><p>Modify Account Abstraction configuration in the Portal Admin Dashboard.</p></figcaption></figure>

In the section, you will find multiple rows corresponding to various chains for which you can set gas subsidization policies. To modify a policy, simply click on **Edit Policy** for the desired chain.

<figure><img src="../.gitbook/assets/Screenshot 2024-12-03 at 6.01.38 AM.png" alt=""><figcaption><p>Click "Edit Policy" to modify the amount of gas sponsored per chain every month (30 days).</p></figcaption></figure>

### Step 2: Confirm Policy Configuration

Once you have made your desired changes to the gas subsidization policies, ensure you verify and confirm the configurations. Contact our team for more information on billing.

<figure><img src="../.gitbook/assets/Screenshot 2024-12-03 at 6.05.47 AM.png" alt=""><figcaption><p>Specify the amount of gas to sponsor for a chain every month (30 days).</p></figcaption></figure>

Congratulations! :tada: You will now be sponsoring the gas fees for the client, based on the policies you have set and the chain the client is using.

### Support

If you encounter any issues or have questions about the Account Abstraction feature, feel free to reach out to our support team.
