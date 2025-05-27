# Create a wallet

To create a wallet we are going to use the `/v1/generate` endpoint to create a new wallet and then use the `/v3/clients/me/signing-share-pairs` endpoint to confirm successful storage of the generated signing share. Worth nothing that the generate endpoint returns MPC shares for both the `SECP256K1` curve (compatible with EVM and Bitcoin chains) and the `ED25519` curve (compatible with Solana). In other words, you only need to make one generate request to get wallets for multiple chains!

### Steps

1. From your application, make an HTTP POST request to `https://mpc-client.portalhq.io/v1/generate` using the `clientApiKey` you received in the previous step as the Bearer token.

```bash
curl -X POST 'https://mpc-client.portalhq.io/v1/generate' \
    -H 'Content-Type: application/json' \
    -H 'Authorization: Bearer <clientApiKey>' \
    -d '{}'
```

After sending this request you should get a `200` status code with a JSON response that looks like:

```json
{
    "secp256k1": {
        "share": "eyJjbGllbnRJZCI6IiIsImJhY2t1cFNoYXJlUGFpcklkIjoiIiwic2lnbml...",
        "id": "clu3aue3j001fs60wdecyz0qy"
    },
    "ed25519": {
        "share": "zMzMzQ4MzIyMDUwMDYwMDc1NzU2NDYzMTU1MzEyODg3MzY5MjQxNTc2lnbml...",
        "id": "clu3auej6001ds60wqhh4tzgx"
    }
}
```

2. Returned in this request is the `share` and `id` of that share for each respective curve! In your production build you’ll store the full object that is returned, `generateReponse`, in a secure location. For this guide, just save this value for Signing.&#x20;
3. As a wallet safeguarding measure, we require the client to notify our backend once they have successfully stored their signing share (in either a keychain or DB). To do this, just make a single `PATCH` request to `/api/v3/clients/me/signing-share-pairs` with the `signingSharePairIds` you get from the generate response and a `"STORED_CLIENT"` status.

```typescript
const SECP256K1_SHARE_ID: string = response.secp256k1.id
const ED25519_SHARE_ID: string = response.ed25519.id
```

```bash
curl -X PATCH 'https://api.portalhq.io/api/v3/clients/me/signing-share-pairs' \
    -H 'Content-Type: application/json' \
    -H 'Authorization: Bearer <clientApiKey>' \
    -d '{
      "status": "STORED_CLIENT",
      "signingSharePairIds": ["SECP256K1_SHARE_ID", "ED25519_SHARE_ID"]
    }'
```

This will return a `204` on success.

Congrats! 🎉 You have now created your first Portal wallet and are ready to sign a transaction!
