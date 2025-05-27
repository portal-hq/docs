---
description: >-
  This guide will walk you through how your Portal Wallets work with ERC20
  tokens, how to interface with ERC20 Token Contracts, and how to use the Portal
  Provider to transfer ERC20 tokens.
---

# Manage ERC20 tokens

## What are ERC20 tokens?

[ERC20 is an Ethereum standard](https://ethereum.org/en/developers/docs/standards/tokens/erc-20/) that defines a unified interface for standard token behavior in a smart contract. This allows for custom tokens to be created beyond the native token for that chain (ETH in the case of Ethereum). Examples of ERC20 tokens include stablecoins like [USDT](https://etherscan.io/token/0xdac17f958d2ee523a2206206994597c13d831ec7) & [USDC](https://etherscan.io/token/0xa0b86991c6218b36c1d19d4a2e9eb0ce3606eb48) or Defi protocol tokens like [UNI](https://etherscan.io/token/0x1f9840a85d5af5bf1d1762f925bdaddc4201f984).

The ERC20 standard defines just a few Solidity functions that these contracts must implement:

```solidity
function name() public view returns (string)
function symbol() public view returns (string)
function decimals() public view returns (uint8)
function totalSupply() public view returns (uint256)
function balanceOf(address _owner) public view returns (uint256 balance)
function transfer(address _to, uint256 _value) public returns (bool success)
function transferFrom(address _from, address _to, uint256 _value) public returns (bool success)
function approve(address _spender, uint256 _value) public returns (bool success)
function allowance(address _owner, address _spender) public view returns (uint256 remaining)
```

In order to send or check the balance of your ERC20 tokens you must interact with the smart contract. Depending on if those functions are **Read-Only** or **State Changing** you interact with these functions in two different ways.

#### Read-Only Functions

Notice that these functions (shown as their [Solidity](https://ethereum.org/en/developers/docs/smart-contracts/languages/#solidity) function signatures) all contain the `view` keyword. This means that these functions can be invoked using [`eth_call` RPC method](https://ethereum.org/en/developers/docs/apis/json-rpc/#eth_call) and do **not** change the state of the blockchain. This also means that calling these functions does not require gas.&#x20;

```solidity
function name() public view returns (string)
function symbol() public view returns (string)
function decimals() public view returns (uint8)
function totalSupply() public view returns (uint256)
function balanceOf(address _owner) public view returns (uint256 balance)
function allowance(address _owner, address _spender) public view returns (uint256 remaining)
```

#### State Changing Functions

These functions do not contain `view` keyword, which means they **do** change the state of the blockchain. This requires sending a signed transaction to the blockchain, along with gas, to update the global state. These functions are invoked using the [`eth_sendTransaction` RPC method.](https://ethereum.org/en/developers/docs/apis/json-rpc/#eth_sendtransaction)

```solidity
function transfer(address _to, uint256 _value) public returns (bool success)
function transferFrom(address _from, address _to, uint256 _value) public returns (bool success)
function approve(address _spender, uint256 _value) public returns (bool success)
```

## Transferring ERC20 with your Portal Wallet

The Portal SDK supports the processing of web3 transactions using the Portal Provider. Because of this, working with ERC20 tokens using your Portal Wallet is very similar to working with ETH itself. The main difference is that you'll need to interface with the ERC20 token's contract in order to build the appropriate transaction before signing with the Portal Provider.

This can be thought of in three basic steps:

1. Gathering inputs for generating a transaction
2. Using Web3.swift to generate an ERC20 transaction
3. Using Provider to sign and send the transaction

### Gathering inputs for generating a transaction

In this example, we'll be looking at transferring ERC20 tokens from your Portal Wallet to another wallet (sending ERC20 funds to another wallet). In order to accomplish this, you'll need your application to manage certain inputs required to generate the transaction: the receiver's address, and the token address for the ERC20 token being sent. \
\
In this case, we'll use Uniswap (UNI) as an example, so we'll be using the `tokenAddress` for UNI ([`0x1f9840a85d5aF5bf1D1762F925BDADdC4201F984`](https://etherscan.io/address/0x1f9840a85d5aF5bf1D1762F925BDADdC4201F984)).

### Using Web3.swift to generate an ERC20 transaction

The [`Web3.swift` library](https://github.com/Boilertalk/Web3.swift) allows you to easily interface with token contracts to build the appropriate transaction payload for an ERC20 transaction without needing to manually build `eth_call` requests.  We'll be using it for this purpose in this example.&#x20;

The following code demonstrates how to use the `Web3.swift` library to access an instance of the UNI Contract and generating a basic `send` transaction.

{% hint style="info" %}
In order to use Web3.swift in your application, you'll need to add the package as a dependency. More details on adding Web3.swift to your project can be found [here](https://github.com/Boilertalk/Web3.swift#installation).
{% endhint %}

```swift
import Web3
import Web3ContractABI

// Get the Gateway URL from the Portal Provider
guard let gatewayUrl = portal?.getRpcUrl(forChainId: "eip155:11155111") else {
  // Add application logic to handle this scenario
  return
}

// Get the Portal Wallet address
guard let address = portal?.address else {
  // Add application logic to handle this scenario
  return
}

// Create an instance of Web3.swift
let web3 = Web3(rpcURL: gatewayUrl)

// Get an instance of the UNI Contract to generate transactions
let contract = web3.eth.Contract(
  type: GenericERC20Contract.self,
  address: "0x1f9840a85d5aF5bf1D1762F925BDADdC4201F984" // UNI token address
)

// Get the correct decimal conversion for the UNI smart contract
let conversionFactor: Double = 1e18
let value = BigUInt(amount * conversionFactor)


// Generate the ERC20 transaction using the UNI Contract
guard let tx = contract
    .transfer(
      // eip55 is dependent on the wallet receiving funds
      to: try EthereumAddress(hex: toAddress, eip55: false)
      value: value
    )
    .createTransaction(
      nonce: 0, // Here you'd want to provide the actual nonce for this transaction
      gasPrice: EthereumQuantity(quantity: 21.gwei),
      maxFeePerGas: nil,
      maxPriorityFeePerGas: nil,
      gasLimit: 100_000,
      // eip55 will be false for Portal MPC Wallets
      from: try EthereumAddress(hex: address, eip55: false),
      value: 0, // This is the value of the native token, not the ERC20 amount
      accessList: [:],
      transactionType: .legacy
    ) else { 
      // Add application logic to handle this scenario
      return 
    }
```

### Using Portal to sign and send the transaction

Once you've generated a transaction using the token Contract, you can use it to create a request using your Portal instance to sign and send the transaction.&#x20;

```swift
Task {
  do {
    // Ethereum Sepolia
    let chainId = "eip155:11155111" // CAIP-2 format.

    // Obtain the eip155 address of the user's wallet.
    guard let eip155Address = await portal.getAddress(chainId) else {
      throw PortalExampleAppError.addressNotFound()
    }

    // Create the transaction object.
    let transaction = [
      "from": eip155Address,
      "to": "0xRecipientAddress",  // Sending the tx to the smart contract to execute the smart contract function call in the data field
      "gasPrice": tx.gasPrice.hex(),
      "value": "0x0",
      "data": tx.data.hex(), 
      "nonce": tx.nonce.hex()
    ]

    // Make the eth_sendTransaction request.
    let requestResponse = try await portal.request(
      chainId,
      withMethod: .eth_sendTransaction,
      andParams: [transaction]
    )
  }
}
```

## The sendERC20Token() function

Now that we've covered the steps required to sign and send a transaction using ERC20 tokens, let's look at an example of a full function for sending ERC20 tokens to another wallet.

<pre class="language-swift"><code class="lang-swift">func sendERC20Token(tokenAddress: String, toAddress: String, amount: Int64) throws {
  // Get the Gateway URL from the Portal Provider
  guard let gatewayUrl = portal?.getRpcUrl(forChainId: "eip155:11155111") else {
    return
  }

  // Ensure the Portal Address is set
  guard let address = portal?.address else {
    // Probably throw an error here
    return
  }

  // Initialize Web3
  let web3 = Web3(rpcURL: gatewayUrl)

  do {
    // Transform the receiver address
    let receiverAddress = try EthereumAddress(hex: toAddress, eip55: false) // eip55 is dependent on the wallet receiving funds

    // Transfor the sender address
    let senderAddress = try EthereumAddress(hex: address, eip55: false) // eip55 will be false for Portal MPC Wallets

    // Transform the token address
    let tokenContractAddress = try EthereumAddress(hex: tokenAddress, eip55: true) // eip55 will be true for Token Contracts

    // Initialize the token contract
    let contract = web3.eth.Contract(
      type: GenericERC20Contract.self,
      address: tokenContractAddress
    )

    // Get the correct decimal conversion for the UNI smart contract
<strong>    let conversionFactor: Double = 1e18 // In the case of UNI it is 18.
</strong>    let value = BigUInt(amount * conversionFactor)

    // Generate the transaction for Portal to execute
    guard let tx = contract
      .transfer(to: receiverAddress, value: value)
      .createTransaction(
        nonce: 0, // Here you'd want to provide the actual nonce for this transaction
        gasPrice: EthereumQuantity(quantity: 21.gwei),
        maxFeePerGas: nil,
        maxPriorityFeePerGas: nil,
        gasLimit: 100_000,
        from: senderAddress,
        value: 0,
        accessList: [:],
        transactionType: .legacy
      ) else { return }

    Task {
      do {
        // Ethereum Sepolia
        let chainId = "eip155:11155111" // CAIP-2 format.
    
        // Obtain the eip155 address of the user's wallet.
        guard let eip155Address = await portal.getAddress(chainId) else {
          throw PortalExampleAppError.addressNotFound()
        }
    
        // Create the transaction object.
        let transaction = [
          "from": eip155Address,
          "to": tokenAddress,
          "gasPrice": tx.gasPrice.hex(),
          "value": "0x0",
          "data": tx.data.hex(), 
          "nonce": tx.nonce.hex()
        ]
    
        // Make the eth_sendTransaction request.
        let requestResponse = try await portal.request(
          chainId,
          withMethod: .eth_sendTransaction,
          andParams: [transaction]
        )
      }
    }
  } catch {
    // Handle errors
  }
}
</code></pre>
