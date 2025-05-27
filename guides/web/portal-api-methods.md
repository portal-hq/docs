---
description: >-
  Portal API's convenient helper methods are designed to streamline data
  retrieval from the Portal REST API.
---

# Portal API methods

#### **`portal.getClient`**

Gets all of the Portal client's relevant details, such as their wallet addresses, their signing share pairs' statuses, their backup share pairs' statuses, and more.

```typescript
const portalClient = await portal.getClient()
```

<details>

<summary>Example response</summary>

```json
{
  "createdAt": "2025-04-04T21:41:52.354Z",
  "custodian": {
    "id": "id",
    "name": "My Organization"
  },
  "ejectedAt": null,
  "environment": {
    "id": "id",
    "backupWithPortalEnabled": true,
    "isMultiBackupEnabled": true,
    "name": "Production"
  },
  "id": "clientId",
  "isAccountAbstracted": false,
  "metadata": {
    "namespaces": {
      "solana": {
        "address": "CDjKJBeidJ7KqR4uuQvfXSbeNUNBzZ69mSauXHKAUZqZ",
        "curve": "ED25519"
      },
      "stellar": {
        "address": "GCTLGX2EFNK2ICF6N27MALUNO6NNZBI3MD47MI5ULET34DPMIG6YQPPQ",
        "curve": "ED25519"
      },
      "bip122": {
        "address": "",
        "curve": "SECP256K1",
        "bitcoin": {
          "p2wpkh": {
            "mainnet": "bc1qmt4g8tehe5ap35l7n2hm264jdgp0yq3feftt8d",
            "testnet": "tb1qmt4g8tehe5ap35l7n2hm264jdgp0yq3fn0scu7"
          }
        }
      },
      "eip155": {
        "address": "0xfb03795d3a61db4874bc247bfe1e9363c2384437",
        "curve": "SECP256K1"
      },
      "tron": {
        "address": "TYrSmbGbsuVQB6HfraicoNK33JzAUcJZa9",
        "curve": "SECP256K1"
      }
    }
  },
  "wallets": [
    {
      "createdAt": "2025-04-04T22:02:19.127Z",
      "curve": "ED25519",
      "ejectableUntil": null,
      "id": "id",
      "backupSharePairs": [],
      "signingSharePairs": [
        {
          "createdAt": "2025-04-04T22:02:19.131Z",
          "id": "id",
          "status": "completed"
        }
      ],
      "publicKey": "{\"x\":\"123\",\"y\":\"123\"}"
    },
    {
      "createdAt": "2025-04-04T22:02:18.474Z",
      "curve": "SECP256K1",
      "ejectableUntil": null,
      "id": "id",
      "backupSharePairs": [],
      "signingSharePairs": [
        {
          "createdAt": "2025-04-04T22:02:18.478Z",
          "id": "id",
          "status": "completed"
        }
      ],
      "publicKey": "{\"x\":\"123\",\"y\":\"123\"}"
    }
  ]
}
```

</details>

#### **`portal.getAssets`**

Fetches a list of non-fungible tokens (NFTs). The response is an array of objects where each object represents a unique NFT.

```typescript
const assets = portal.getAssets(
  'solana:EtWTRABZaYq6iMfeYKouRu166VU2xqa1', // CAIP-2 chain ID
  false // (optional) include NFTs
)
```

<details>

<summary>Example response</summary>

