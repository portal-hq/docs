---
title: portal.api.buildEip155Trans...
---

### **`portal.api.buildEip155Transaction`**

Creates an unsigned transaction for transferring assets to another address on EIP155 chain. You can then use this unsigned transaction to sign and submit the transaction.

```kotlin
val buildTransactionParam = BuildTransactionParam(
    to = "EIP155 address",
    token = "ETH",
    amount = "0.001"
)
val eip155TransactionResult = portal.api.buildEip155Transaction(chainId, buildTransactionParam)
```

<details>

<summary>Example API response</summary>

The response includes the unsigned transaction details and metadata. The structure of the response differs depending on whether the chain is Solana or an EIP-155 chain (e.g., Ethereum, Polygon, Base).

```json
{
  "transaction": {
    "from": "0x54968898742c08da211a1cd355447cd1f37f0649",
    "to": "0xa0b86991c6218b36c1d19d4a2e9eb0ce3606eb48",
    "data": "0xa9059cbb000000000000000000000000dfd8302f44727a6348f702ff7b594f127de3a9020000000000000000000000000000000000000000000000000000000000002710"
  },
  "metadata": {
    "amount": "0.01",
    "fromAddress": "0x54968898742c08da211a1cd355447cd1f37f0649",
    "toAddress": "0xdFd8302f44727A6348F702fF7B594f127dE3A902",
    "tokenAddress": "0xa0b86991c6218b36c1d19d4a2e9eb0ce3606eb48",
    "tokenDecimals": 6,
    "rawAmount": "10000"
  }
}
```

</details>

### **`portal.api.buildSolanaTransaction`**

Creates an unsigned transaction for transferring assets to another address on Solana chain. You can then use this unsigned transaction to sign and submit the transaction.

```kotlin
val buildTransactionParam = BuildTransactionParam(
    to = "Solana address",
    token = "SOL",
    amount = "0.001"
)
val solanaTransactionResult = portal.api.buildSolanaTransaction(chainId, buildTransactionParam)
```

<details>

<summary>Example API response</summary>

The response includes the unsigned transaction details and metadata. The structure of the response differs depending on whether the chain is Solana or an EIP-155 chain (e.g., Ethereum, Polygon, Base).

