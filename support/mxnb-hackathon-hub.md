---
description: All of the information you need to get started for a hackathon using Portal!
---

# MXNB Hackathon Hub

The [MXNB Hackathon](https://lp.mxnb.mx/hackathon) is a global online challenge for builders who want to reshape the future of finance. You’ll design, build, and launch real-world payment and DeFi apps using MXNB, the leading Mexican peso stablecoin, the Bitso Business API suite, and Portal's web3 SDKs.

## Getting Started

To get started head to the`portalhq` hackathon discord channel or to the [Portal Community Slack](https://join.slack.com/t/portalcommunity/shared_invite/zt-2obhgakbb-TGGNAutwgHF4VCGbr0tCYA) to request access to the [Portal Admin Dashboard](https://app.portalhq.io/login) where you can get credentials to start developing with Portal.

Once you have access you can get started with one of our hackathon kit repositories for the SDK you would like to use:

* Android: [https://github.com/portal-hq/portal-hackathon-kit-android-mxnb](https://github.com/portal-hq/portal-hackathon-kit-android-mxnb)
* iOS: [https://github.com/portal-hq/portal-hackathon-kit-ios-mxnb](https://github.com/portal-hq/portal-hackathon-kit-ios-mxnb)
* React Native: [https://github.com/portal-hq/portal-hackathon-kit-react-native-mxnb](https://github.com/portal-hq/portal-hackathon-kit-react-native-mxnb)
* Web (Typescript): [https://github.com/portal-hq/portal-hackathon-kit-web-mxnb](https://github.com/portal-hq/portal-hackathon-kit-web-mxnb)
* Enclave MPC API: [https://github.com/portal-hq/portal-hackathon-kit-api-mxnb](https://github.com/portal-hq/portal-hackathon-kit-api-mxnb)

## What is Portal?

**Portal** is an [MPC wallet](../resources/portals-mpc-architecture.md) provider and web3 developer platform. You can use Portal to create embedded MPC wallets for users and the Portal SDK to build out the web3 functionality for your application.

In our example repos, you can use Portal to create wallets that can hold and transfer MXNB (among many other tokens) over the Arbitrum network.

## Documentation

### Portal SDK Reference

Portal's SDKs have several pieces of core functionality.

* [Generating a Wallet](https://docs.portalhq.io/guides/web/create-a-wallet): This function creates MPC key shares on your local device and the Portal servers. These key shares support all EVM chains and Solana.
* [Signing a Transaction](https://docs.portalhq.io/guides/web/sign-a-transaction): This function signs a provided transaction, and can broadcast that transaction to a chain when an RPC gateway URL is provided.
* [Signature Hooks](https://docs.portalhq.io/guides/web/add-custom-signature-hooks): By default this repo will submit a transaction without prompting a user, but you can use signature hooks to build a prompt for users before submitting a transaction for signing.

### Portal APIs

Portal supplies several APIs for simplifying your development.

* [Get Assets](https://docs.portalhq.io/reference/client-api/v3-endpoints#get-assets-by-chain): This endpoint returns a list of fungible asset (native, ERC-20, and SPL tokens) associated with your client for a given chain.
* [Get NFTs](https://docs.portalhq.io/reference/client-api/v3-endpoints#get-nft-assets-by-chain): This endpoint returns a list of the NFTs associated with your client for a given chain.
* [Get Transactions](https://docs.portalhq.io/reference/client-api/v3-endpoints#get-transactions-by-chain): This endpoint returns a list of the historic transactions associated with your client for a given chain.
* [Build a Transaction - Send Asset](https://docs.portalhq.io/reference/client-api/v3-endpoints#build-a-send-asset-transaction): This endpoint builds a formatted transaction to send a fungible asset (native, ERC-20, and SPL tokens) for a given chain.
* [Evaluate a Transaction](https://docs.portalhq.io/reference/client-api/v3-endpoints#evaluate-a-transaction): This endpoint can simulate a transaction and/or scan a transaction for security concerns.

### MXNB Documentation

* [MXNB Website](https://mxnb.mx/)
* [MXNB Whitepaper](https://mxnb.mx/whitepaper.pdf)

### Faucets

* [MXNB Faucet](https://buildwithjuno.com/en-US/faucet)