```json
{
  "nativeBalance": {
    "balance": "0.00501002",
    "decimals": 9,
    "name": "Solana",
    "rawBalance": "5010020",
    "symbol": "SOL",
    "metadata": {}
  },
  "tokenBalances": [
    {
      "balance": "0.01",
      "decimals": 6,
      "name": "USD Coin",
      "rawBalance": "10000",
      "symbol": "USDC",
      "metadata": {
        "tokenAccountAddress": "6sVvsCBKYDWrZkE1txPU4PtcwfUnw4UDN1S78Eo7Eizm",
        "tokenMintAddress": "EPjFWdd5AufqSSqeM2qN1xzybapC8G4wEGGkZwyTDt1v"
      }
    },
    {
      "balance": "0.01",
      "decimals": 6,
      "name": "Tether USD",
      "rawBalance": "10000",
      "symbol": "USDT",
      "metadata": {
        "tokenAccountAddress": "aBJYKdVpZXPVxQ7uNRk3SPWEdtARm63WEchGxNMzCa9",
        "tokenMintAddress": "Es9vMFrzaCERmJfrF4H2FYD4KCoNkY11McCe8BenwNYB"
      }
    },
    {
      "balance": "0.01",
      "decimals": 6,
      "name": "PayPal USD",
      "rawBalance": "10000",
      "symbol": "PYUSD",
      "metadata": {
        "tokenAccountAddress": "CrdsCZTqvEr6nL7sC9St2RfMnVeYbitGqoEcvaZpFuk9",
        "tokenMintAddress": "2b1kV6DkPAnxd5ixfnxCpjxmKwqjjaYmCZfHsFu24GXo"
      }
    }
  ]
}
```

</details>

#### **`portal.buildTransaction`**

Creates an unsigned eip155/solana transaction for transferring assets to another address on a specific chain. You can then use this unsigned eip155/solana transaction to sign and submit the eip155/solana transaction.

```typescript
const txDetails = await portal.buildTransaction(
  'sepolia', // Friendly chain or CAIP-2 chainId
  '0xDestinationAddress',
  'USDC', // Friendly token name, or token address (mint address for Solana)
  '1', // Sends 1 USDC
)
```

<details>

<summary>Example response (Sending USDC on Ethereum Sepolia)</summary>

```json
{
  "transaction": {
    "from": "0xc690a7F8F02D551287C049997C9A003Ffe65CAD5",
    "to": "0xf08A50178dfcDe18524640EA6618a1f965821715",
    "data": "0xa9059cbb000000000000000000000000dfd8302f44727a6348f702ff7b594f127de3a90200000000000000000000000000000000000000000000000000000000000186a0"
  },
  "metadata": {
    "amount": "0.1",
    "fromAddress": "0xc690a7F8F02D551287C049997C9A003Ffe65CAD5",
    "toAddress": "0xdFd8302f44727A6348F702fF7B594f127dE3A902",
    "tokenAddress": "0xf08A50178dfcDe18524640EA6618a1f965821715",
    "tokenDecimals": 6,
    "rawAmount": "100000"
  }
}
```

</details>

<details>

<summary>Example response (Sending USDC on Solana Devnet)</summary>

