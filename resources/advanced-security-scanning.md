---
description: >-
  Enable advanced security scanning in the Portal Admin Dashboard to protect
  your users from malicious transactions and domain connections using Blockaid's
  robust security features.
---

# Advanced security scanning

## Overview

Advanced security scanning enhances the security of your users' crypto transactions and their dApp connections by integrating [Blockaid](https://www.blockaid.io/)’s powerful threat detection directly into your Portal environment. This feature can be easily enabled or disabled through the [Portal Admin Dashboard](https://app.portalhq.io), under the Settings page in the "Security" section.

{% hint style="warning" %}
This feature is rate limited by default. If you require a higher rate limit, please contact Portal's support team for assistance.
{% endhint %}

## Steps to get started:

### **Step 1: Access the Settings Page**

To begin, log into your [Portal Admin Dashboard](https://app.portalhq.io). Navigate to the [Settings](https://app.portalhq.io/settings) page and locate the "Security" section.

### **Step 2: Enable Advanced Transaction Validation**

Within the "Security" section, you will find an option to enable Advanced Security Scanning. By enabling this feature, Portal will automatically generate a Blockaid API Key specific to your current Portal environment ("Development" or "Production").&#x20;

## **Transaction Validation**

Once enabled, requests made by your users on supported chains will be validated through Blockaid. Portal currently supports transaction validation and simulation on all EVM and Solana chains [that Blockaid supports](https://docs.blockaid.io/reference/supported-chains).

If a transaction is detected as malicious, it will be blocked and an error message will be returned: "Firewall: Transaction is malicious".

## **dApp Connection Validation**

In addition to transaction validation, any attempt by your users to connect to a dApp via PortalConnect (WalletConnect) will trigger a URL validation through Blockaid when Advanced Security Scanning is enabled. Should the domain be identified as malicious, the connection attempt will be blocked with the error message: "Blocking connection attempt from malicious domain: \[domain]."



Congratulations! :tada:You're now protecting your users web3 experiences with the Portal Advanced Security Scanning feature. Feel free to reach out to our support team if you have any questions!
