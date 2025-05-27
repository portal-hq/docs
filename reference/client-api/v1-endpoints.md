---
description: These endpoints require a Client API Key as a bearer token.
---

# V1 endpoints

## Notify Portal when you store or fail to store the encrypted client backup share.

<mark style="color:orange;">**`PUT`**</mark> `https://api.portalhq.io/api/v1/clients/me/wallet/stored-client-backup-share`

By letting Portal know that you stored the encrypted client backup share successfully, the client is now capable of running `portal.recover`.

#### Headers

| Name                                            | Type   | Description              |
| ----------------------------------------------- | ------ | ------------------------ |
| Authorization<mark style="color:red;">\*</mark> | String | Bearer \<Client API Key> |
| Content-Type<mark style="color:red;">\*</mark>  | String | application/json         |

#### Request Body

| Name                                           | Type                                            | Description                                                     |
| ---------------------------------------------- | ----------------------------------------------- | --------------------------------------------------------------- |
| backupMethod<mark style="color:red;">\*</mark> | "ICLOUD" \| "GDRIVE" \| "PASSKEY" \| "PASSWORD" | Provide the backup method used when `portal.backup` was ran.    |
| success<mark style="color:red;">\*</mark>      | Boolean                                         | Provide `false` if you failed to store the client backup share. |

{% tabs %}
{% tab title="204: No Content Success" %}
No response body.
{% endtab %}

{% tab title="400: Bad Request Bad Request" %}
```json
{}
```
{% endtab %}

{% tab title="401: Unauthorized Unauthorized Request" %}
```json
{}
```
{% endtab %}
{% endtabs %}