```json
{
  "transaction": "AQAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAABAAUIYOC3CyvldvId8bF28rNypn1C7OBqvhXqEYXLKLeWh6ou38jGPjCqlLFY3TlPbCHB6R+g/1F1WqGrCV4DL8GB9XhhA/bXqsIjAl2JBhtZTr77wOyNjGsBM3b+kC2y56V7AAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAA7RCyzkSFX8TqTPQE0KC0DK1/+zQGi2/G3eQYI3wAupwHVrOJiBDitgxRzQDkrbLih58c3zcw12JO80+paiyM8jJclj04kifG7PRApFI4NgwtaE5na/xCEBI572Nvp+FkG3fbh12Whk9nL4UbO63msHLSF7V9bN5E6jPWFfv8AqbQF5ecBPqhVgg4ZLgfMMY9c0oT1wlGamtcEtNX/+C0iAgYGAAEFBAMHAAcDAgEACQOghgEAAAAAAA==",
  "metadata": {
    "amount": "0.1",
    "fromAddress": "7XAsvsVfvzQu61gHpa1QDMFF5Zsz5DrU6u2LAizNrXPX",
    "toAddress": "8APEEA4SHrfGteABQcUmH2yEHy7nEe8DqgvmKGAStHR",
    "tokenMintAddress": "4zMMC9srt5Ri5X14GAgXhaHii3GnPAEERYPJgZJDncDU",
    "tokenDecimals": 6,
    "tokenProgramId": "TokenkegQfeZyiNwAJbNbGKPFXCWuBvf9Ss623VQ5DA",
    "tokenExtensions": [],
    "rawAmount": "100000",
    "lastValidBlockHeight": "342868333",
    "serializedTransactionBase64Encoded": "AQAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAABAAUIYOC3CyvldvId8bF28rNypn1C7OBqvhXqEYXLKLeWh6ou38jGPjCqlLFY3TlPbCHB6R+g/1F1WqGrCV4DL8GB9XhhA/bXqsIjAl2JBhtZTr77wOyNjGsBM3b+kC2y56V7AAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAA7RCyzkSFX8TqTPQE0KC0DK1/+zQGi2/G3eQYI3wAupwHVrOJiBDitgxRzQDkrbLih58c3zcw12JO80+paiyM8jJclj04kifG7PRApFI4NgwtaE5na/xCEBI572Nvp+FkG3fbh12Whk9nL4UbO63msHLSF7V9bN5E6jPWFfv8AqbQF5ecBPqhVgg4ZLgfMMY9c0oT1wlGamtcEtNX/+C0iAgYGAAEFBAMHAAcDAgEACQOghgEAAAAAAA==",
    "serializedTransactionBase58Encoded": "4c5ErVEQWyXMS7yGF2TJaJBf7Vk3bkdaXx6LrCS3xkeLUPWnJMde1CmjFFxEEwbwLjX6N2u2RNpNFhTPML7ow4ZDpUPB5NkxQpiNawgUhYUj9bBDeFQfXjYzFFjs7fVD6jRJzmqQSCrdWFiFP2rwMiHXiZqj4MFY7HFwnfymJp3zaXxfG2VxEXVJU5ii9ddvjynNg7jJvhZ4ysUpjTJkWAVdj3UCJAkaqjZoeapmFVm73113uAJV2arPqnNtoouj8vcHYNKLdA1NnG3T8ZSAeePWTmruEH9uCtMpXgiNefgwvA4szZ6DNKDCAREUWXH38cLH3XQjgjLDdRXmZ8xQHYacuwmtXVVjdUzV3rcVuC1NM4YmHkRnw8nfULm4PmrZoBxchnS7vNXxXHBAd9ysg1VQp5v21oBsgjegZMrxfo8wzr3HHvjXoBw3LeBJ1raWYAAa1S32oE19Hvqhm3EKFaF9txb46j4kHVDqbmPuVQDCzAHAjMZHiALqL2MCQJGimS86gfq7d",
    "unsignedTransactionMessage": {
      "signatures": null,
      "message": {
        "accountKeys": [
          "7XAsvsVfvzQu61gHpa1QDMFF5Zsz5DrU6u2LAizNrXPX",
          "49yfs2fpy9HhVRU6icApq3QvNdYRBrtcRJojTT7jnUAx",
          "96uj2xF4aT7JfkvsYXjeejaFMtzPYQwyMDti29QXWeux",
          "11111111111111111111111111111111",
          "4zMMC9srt5Ri5X14GAgXhaHii3GnPAEERYPJgZJDncDU",
          "8APEEA4SHrfGteABQcUmH2yEHy7nEe8DqgvmKGAStHR",
          "ATokenGPvbdGVxr1b2hvZbsiqW5xWH25efTNsLJA8knL",
          "TokenkegQfeZyiNwAJbNbGKPFXCWuBvf9Ss623VQ5DA"
        ],
        "header": {
          "numRequiredSignatures": 1,
          "numReadonlySignedAccounts": 0,
          "numReadonlyUnsignedAccounts": 5
        },
        "instructions": [
          {
            "programIdIndex": 6,
            "accounts": [
              0,
              1,
              5,
              4,
              3,
              7
            ],
            "data": ""
          },
          {
            "programIdIndex": 7,
            "accounts": [
              2,
              1,
              0
            ],
            "data": "3gJqkocMWaMm"
          }
        ],
        "recentBlockhash": "D7jbugcuXsPj7zejJ5i3xSHQU9Jj1mpsVw7hUTawLcrd"
      }
    }
  }
}
```

</details>

***

Looking for more functionality? Explore our other Client API endpoints [here](../../reference/client-api/).
