---
description: >-
  This guide will walk you through the process of taking your application to
  Production using Portal.
---

# Going to Production

### Prerequisites

Before we get started, please ensure you have the following:

* An active account with Portal.
* Access to the Portal web app.
* Access to the Portal SDK you're using.
* Wallets that have been tested in your "Development" environment and have successfully been generated, backed up, recovered, and signed.

### Step 1: Switch to "Production" in the Web App

The first step is to switch your web app to the Production environment. This can be accomplished within your Portal web app. Follow the steps outlined below:

1. [Log into](https://app.portalhq.io) your Portal account.
2. Locate the **Environment** dropdown in the bottom-right corner of your screen.
3. Switch it to **Production**.

![](<../.gitbook/assets/image (20).png>)

### Step 2: Create a Production API Key

The next step is to create an API key that will be used in the Production environment. Here's how to do it:

1. After switching to the Production environment, navigate to the **API Keys** section on the Settings page.
2. Click on the **New +** button.
3. A modal will pop up. Give your new Production API key a recognizable name. For instance, "Production Key".
4. Click on **Create**.

After clicking Create, your new Production API key is generated. You can now use this key to create production clients via the REST API endpoint.

This will ensure that all clients in your Production environment will be logically separated from your Development environment, and restrict access to only your Production API key.

### Step 3: Configure a Production Self-Managed Backup Webhook \[Optional]

This step only applies if you chose to use [Self-Managed Backups](self-managed-backups.md) to store backup shares on your own backend instead of Portal Managed Backup which stores the backup shares in Portal's backend.

If so follow the Self-Managed Backup instructions to:

1. Set up your [database schema](self-managed-backups.md#your-database) to store Portal backup shares.
2. Spin up a [webhook server](self-managed-backups.md#api-endpoints-you-need-to-build) to accept backup and recovery webhook requests. Be sure to add IP Allowlisting and authenticate the Webhook Secret.
3. Update the [Portal webhook configuration](self-managed-backups.md#configuration) with yout webhook URL.

Remember, this should be a different configuration than your Development environment setup.

Congratulations! You are now set up to create Production wallets with Portal. If you encounter any issues or have further questions, please don't hesitate to contact Portal support.

**Happy coding!**
