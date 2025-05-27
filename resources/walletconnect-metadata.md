---
description: >-
  Personalize the pairing information displayed on third-party dApps via
  WalletConnect from the Portal Admin Dashboard.
---

# WalletConnect metadata

When users connect to a dApp using WalletConnect, the dApp may showcase your organization's details, such as the **name**, **description**, **website**, and **logo**. Personalize this information following the below steps.

## Customize WalletConnect Metadata

### **Go to the Portal Admin Dashboard**

* Go to the [Settings page](https://app.portalhq.io/settings) of the Portal Admin Dashboard.
* Scroll until you find the **WalletConnect Metadata** section.

<figure><img src="../.gitbook/assets/image (13).png" alt=""><figcaption><p>The Wallet Connect Metadata section on the Settings page within the <a href="https://app.portalhq.io">Portal Admin Dashboard</a>.</p></figcaption></figure>

### **Modify Metadata**

* If metadata for your Portal environment is unset, click **New**.
* If it's already set, click **Edit** to make updates.
* Personalize the metadata and click either **Create** or **Update** in the modal.

<figure><img src="../.gitbook/assets/image (44).png" alt=""><figcaption><p>Customize the metadata displayed by dApps using Wallet Connect.</p></figcaption></figure>

{% hint style="info" %}
After you register your dApp with WalletConnect Cloud, you will be assigned a **project ID**. You can find this ID in the [WalletConnect Cloud dashboard](https://cloud.walletconnect.com/app).
{% endhint %}

<figure><img src="../.gitbook/assets/Screenshot 2023-12-19 at 1.18.11 AM.png" alt=""><figcaption><p>Be sure to save this project ID into your WalletConnect Metadata information on the <a href="https://app.portalhq.io">Portal Admin Dashboard</a>.</p></figcaption></figure>

### **Test for Accuracy**

* Initiate a connection to a dApp using WalletConnect.
* Verify that dApps show the updated metadata correctly.
* If you notice any discrepancies, return to the [Portal Admin Dashboard](https://app.portalhq.io) to make necessary adjustments.

{% hint style="info" %}
If you don't provide custom metadata for WalletConnect, the default details will be from the Portal's organization information.
{% endhint %}

Congratulations! dApps connected via WalletConnect now show personalized pairing details to your users. Well done! :tada:
