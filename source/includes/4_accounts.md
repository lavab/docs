# Accounts

## Register using an invite

```http
POST /accounts HTTP/1.1
User-Agent: LavaboomClient/1.0.0
Accept: application/json
Host: api.lavaboom.io
Content-Type: application/json
Content-Length: 93

{
    "username": "johndoe",
    "password": "<sha3-256(fancypassword)>",
    "token": "valid password"
}
```

```json
{
    "success": true,
    "message": "A new account was successfully created",
    "account": {
        "id": "<id>",
        "date_created": "<RFC3339Nano date>",
        "date_modified": "<RFC3339Nano date>",
        "name": "johndoe",
        "type": "beta",
        "status": "invited"
    }
}
```

### Definition

`POST /accounts`

### Description

The `invite` token is generated either by a Lavaboom employee or a premium user.
Such request results in an instant creation of an account, that doesn't require
any confirmation.

### Fields

| Key      | Type   | Description                                                                                  |
|:---------|:-------|:---------------------------------------------------------------------------------------------|
| username | string | The desired username for the account.                                                        |
| password | string | Chosen password, hashed using SHA3-256. It's checked against 10000 most used passwords list. |
| token    | string | The invite token.                                                                            |

## Register with email confirmation

```http
POST /accounts HTTP/1.1
User-Agent: LavaboomClient/1.0.0
Accept: application/json
Host: api.lavaboom.io
Content-Type: application/json
Content-Length: 100

{
    "username": "johndoe",
    "password": "<sha3-256(fancypassword)>",
    "alt_email": "johndoe@gmail.com"
}
```

```json
{
    "success": true,
    "message": "A new account was successfully created, you should receive a confirmation email soon™.",
    "account": {
        "id": "<id>",
        "date_created": "<RFC3339Nano date>",
        "date_modified": "<RFC3339Nano date>",
        "name": "johndoe",
        "type": "beta",
        "alt_email": "johndoe@gmail.com",
        "status": "unverified"
    }
}
```

<aside class="warning">Confirmation emails are not implemented yet!</aside>

### Definition

`POST /accounts`

### Description

A user registers on the "Sign up" page. The account is created, but it's marked
as unverified. An email is sent to the `alt_email` address that contains a link
to the Lavaboom web client, which verifies the account and signs in the user.

### Fields

| Key       | Type   | Description                                                                                  |
|:----------|:-------|:---------------------------------------------------------------------------------------------|
| alt_email | string | An email address to send the confirmation token to.                                          |
| username  | string | The desired username for the account.                                                        |
| password  | string | Chosen password, hashed using SHA3-256. It's checked against 10000 most used passwords list. |

## Queue for beta

```http
POST /accounts HTTP/1.1
User-Agent: LavaboomClient/1.0.0
Accept: application/json
Host: api.lavaboom.io
Content-Type: application/json
Content-Length: 40

{
    "alt_email": "johndoe@gmail.com"
}
```

```json
{
    "success": true,
    "message": "Reserved an account."
}
```

<aside class="warning">Invitation emails are not implemented yet!</aside>

### Definition

`POST /accounts`

### Description

During the closed beta, a user can request an invite to the service. An account
isn't created, but the `alt_email` is appended to the queue. Then, when the
user's turn comes, they get an email with an invitation token.

### Fields

| Key       | Type   | Description                                       |
|:----------|:-------|:--------------------------------------------------|
| alt_email | string | An email address to send the invitation token to. |

## Reserve a username for beta

```http
POST /accounts HTTP/1.1
User-Agent: LavaboomClient/1.0.0
Accept: application/json
Host: api.lavaboom.io
Content-Type: application/json
Content-Length: 67

{
    "username": "johndoe",
    "alt_email": "johndoe@gmail.com"
}
```

```json
{
    "success": true,
    "message": "Reserved an account."
}
```

<aside class="warning">Username reservations might not be enforced in all cases.</aside>

### Definition

`POST /accounts`

### Description

If username reservations are enabled in the API, a user can also reserve
an account name. Process is almost the same as in the queue process.

### Fields

| Key       | Type   | Description                                               |
|:----------|:-------|:----------------------------------------------------------|
| username  | string | The desired username that the user would like to reserve. |
| alt_email | string | An email address to send the invitation token to.         |

## Get own account information

```http
GET /accounts/me HTTP/1.1
User-Agent: LavaboomClient/1.0.0
Accept: application/json
Host: api.lavaboom.io
```

```json
{
    "success": true,
    "account": {
        "id": "<id>",
        "date_created": "<RFC3339Nano date>",
        "date_modified": "<RFC3339Nano date>",
        "name": "johndoe",
        "type": "beta",
        "alt_email": "johndoe@gmail.com",
        "status": "verified"
    }
}
```

<aside class="notice">Requires authentication.</aside>

### Definition

`GET /accounts/me`

### Description

Returns information about the current user.

## Update account data

```http
PUT /accounts/me HTTP/1.1
User-Agent: LavaboomClient/1.0.0
Accept: application/json
Host: api.lavaboom.io
Content-Type: application/json
Content-Length: 94

{
    "alt_email": "johndoe2@gmail.com",
    "current_password": "<sha3-256(fancypassword)>"
}
```

```json
{
    "success": true,
    "message": "Your account has been successfully updated",
    "account": {
        "id": "<id>",
        "date_created": "<RFC3339Nano date>",
        "date_modified": "<RFC3339Nano date>",
        "name": "johndoe",
        "type": "beta",
        "alt_email": "johndoe@gmail.com",
        "status": "verified"
    }
}
```

<aside class="notice">Requires authentication.</aside>

### Definition

`PUT /accounts/me`

### Description

Modifies current account's data.

### Fields

| Key              | Type   | Description                                                                                           |
|:-----------------|:-------|:------------------------------------------------------------------------------------------------------|
| alt_email        | string | New alternative email                                                                                 |
| current_password | string | Current password of the account                                                                       |
| new_password     | string | The new desired password, hashed using SHA3-256. It's checked against 10000 most used passwords list. |

## Delete own account

```http
DELETE /accounts/me HTTP/1.1
User-Agent: LavaboomClient/1.0.0
Accept: application/json
Host: api.lavaboom.io
```

```json
{
    "success": true,
    "message": "Your account has been successfully deleted"
}
```

<aside class="notice">Requires authentication.</aside>
<aside class="warning">Not all dependent objects are removed.</aside>

### Definition

`DELETE /accounts/me`

### Description

Deletes current account and dependent objects.

## Wipe account data

```http
POST /accounts/me/wipe-data HTTP/1.1
User-Agent: LavaboomClient/1.0.0
Accept: application/json
Host: api.lavaboom.io
```

```json
{
    "success": true,
    "message": "Your account has been successfully wiped"
}
```

<aside class="notice">Requires authentication.</aside>
<aside class="warning">Not all dependent objects are removed.</aside>

### Definition

`POST /accounts/me/wipe-data`

### Description

Removes all account's dependent objects.
