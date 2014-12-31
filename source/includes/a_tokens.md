# Tokens

## Get token information

```http
GET /tokens HTTP/1.1
User-Agent: LavaboomClient/1.0.0
Accept: application/json
Host: api.lavaboom.io
```

```json
{
    "success": true,
    "created": "<RFC3339Nano date>",
    "expires": "<RFC3339Nano date>"
}
```

<aside class="notice">Requires authentication.</aside>

### Definition

 - `GET /tokens`
 - `GET /tokens/:id`

### Description

Returns information about a token.

## Create a new token (sign in)

```http
POST /tokens HTTP/1.1
User-Agent: LavaboomClient/1.0.0
Accept: application/json
Host: api.lavaboom.io
Content-Type: application/json
Content-Length: 94

{
    "username": "johndoe",
    "password": "<sha3-256(fancypassword)>",
    "type": "auth"
}
```

```json
{
    "success": true,
    "message": "Authentication successful",
    "token": {
        "id": "<id>",
        "date_created": "<RFC3339Nano date>",
        "date_modified":" <RFC3339Nano date>",
        "name": "Auth token expiring on <RFC3339 date>",
        "owner": "<account id>",
        "expiry_date": "<RFC3339Nano date>",
        "type": "auth"
    }
}
```

### Definition

`POST /tokens`

### Description

Creates a new token. Only `auth` tokens are supported now, therefore you can
only sign in using this endpoint.

### Fields

| Key      | Type   | Description                                                               |
|:---------|:-------|:--------------------------------------------------------------------------|
| username | string | Name of the account.                                                      |
| password | string | SHA3-256 hash of your password.                                           |
| type     | string | Type of the token you would like to create. Only `auth` is supported now. |
| token    | string | Two-factor token if you are authenticating (eg. a Yubikey OTP).           |

## Delete a token (log out)

```http
DELETE /tokens HTTP/1.1
User-Agent: LavaboomClient/1.0.0
Accept: application/json
Host: api.lavaboom.io
```

```json
{
    "success": true,
    "message": "Successfully logged out"
}
```

<aside class="notice">Requires authentication.</aside>

### Definition

 - `DELETE /tokens`
 - `DELETE /tokens/:id`

### Description

Deletes the specified token.