```json
{
  "transaction": "AQAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAABAAUIXIcyVRnqtuJq4VAVZQPX4M5MFtpcAzLmaNCC62aV/5Ftzsmzlp2NKI8Dlj+MKJ7LBQsJ9iiXAult3S/Y/4YTRgjDL4wY8hJ13iBEAciC0uIGJEPr+VGVf2qQqxBytKhwAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAB1aziYgQ4rYMUc0A5K2y4oefHN83MNdiTvNPqWosjPIyXJY9OJInxuz0QKRSODYMLWhOZ2v8QhASOe9jb6fhZxvp6877brTo9ZfNqq8l0MbG75MLS9uDkfKYCA0UvXWEG3fbh12Whk9nL4UbO63msHLSF7V9bN5E6jPWFfv8AqSgizMJvj1c1WJPJuxDQWi6VG4dp6Fq930FKOQcI25XjAgUGAAIEBgMHAAcDAQIACQMQJwAAAAAAAA==",
  "metadata": {
    "amount": "0.01",
    "fromAddress": "7EC5TEEAcDseLH6PyqKkPiBasVnsdmrRbRHjN3zkkHFW",
    "toAddress": "8APEEA4SHrfGteABQcUmH2yEHy7nEe8DqgvmKGAStHR",
    "tokenMintAddress": "EPjFWdd5AufqSSqeM2qN1xzybapC8G4wEGGkZwyTDt1v",
    "tokenDecimals": 6,
    "tokenProgramId": "TokenzQdBNbLqP5VEhdkAS6EPFLC1PHnBqCXEpPxuEb",
    "tokenExtensions": [
      "ConfidentialTransferAccount",
      "ConfidentialTransferMint",
      "CpiGuard",
      "DefaultAccountState",
      "GroupMemberPointer",
      "GroupPointer",
      "ImmutableOwner",
      "InterestBearingConfig",
      "MemoTransfer",
      "MetadataPointer",
      "MintCloseAuthority",
      "NonTransferable",
      "NonTransferableAccount",
      "PermanentDelegate",
      "TokenGroup",
      "TokenGroupMember",
      "TokenMetadata",
      "TransferFeeAmount",
      "TransferFeeConfig",
      "TransferHook",
      "TransferHookAccount",
      "Uninitialized",
    ],
    "rawAmount": "10000",
    "lastValidBlockHeight": "260129177",
    "serializedTransactionBase64Encoded": "AQAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAABAAUIXIcyVRnqtuJq4VAVZQPX4M5MFtpcAzLmaNCC62aV/5Ftzsmzlp2NKI8Dlj+MKJ7LBQsJ9iiXAult3S/Y/4YTRgjDL4wY8hJ13iBEAciC0uIGJEPr+VGVf2qQqxBytKhwAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAB1aziYgQ4rYMUc0A5K2y4oefHN83MNdiTvNPqWosjPIyXJY9OJInxuz0QKRSODYMLWhOZ2v8QhASOe9jb6fhZxvp6877brTo9ZfNqq8l0MbG75MLS9uDkfKYCA0UvXWEG3fbh12Whk9nL4UbO63msHLSF7V9bN5E6jPWFfv8AqSgizMJvj1c1WJPJuxDQWi6VG4dp6Fq930FKOQcI25XjAgUGAAIEBgMHAAcDAQIACQMQJwAAAAAAAA==",
    "serializedTransactionBase58Encoded": "4c5ErVEQWyXMS7yGF2TJaJBf7Vk3bkdaXx6LrCS3xkeLUPWnJMde1CmjFFxEEwbwLjX6N2u2RNpNFhTPML7ow4ZDpUPB5NeocD5GDDPQb9Sy7BPTEMQH7QGXEDx8pfYmk7Z3yYTi7Wz6CsgoKgwhQTGRqCr6mhacTYVAwUpiptsASucz6hqkffZx7P96UPrU3vwL1QMYjLXKYYduuiqket5bt6uCRyF5cchdjir2LPTaqdLsKehDsFPh6ko45vJ4QuXZmeMyeF1fxECfjoj1N8wazVL57Ct9t5CKWXcWrXJkTzksGwMJ7CJQCUrMT7CGswzd9pwj55Lxcq55KcsGvTsu9NEo6dCs4ZFDXuXBexg7C3XNTf5GgsqBEi5MQVcZicUQb3EdS1j8ShTiNyiVkG2JEkvHQc4FZGQvSqrjgaUoi7FSfMRVeB6vWWwtnUfsGea1UcarfrFN8BcvuZrHNA5pyfC89iJpm55GRqgZFFupCCoWG3sJ47spU19oGEoAYD4pw6TJw",
    "unsignedTransactionMessage": {
      "signatures": null,
      "message": {
        "accountKeys": [
          "7EC5TEEAcDseLH6PyqKkPiBasVnsdmrRbRHjN3zkkHFW",
          "8PeKkecAjt1VgQNg1acdhgrajDJDB9uEDXP6MvRC8WAZ",
          "bCtKaS7SecyaiTDL388bnLyMmgFvGdxmLmeEhcoq5Eo",
          "11111111111111111111111111111111",
          "8APEEA4SHrfGteABQcUmH2yEHy7nEe8DqgvmKGAStHR",
          "ATokenGPvbdGVxr1b2hvZbsiqW5xWH25efTNsLJA8knL",
          "EPjFWdd5AufqSSqeM2qN1xzybapC8G4wEGGkZwyTDt1v",
          "TokenkegQfeZyiNwAJbNbGKPFXCWuBvf9Ss623VQ5DA"
        ],
        "header": {
          "numRequiredSignatures": 1,
          "numReadonlySignedAccounts": 0,
          "numReadonlyUnsignedAccounts": 5
        },
        "instructions": [
          {
            "programIdIndex": 5,
            "accounts": [
              0,
              2,
              4,
              6,
              3,
              7
            ],
            "data": ""
          },
          {
            "programIdIndex": 7,
            "accounts": [
              1,
              2,
              0
            ],
            "data": "3GAG5eogvTjV"
          }
        ],
        "recentBlockhash": "3hg5wAbN1FYh5fGANDpCgWMDG1NPMYmNwdDiYTqinFUv"
      }
    }
  }
}
```

</details>
