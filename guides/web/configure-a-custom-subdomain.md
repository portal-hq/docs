---
description: >-
  Complete configuration for the Portal Web SDK by enabling it to run on a
  subdomain of your application.
---

# Configure a custom subdomain

In order to support Safari and other browsers with third-party cookie restrictions you will need to configure a subdomain of your application to point to Portal's web backend.&#x20;

### Setup Instructions

Adding a record on your DNS provider will enable Portal to run on a sub-domain of your application.&#x20;

1. Create a `CNAME`  record to `web.portalhq.io` in your DNS provider:
   1. Set the DNS Record Type to `CNAME`
   2. Set the Name or Host field to `portal` (or any value you'd like to use for the subdomain)  This will create a subdomain like `portal.yourdomain.com`.&#x20;
   3. Set the Data or Content field to `web.portalhq.io`
2. Reach out to Portal to let us know the value of the subdomain you've configured.
3. Portal will update the backend configuration to support that subdomain and issue a TLS certificate.

<figure><img src="../../.gitbook/assets/example-cname (1).png" alt=""><figcaption><p>Example CNAME configuration.</p></figcaption></figure>

{% hint style="info" %}
Your subdomain **must** be a child of your the application that the Portal Web SDK will be running on.
{% endhint %}

We recommend using  `portal.` as the name of the subdomain on your application. So if your application is at `yourdomain.com` we recommend configuring `portal.yourdomain.com`. If your application is at `app.yourdomain.com` the we recommend configuring `portal.app.yourdomain.com` .

### Why do I need a custom domain?

Before configuring a custom subdomain, the Portal Web SDK depends on third-party cookies to authenticate with the Portal backend.&#x20;

<figure><img src="../../.gitbook/assets/image (40).png" alt=""><figcaption><p>Domain separation before implementing custom domains.</p></figcaption></figure>

While this is a valid use case of third-party cookies, many browsers are working to restrict usage of third party cookies for privacy purposes as they're often abused by tracking services.&#x20;

Safari currently blocks third party cookies by default and Chrome is working to deprecate them in 2024.&#x20;

Unfortunately, browsers have not yet agreed upon the best way to handle legitimate use cases. While emerging web standards like CHIPS will help to support valid third party cookies use cases in the near future, the standard is not yet supported by all browsers.&#x20;

Currently, the best way to support this is to convert the third party Portal cookies to first party cookies on your domain.

However, by configuring the Web SDK to run on a subdomain of your main application you provide additional isolation of Portal resources from your main application.&#x20;

<figure><img src="../../.gitbook/assets/image (42).png" alt=""><figcaption><p>Portal Web SDK resources are isolated to the custom subdomain.</p></figcaption></figure>

### Security in Depth

Using a separate subdomain for the Portal Web SDK ensure separation of resources from your main application.

**Cookies**

The Web SDK leverages cookies to store the `ClientSessionToken` which is used to authenticate a user to the Portal backend.

Cookies are configured with the security flags `http-only` and `secure` to ensure cookies are only transmitted over secure connections (TLS) and are inaccessible by javascript.

Cookies are assigned to the subdomain, which means they are **not** included on requests to your application, and only on requests to Portal's backend.

**LocalStorage**

The Web SDK leverages LocalStorage to store the user's signing share which is used during MPC operations to sign messages and transactions.

Values stored in LocalStorage are scoped to the subdomain which means they **cannot** be accessed by the DOM of your application. This is a helpful level of isolation that protects the MPC share from any XSS bugs or malicious javascript on your web application.
