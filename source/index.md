---
language_tabs:
  - http

toc_footers:
  - © 2014 Lavaboom GmbH

includes:
  - errors

search: true
---

# Introduction

```http
GET / HTTP/1.1
User-Agent: LavaboomClient/1.0.0
Accept: application/json
Host: api.lavaboom.io
```

```json
{
    "message": "Lavaboom API",
    "docs_url": "http://lavaboom.readme.io/",
    "version": "v0"
}
```

Welcome to the Lavaboom API documentation. This is the API used by the official Lavaboom web interface, so everything done there can be also performed via the API.

There are multiple endpoints for you to use:

| Name | API version     | URL                      | Public |
|:-----|:----------------|:-------------------------|:-------|
| prod | _(not running)_ | http://api.lavaboom.com/ | Yes    |
| dev  | v0              | http://api.lavaboom.io/  | No     |

Please note that any non-production API request must contain a special `X-Lavaboom-Key` token to be authorized.

# SockJS endpoint

```http
GET /ws/info HTTP/1.1
User-Agent: LavaboomClient/1.0.0
Accept: application/json
Host: api.lavaboom.io
```

```json
{
    "websocket": true,
    "cookie_needed": false,
    "origins": ["*:*"],
    "entropy": 0
}
```

There are two ways to execute API requests:

 - Connect to the SockJS endpoint and perform communication using it.
 - Directly connect to the HTTP API.

The endpoint is available at `/ws` on every API.

## Message format

Each message sent through the SockJS connection is encoded using JSON. You can identify its kind by accessing its `type` key.

| Request type | Result type  | Description                   |
|:-------------|:-------------|:------------------------------|
| request      | result       | Executes a HTTP request       |
| subscribe    | subscribed   | Subscribes to user events     |
| unsubscribe  | unsubscribed | Unsubscribes from user events |

## Requests

```json
{
    "type": "request",
    "id": "123",
    "method": "GET",
    "path": "/"
}
```

```json
{
    "type": "response",
    "body": "{\"message\":\"Lavaboom API\",\"docs_url\":\"http://lavaboom.readme.io/\",\"version\":\"v0\"}",
    "headers": {
        "Content-Type": ["application/json; charset=utf-8"]
    },
    "id": "123",
    "status": 200
}
```

You can perform HTTP requests through the SockJS endpoint using `request` messages.

### Request message fields

| Key     | Type             | Description                              |
|:--------|:-----------------|:-----------------------------------------|
| id      | string           | ID returned with the result message      |
| method  | string           | Request method. GET, POST, PUT or DELETE |
| path    | string           | Path to query (for example `/tokens`)    |
| body    | string           | Request body (not required).             |
| headers | map\<string\>any | Headers to pass with the request         |

### Result message fields

| Key     | Type             | Description                         |
|:--------|:-----------------|:------------------------------------|
| id      | string           | ID passed with the request message  |
| status  | int              | Status of the request               |
| headers | map\<string\>any | Headers generated with the response |
| body    | string           | Body of the response                |


## Subscriptions

```json
{
    "type": "subscribe",
    "token": "valid token"
}

{
    "type": "subscribed"
}



{
    "type": "unsubscribe"
}

{
    "type": "unsubscribed"
}
```

In order to listen to events, you have to "log in" to the account using the `subscribe` message. Then, if you want to log out, send an `unsubscribe` message or log out.

### Subscribe message fields (request)

| Key   | Type   | Description                 |
|:------|:-------|:----------------------------|
| token | string | Authentication token to use |

### Subscribed message fields (response)

_None_

### Unsubscribe message fields (request)

_None_

### Unsubscribed message fields (response)

_None_

## User events

```json
{
    "type": "delivery",
    "id": "delivered email id",
    "name": "Hello world"
}

{
    "type": "receipt",
    "id": "received email id",
    "name": "Hello world"
}
```

There are two user events that you can receive:

### Delivery fields

| Key  | Type   | Description                                           |
|:-----|:-------|:------------------------------------------------------|
| id   | string | ID of the delivered message                           |
| name | string | Subject of the delivered message (might be encrypted) |

### Receipt fields

| Key  | Type   | Description                                           |
|:-----|:-------|:------------------------------------------------------|
| id   | string | ID of the received message                            |
| name | string | Subject of the received message  (might be encrypted) |

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

# Contacts

## List all contacts

```http
GET /contacts HTTP/1.1
User-Agent: LavaboomClient/1.0.0
Accept: application/json
Host: api.lavaboom.io
```

