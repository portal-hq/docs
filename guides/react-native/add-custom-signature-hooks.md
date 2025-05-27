---
description: >-
  This guide will help you configure custom signature hooks for requests to the
  Portal Provider to ensure your business logic is properly implemented into
  Portal's signing flow.
---

# Add custom signature hooks

## What are custom signature hooks?

**Custom signature hooks** allow companies to trigger approval business logic based on parameters including transaction value, transaction volume, types of signature, and more. The business logic can be anything that helps the company and/or end user make good decisions during the signing request process.&#x20;

A couple examples are auto-approvals (eg, auto-approving transactions below a certain value or volume) and two-factor authentication (eg, requiring approval for transactions above a certain limit). Another option are security checks, either internal or external, to prevent malicious activity.

## Why use custom signature hooks?

There are a few common reasons developers use custom signature hooks. For example:&#x20;

* **Approving transactions** - Most developers will prompt a user for approval before signing a message from a dApp or Wallet Connect
* **Adding additional authentication** - Developers can add extra authentication steps such as MFA to riskier transactions&#x20;
* **Checking transaction sizes** - Developers can perform checks on transaction sizes to trigger specific authentication steps&#x20;

Custom signature hooks can be passive, invisible to the user, or active, designed into the frontend UX with which customers engage.&#x20;

A **passive** flow will generally be used to facilitate company goals, such as ensuring a user isn’t on a deny list. As such, the company can create a custom security check that is activated when a customer signs a transaction without them knowing. Transactions will continue as normal unless the user is found to be denied use of the app, at which time it will be rejected.\
\
An **active** flow is visible to the user and can be used to offer a superior user experience. For example, companies can trigger MFA for transactions above a certain size, an example we will dive into at the end of this guide.

## How do custom signature hooks work?

The Portal approval flow is driven by a series of event hooks that take place throughout the lifecycle of a Provider requests. These hooks are as follows:

* `portal_signingRequested` - Emitted by the Portal Provider when a new signing request is received by the Provider
* `portal_signingApproved` - Emitted by you when signing a given request is approved
* `portal_signingRejected` - Emitted by you when signing a given request is rejected

## Designing your flow

The expectation of this flow is that your code binds to the `portal_signingRequested` event using the following pattern:

```typescript
portal.provider.on('portal_signingRequested', ({ method, params }) => {
    // On approval
    portal.provider.emit('portal_signingApproved', { method, params })
    
    // On rejection
    portal.provider.emit('portal_signingRejected', { method, params })
})
```

As demonstrated by this example, you can implement any business logic you feel is required to make good decisions about what signing requests to process. You can prompt your user for approval, you can make requests to your backend to run some code remotely, etc. The only restriction of this flow is that when you have determined whether a signing request should be approved or rejected, you should emit either the `portal_signingRejected` or `portal_signingApproved` event.

_NOTE: When emitting these events, the Provider uses the `method` and `params` to determine if we are indeed approving or rejecting the correct request, so it is vital that you pass the `method` and `params` to your `emit()` calls._

### A more real-world example: MFA for large transactions

A common use case is to trigger additional approval from users for large transactions. The implementation of this using custom signature hooks might look something like this:

```typescript
portal.provider.on('portal_signingRequested', async ({ method, params }) => {    
    // Check the transaction size
    const isLargeTransaction = checkIfLargeTransaction(method, params)
    
    // Request MFA approval from user if large transaction
    let isMfaApproved = false
    if (isLargeTransaction) {
      isMfaApproved = await getMfaApproval()
    }
    
    // Approve transaction request if MFA was successful
    if (isMfaApproved) {
        portal.provider.emit('portal_signingApproved', { method, params }) 
    } else {
        portal.provider.emit('portal_signingRejected', { method, params })
    }
})
```



The outcome would look like something like this:

![](https://lh4.googleusercontent.com/IUz10Pt88VpjlIwlhswdWv9Uns9Aui_adLXQGFOufIitcLENyWELQcwAjR-uhoFIT5EsneS0ZW7vJ8khMr_2Vtn-2reeudbwmRMsTmRRW3S7yeMVI1UkoAZ5zcWSdKn9hCSrYB0271KH9hlLIB3ePfc)
