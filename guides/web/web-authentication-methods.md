---
description: >-
  The Portal Web SDK offers various approaches to authenticating. The main
  reason for this is to allow you to strike the right balance between
  integration time and security.
---

# Web authentication methods

## Authenticating with a Client API Key

This approach requires the least setup, but is also the least secure as it exposes your Client API Key to your DOM. For this reason, we recommend that this method only be used during local development.

The only thing required for this is to create a client like normal via the Portal REST API and use the `clientApiKey` for the `apiKey` option when initializing `Portal`.

```typescript
import Portal from '@portal-hq/web'

const portal = new Portal({
    apiKey: 'YOUR_CLIENT_API_KEY',
    rpcConfig: {
      'eip155:1': 'YOUR-INFURA-OR-ALCHEMY-URL',
      'solana:5eykt4UsFv8P8NJdTREpY1vzqKqZKvdp': 'https://api.mainnet-beta.solana.com',
    },
})
```

## Authenticating with a Web OTP

This approach is a bit more secure as it instead exposes a OTP to your DOM and once this is used, it's invalidated on Portal's end, which makes it unusable for authentication in the future.

The steps required to accomplish this are:

1. Make a `GET` request to `https://api.portalhq.io/api/v1/custodians/clients/${clientId}/web-otp` using your `CUSTODIAN_API_KEY` as a `Bearer` token.
2. Initialize `Portal` with the delivered OTP as the `authToken` initialization option.

### Fetching a Web OTP

```typescript
// On your server
yourApp.get('/otp', (req: Request, res: Response) => {
  const url = `https://api.portalhq.io/api/v1/custodians/clients/${clientId}/web-otp`
  const { data } = await axios.get(url, {
    headers: {
      Authorization: `Bearer YOUR-CUSTODIAN-API-KEY`,
    },
  })

  res.sent(data.otp)
})
```

### Initializing Portal

```typescript
import Portal from '@portal-hq/web'

const initializePortal = () => {
  const response = await fetch('https://yourserver.com/otp', {
    credentials: 'include'
  })
  
  const authToken = response.text()
  
  const portal = new Portal({
    authToken,
    rpcConfig: {
      'eip155:1': 'YOUR-INFURA-OR-ALCHEMY-URL',
      'solana:5eykt4UsFv8P8NJdTREpY1vzqKqZKvdp': 'https://api.mainnet-beta.solana.com',
    },
  })
}
```

## Authenticating with an authUrl

This approach is by far the most secure as it never exposes any Portal credentials to your DOM. This works by adding a route to your application that fetches an OTP and redirects to Portal's server for validation. The URL to your authentication route is then provided as the `authUrl` option when initializing `Portal`.

The steps required to accomplish this are:

1. Create a route on your server that gets a new Web OTP and redirects to `https://web.portalhq.io/clients/token/validate?otp=YOUR-WEB-OTP`
2. Enable CORS access to `https://web.portalhq.io` on your new route
3. Initialize `Portal` with your `authUrl`

### Creating an authentication route

```typescript
// On your server
yourApp.get(
  '/portal/authenticate', // This can be any route you like
  cors({
    credentials: true,
    origin: 'https://web.portalhq.io',
  }),
  async (req: Request, res: Response) => {
    const url = `https://api.portalhq.io/api/v1/custodians/clients/${clientId}/web-otp`
    const { data } = await axios.get(url, {
      headers: {
        Authorization: 'Bearer YOUR-CUSTODIAN-API-KEY'
      }
    })

    // This redirect is where the magic happens
    res.redirect(`https://web.portalhq.io/clients/token/validate?otp=${data.otp}`)
  }
)
```

### Initializing Portal with an authUrl

```typescript
import Portal from '@portal-hq/web'

const portal = new Portal({
  // This URL corresponds to the route you created in the previous step
  authUrl: 'https://yourserver.com/portal/authenticate',
  rpcConfig: {
    'eip155:1': 'YOUR-INFURA-OR-ALCHEMY-URL',
    'solana:5eykt4UsFv8P8NJdTREpY1vzqKqZKvdp': 'https://api.mainnet-beta.solana.com',
  },
})
```