```json
{
    "success": true,
    "contacts": [
        {
            "id": "<id>",
            "date_created": "<RFC3339Nano date>",
            "date_modified": "<RFC3339Nano date>",
            "name": "John Doe",
            "owner": "<account id>",
            "encoding": "json",
            "pgp_fingerprints": ["<PGP fingerprint>"],
            "data": "<PGP-encoded payload>",
            "schema": "email",
            "version_major": 1,
            "version_minor": 0
        }
    ]
}
```

### Definition

`GET /contacts`

### Description

Returns all contacts owned by the user.

## Create a new contact

```http
GET /contacts HTTP/1.1
User-Agent: LavaboomClient/1.0.0
Accept: application/json
Host: api.lavaboom.io
Content-Type: application/json
Content-Length: 175

{
    "data": "<encrypted PGP blob>",
    "name": "John Doe",
    "encoding": "json",
    "version_major": 1,
    "version_minor": 0,
    "pgp_fingerprints": ["fingerprint"]
}
```

```json
{
    "success": true,
    "message": "A new contact was successfully created",
    "contact": {
        "id": "<id>",
        "date_created": "<RFC3339Nano date>",
        "date_modified": "<RFC3339Nano date>",
        "name": "John Doe",
        "owner": "<account id>",
        "encoding": "json",
        "pgp_fingerprints": ["<PGP fingerprint>"],
        "data": "<PGP-enrypted blob>",
        "schema": "email",
        "version_major": 1,
        "version_minor": 0
    }
}
```

### Definition

`POST /contacts`

### Description

Creates a new contact, which is an encrypted blob of data.

### Fields

| Key              | Type           | Description                                                               |
|:-----------------|:---------------|:--------------------------------------------------------------------------|
| data             | string         | PGP-encrypted contact data                                                |
| name             | string         | Unencrypted name of the contact                                           |
| encoding         | string         | Encrypted blob's encoding (for example `json`)                            |
| version_major    | int            | Major version of the encrypted blob                                       |
| version_minor    | int            | Minor version of the data, different minor versions should be compatible. |
| pgp_fingerprints | list\<string\> | PGP fingerprints used to encode a contact                                 |

## Get a contact

```http
GET /contacts/<id> HTTP/1.1
User-Agent: LavaboomClient/1.0.0
Accept: application/json
Host: api.lavaboom.io
```

```json
{
    "success": true,
    "contact": {
        "id": "<id>",
        "date_created": "<RFC3339Nano date>",
        "date_modified": "<RFC3339Nano date>",
        "name": "John Doe",
        "owner": "<account id>",
        "encoding": "json",
        "pgp_fingerprints": ["<PGP fingerprint>"],
        "data": "<PGP-encoded payload>",
        "schema": "email",
        "version_major": 1,
        "version_minor": 0
    }
}
```

### Definition

`GET /contacts/:id`

### Description

Retrieves a contact by its ID.

## Update a contact

```http
PUT /contacts/<id> HTTP/1.1
User-Agent: LavaboomClient/1.0.0
Accept: application/json
Host: api.lavaboom.io
Content-Type: application/json
Content-Length: 27

{
    "name": "John Does"
}
```

```json
{
    "success": true,
    "contact": {
        "id": "<id>",
        "date_created": "<RFC3339Nano date>",
        "date_modified": "<RFC3339Nano date>",
        "name": "John Does",
        "owner": "<account id>",
        "encoding": "json",
        "pgp_fingerprints": ["<PGP fingerprint>"],
        "data": "<PGP-enrypted blob>",
        "schema": "email",
        "version_major": 1,
        "version_minor": 0
    }
}
```

### Definition

`PUT /contacts/:id`

### Description

Updates specified contact.

### Fields

| Key              | Type           | Description                                                               |
|:-----------------|:---------------|:--------------------------------------------------------------------------|
| data             | string         | PGP-encrypted contact data                                                |
| name             | string         | Unencrypted name of the contact                                           |
| encoding         | string         | Encrypted blob's encoding (for example `json`)                            |
| version_major    | int            | Major version of the encrypted blob                                       |
| version_minor    | int            | Minor version of the data, different minor versions should be compatible. |
| pgp_fingerprints | list\<string\> | PGP fingerprints used to encode a contact                                 |

## Delete a contact

```http
DELETE /contacts/<id> HTTP/1.1
User-Agent: LavaboomClient/1.0.0
Accept: application/json
Host: api.lavaboom.io
```

```json
{
    "success": true,
    "message": "Contact successfully removed"
}
```

### Definition

`DELETE /contacts/:id`

### Description

Deletes specified contact.

# Emails

## List emails

## Create a new email

## Get an email

## Delete an email

# Keys

## List keys assigned to an email

## Get a key by ID

## Upload a new key

## Vote on a key

# Labels

# Threads

# Tokens

## Get current token info

## Create a new token (sign in)

## Delete a token (log out)
