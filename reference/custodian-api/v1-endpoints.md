---
description: >-
  These endpoints require a Portal API Key (aka "Custodian API Key") as a bearer
  token.
---

# V1 endpoints

{% hint style="warning" %}
Before going live with real users, always ensure that you create clients using a [Portal API Key](../../resources/authentication-and-api-keys.md#portal-api-key) from your Portal's **Production** environment. Read more [here](../../resources/going-to-production.md) on going live with real users.
{% endhint %}

## Create a new client

<mark style="color:purple;">**`POST`**</mark> `https://api.portalhq.io/api/v1/custodians/clients`

Registers a new client and returns a client session token.

#### Headers

| Name                                            | Type   | Description              |
| ----------------------------------------------- | ------ | ------------------------ |
| Authorization<mark style="color:red;">\*</mark> | String | Bearer \<Portal API Key> |
| Content-Type<mark style="color:red;">\*</mark>  | String | application/json         |

#### Request Body

| Name                | Type    | Description                                       |
| ------------------- | ------- | ------------------------------------------------- |
| isAccountAbstracted | Boolean | The client will use ERC-4337 Account Abstraction. |

{% tabs %}
{% tab title="201: Created Success" %}
```json
{
  "id": "string",
  "clientApiKey": "string",
  "clientSessionToken": "string",
  "isAccountAbstracted": boolean
}
```
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

## Fetches a single client

<mark style="color:green;">**`GET`**</mark> `https://api.portalhq.io/api/v1/custodians/clients/{clientId}`

Fetches the specified client for the authorized custodian.

#### Path Parameters

| Name                                       | Type   | Description         |
| ------------------------------------------ | ------ | ------------------- |
| clientId<mark style="color:red;">\*</mark> | String | The ID of a client. |

#### Headers

| Name                                            | Type   | Description              |
| ----------------------------------------------- | ------ | ------------------------ |
| Authorization<mark style="color:red;">\*</mark> | String | Bearer \<Portal API Key> |
| Content-Type<mark style="color:red;">\*</mark>  | String | application/json         |

{% tabs %}
{% tab title="200: OK Success" %}
```json
{
  "id": "string",
  "address": "string",
  "clientApiKey": "string",
  "isAccountAbstracted": boolean
}
```
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

## Fetches a list of clients

<mark style="color:green;">**`GET`**</mark> `https://api.portalhq.io/api/v1/custodians/clients`

Fetches all clients for the authorized custodian.

#### Headers

| Name                                            | Type   | Description              |
| ----------------------------------------------- | ------ | ------------------------ |
| Authorization<mark style="color:red;">\*</mark> | String | Bearer \<Portal API Key> |
| Content-Type<mark style="color:red;">\*</mark>  | String | application/json         |

{% tabs %}
{% tab title="200: OK Success" %}
```json
[
  {
    "id": "string",
    "address": "string",
    "backupStatus": "string",
    "clientApiKey": "string",
    "signingStatus": "string"
  }
]
```
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

## Refresh Client Session Token (CST)

<mark style="color:purple;">**`POST`**</mark> `https://api.portalhq.io/api/v1/custodians/clients/{clientId}/session`

Refreshes a client's Client Session Token.

#### Path Parameters

| Name     | Type   | Description         |
| -------- | ------ | ------------------- |
| clientId | String | The ID of a client. |

#### Headers

| Name                                            | Type   | Description              |
| ----------------------------------------------- | ------ | ------------------------ |
| Authorization<mark style="color:red;">\*</mark> | String | Bearer \<Portal API Key> |
| Content-Type<mark style="color:red;">\*</mark>  | String | application/json         |

{% tabs %}
{% tab title="200: OK Success" %}
```json
{
  "id": "string",
  "clientSessionToken": "string",
  "isAccountAbstracted": boolean
}
```
{% endtab %}

{% tab title="400: Bad Request Bad Request" %}
```json
{
  "error": "string"
}
```
{% endtab %}

{% tab title="401: Unauthorized Unauthorized Request" %}
```json
{
  "error": "string"
}
```
{% endtab %}
{% endtabs %}

## Generate Client Web One-Time Password (OTP)

<mark style="color:green;">**`GET`**</mark> `https://api.portalhq.io/api/v1/custodians/clients/{clientId}/web-otp`

Generate a one-time password for a client.

#### Path Parameters

| Name                                       | Type   | Description         |
| ------------------------------------------ | ------ | ------------------- |
| clientId<mark style="color:red;">\*</mark> | String | The ID of a client. |

#### Headers

| Name                                            | Type   | Description              |
| ----------------------------------------------- | ------ | ------------------------ |
| Authorization<mark style="color:red;">\*</mark> | String | Bearer \<Portal API Key> |
| Content-Type<mark style="color:red;">\*</mark>  | String | application/json         |

{% tabs %}
{% tab title="200: OK Success" %}
```json
{
  "id": "string"
  "otp": "string"
}
```
{% endtab %}

{% tab title="400: Bad Request Bad Request" %}
```
{}
```
{% endtab %}

{% tab title="401: Unauthorized Unauthorized" %}
```
{}
```
{% endtab %}
{% endtabs %}
