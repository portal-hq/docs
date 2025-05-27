---
description: >-
  In this page, we'll go through setting up policies on your security firewall
  to help protect your users/wallets from scams and threats.
---

# Security firewall

The security firewall is accessible through the Portal web app at [https://app.portalhq.io/](https://app.portalhq.io/login).

Step 1: Click on the "Firewall" button on the right side of the page

Step 2: Click on Block address on the top right and enter either a digital address or smart contract address with your reason.;

Once that's completed the address will be set in a block state and available to be updated for the future.

<figure><img src="../.gitbook/assets/647758f2d2115dde57409fe4_Portal Wallet Firewall.gif" alt=""><figcaption></figcaption></figure>

**Dynamic scam and exploit protection**&#x20;

Portal automatically blocks addresses, smart contracts and dApps associated with suspicious activity based on open source blocklists and updates the firewall with new information on a daily basis.&#x20;

Additionally, Portal reviews transactions in process, checking both the _to_ and _data_ fields of a transaction. If either field contains an address that has been blocked, the transaction is rejected. This protects users from sending a transaction to an otherwise safe address if that address is associated with any blocked addresses or smart contracts directly (determined by the _to_ field) or indirectly (determined by the _data_ field).

For more detail information regarding the security firewall take a look at our blog post [here](https://www.portalhq.io/post/introducing-the-portal-wallet-firewall).

