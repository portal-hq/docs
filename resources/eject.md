---
description: >-
  Eject allows a user to construct a private key from their MPC shares so that
  they may import it into a wallet like MetaMask.
---

# Eject

### How does it work?

In order to be able to recover the private key using your MPC shares, they simply have to be "added" up. Now thats a simplification of the underlying cryptographic operation, but the takeaway here is that you can take two corresponding shares, combine them, and get the private key which is can be imported into any self-custodial wallet like Metamask.&#x20;

The private key calculation operation happens on client side, and does not involve Portal's servers.&#x20;

{% hint style="warning" %}
After ejecting a user private key from their key shares, Portal is no longer responsible for the security of the funds in that wallet as we can no longer guarantee split custody. Reach out to us if you have any questions.
{% endhint %}

### When would I want to use Eject?

Eject allows you to provide the option for your users to switch from a two share MPC architecture to a single private key while maintaining their assets. Put another way, it allows users to "export" their MPC wallet into a wallet provider like Metamask.

{% hint style="warning" %}
This is a sensitive operation. We strongly recommend to guard this operation by multiple factors of authentication as it removes the security benefits of split custody and can leave a user more susceptible to phishing attacks.
{% endhint %}

### What happens to the shares stored on Portal after the user runs Eject? Can the user still use Portal after running Eject?

After a user ejects their wallet Portal **still supports all MPC operations.** We update our database to record that the wallet has been ejected, but we do not suspend operation of the existing MPC shares or backups. While this is the default functionatliy, let us know if you have a use case with different requirements.
