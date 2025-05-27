# Chain ID formatting

## Overview

In the world of blockchain technology, it's common to encounter different formats for representing chain IDs. While the traditional EVM chain ID format, like `1` for Ethereum mainnet, is widely recognized, [the CAIP-2 format](https://github.com/ChainAgnostic/CAIPs/blob/main/CAIPs/caip-2.md) offers a more comprehensive way to denote chain IDs across multiple curves. **At Portal, we primarily use the CAIP-2 format for chain IDs for this reason.** This document will clarify the differences and provide examples for Ethereum, Solana, and other EVM chains.

### Traditional EVM Chain ID Format

The traditional EVM chain ID format is a straightforward numerical representation used by Ethereum Virtual Machine (EVM) compatible chains. Here are some examples:

* **Ethereum Mainnet**: `1`
* **Optimism**: `10`
* **Polygon**: `137`
* **Arbitrum**: `42161`

### CAIP-2 Chain ID Format

The CAIP-2 ([Chain Agnostic Improvement Proposal 2](https://github.com/ChainAgnostic/CAIPs/blob/main/CAIPs/caip-2.md)) format provides a more structured way to represent chain IDs, including the namespace and reference, which helps in uniquely identifying different blockchain networks. The format is `namespace:reference`.

#### Examples:

* **Solana:** `solana:5eykt4UsFv8P8NJdTREpY1vzqKqZKvdp`&#x20;
* **Ethereum Mainnet**: `eip155:1`
* **Optimism**: `eip155:10`
* **Polygon**: `eip155:137`
* **Arbitrum**: `eip155:42161`
* **Tron:** `tron:mainnet`
* **Stellar:** `stellar:pubnet`
* **Bitcoin:** `bip122:000000000019d6689c085ae165831e93-p2wpkh`

{% hint style="info" %}
The only exception to our chains adhering to the CAIP-2 standardized format is Bitcoin networks, which require special consideration due to the variety of address types they support.
{% endhint %}

### Conversion Table

<table><thead><tr><th width="244.984375">Chain</th><th>CAIP-2 Format</th></tr></thead><tbody><tr><td>Solana Mainnet</td><td><code>solana:5eykt4UsFv8P8NJdTREpY1vzqKqZKvdp</code></td></tr><tr><td>Solana Devnet</td><td><code>solana:EtWTRABZaYq6iMfeYKouRu166VU2xqa1</code></td></tr><tr><td>Ethereum Mainnet</td><td><code>eip155:1</code></td></tr><tr><td>Optimism</td><td><code>eip155:10</code></td></tr><tr><td>Polygon</td><td><code>eip155:137</code></td></tr><tr><td>Arbitrum</td><td><code>eip155:42161</code></td></tr><tr><td>Base</td><td><code>eip155:8453</code></td></tr><tr><td>Base Sepolia</td><td><code>eip155:84532</code></td></tr><tr><td>Celo</td><td><code>eip155:42220</code></td></tr><tr><td>Celo Alfajores Testnet</td><td><code>eip155:44787</code></td></tr><tr><td>Stellar</td><td><code>stellar:pubnet</code></td></tr><tr><td>Stellar Testnet</td><td><code>stellar:testnet</code></td></tr><tr><td>Tron</td><td><code>tron:mainnet</code></td></tr><tr><td>Tron Nile Testnet</td><td><code>tron:nile</code></td></tr><tr><td>Tron Shasta Testnet</td><td><code>tron:shasta</code></td></tr><tr><td>Bitcoin (p2wpkh)</td><td><code>bip122:000000000019d6689c085ae165831e93-p2wpkh</code></td></tr><tr><td>Bitcoin Testnet (p2wpkh)</td><td><code>bip122:000000000933ea01ad0ee984209779ba-p2wpkh</code></td></tr></tbody></table>

### Understanding the CAIP-2 Format

This includes the namespace (e.g. `eip155` for Ethereum-based chains) followed by the chain-specific identifier. It is designed to be chain-agnostic and supports a wider variety of blockchain networks beyond just EVM-compatible ones.

### Why CAIP-2?

The CAIP-2 format is essential for environments where multiple blockchain networks need to be identified and interacted with. It provides a standardized way to refer to chains, reducing ambiguity and enhancing interoperability across different platforms and applications.

***

Understanding both the traditional EVM chain ID format and the CAIP-2 format is crucial for interacting with multi-curve environments. If you have any questions or need further clarification, please reach out to our support team!
