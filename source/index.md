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
            "schema": "contact",
            "version_major": 1,
            "version_minor": 0
        }
    ]
}
```

<aside class="notice">Requires authentication.</aside>

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

<aside class="notice">Requires authentication.</aside>

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

<aside class="notice">Requires authentication.</aside>

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
        "schema": "contact",
        "version_major": 1,
        "version_minor": 0
    }
}
```

<aside class="notice">Requires authentication.</aside>

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

<aside class="notice">Requires authentication.</aside>

### Definition

`DELETE /contacts/:id`

### Description

Deletes specified contact.

# Emails

## List emails

```http
GET /emails?sort=+date_created HTTP/1.1
User-Agent: LavaboomClient/1.0.0
Accept: application/json
Host: api.lavaboom.io
```

```json
{
    "success": true,
    "emails": [
        {
            "id": "<id>",
            "date_created": "<RFC3339Nano date>",
            "date_modified": "<RFC3339Nano date>",
            "name": "Hello World",
            "owner": "<account id>",
            "kind": "received",
            "to": "johndoe@lavaboom.com",
            "body": {
                "encoding": "json",
                "pgp_fingerprints": ["<PGP fingerprint>"],
                "data": "<PGP-encoded payload>",
                "schema": "email",
                "version_major": 1,
                "version_minor": 0
            },
            "status": "received"
        }
    ]
}
```

<aside class="notice">Requires authentication.</aside>

### Definition

`GET /emails`

### Description

Returns emails owned by the user.

### Query parameters

| Name   | Type   | Description                                                                                                                       |
|:-------|:-------|:----------------------------------------------------------------------------------------------------------------------------------|
| sort   | string | Comma-seperated list of fields, the order is expressed by the first character of the field's name, which can be either `+` or `-` |
| offset | int    | Amount of emails to skip.                                                                                                         |
| limit  | int    | Amount of emails to display.                                                                                                      |

## Create a new email

```http
POST /emails HTTP/1.1
User-Agent: LavaboomClient/1.0.0
Accept: application/json
Host: api.lavaboom.io
Content-Type: application/json
Content-Length: 8

{
    "to": "johndoe@inferiormail.com",
    "subject": "Lavaboom master race!",
    "is_encrypted": false,
    "body": "Check out this cool new email service!"
}
```

```json
{
    "success": true,
    "created": ["<id>"]
}
```

<aside class="notice">Requires authentication.</aside>

### Definition

`POST /emails`

### Description

Sends an email.

### Fields

| Name                  | Type           | Description                                                |
|:----------------------|:---------------|:-----------------------------------------------------------|
| to                    | list\<string\> | Who should receive the email?                              |
| bcc                   | list\<string\> | Hidden recipients                                          |
| reply_to              | string         | Reply-to field's vlaue                                     |
| thread_id             | string         | ID of the thread (can be null)                             |
| subject               | string         | Subject of the email                                       |
| is_encrypted          | bool           | Is body/preview encrypted?                                 |
| body                  | string         | PGP-encrypted email body (or raw)                          |
| body_version_major    | int            | Major version of the body's blob                           |
| body_version_minor    | int            | Minor version of the body's blob                           |
| preview               | string         | PGP-encrypted preview body (or raw)                        |
| preview_version_major | int            | Major version of the preview's blob                        |
| preview_version_minor | int            | Minor version of the preview's blob                        |
| attachments           | list\<string\> | Attachment IDs                                             |
| pgp_fingerprints      | list\<string\> | Fingerprints of keys used to encrypt the body and preview. |

## Get an email

```http
GET /emails/<id> HTTP/1.1
User-Agent: LavaboomClient/1.0.0
Accept: application/json
Host: api.lavaboom.io
```

```json
{
    "success": true,
    "email": {
        "id": "<id>",
        "date_created": "<RFC3339Nano date>",
        "date_modified": "<RFC3339Nano date>",
        "name": "Hello World",
        "owner": "<account id>",
        "kind": "received",
        "to": "johndoe@lavaboom.com",
        "body": {
            "encoding": "json",
            "pgp_fingerprints": ["<PGP fingerprint>"],
            "data": "<PGP-encoded payload>",
            "schema": "email",
            "version_major": 1,
            "version_minor": 0
        },
        "status": "received"
    }
}
```

<aside class="notice">Requires authentication.</aside>

### Definition

`GET /emails/:id`

### Description

Gets a specified email.

## Delete an email

```http
DELETE /emails/<id> HTTP/1.1
User-Agent: LavaboomClient/1.0.0
Accept: application/json
Host: api.lavaboom.io
```

```json
{
    "success": true,
    "message": "Email successfully removed"
}
```

<aside class="notice">Requires authentication.</aside>

### Definition

`DELETE /emails/:id`

### Description

Deletes a specified email.

# Keys

## List keys assigned to a user

```http
GET /keys?user=johndoe HTTP/1.1
User-Agent: LavaboomClient/1.0.0
Accept: application/json
Host: api.lavaboom.io
```

```json
{
    "success": true,
    "keys": ["<armor-encoded public PGP key>"]
}
```

### Definition

`GET /keys?user=<username>`

### Description

Returns keys owned by a user.

### Query parameters

| Key  | Type   | Description                                    |
|:-----|:-------|:-----------------------------------------------|
| user | string | Name of the user whose keys should be returned |

## Get a key by ID

```http
GET /keys/<fingerprint> HTTP/1.1
User-Agent: LavaboomClient/1.0.0
Accept: application/json
Host: api.lavaboom.io
```

```json
{
    "success": true,
    "key": "<armor-encoded public PGP key>"
}
```

### Definition

`GET /keys/<fingerprint>`

### Description

Returns a key based on its fingerprint

## Get a key by email

```http
GET /keys/<email> HTTP/1.1
User-Agent: LavaboomClient/1.0.0
Accept: application/json
Host: api.lavaboom.io
```

```json
{
    "success": true,
    "key": "<armor-encoded public PGP key>"
}
```

### Definition

`GET /keys/<email>`

### Description

Returns default key assigned to an email.

## Upload a new key

```http
POST /keys HTTP/1.1
User-Agent: LavaboomClient/1.0.0
Accept: application/json
Host: api.lavaboom.io
Content-Type: application/json
Content-Length: 23

{
    "key": "<armor-encoded public PGP key>"
}
```

```json
{
    "success": true,
    "message": "A new key has been successfully inserted",
    "key": "<armor-encoded public PGP key>"
}
```

<aside class="notice">Requires authentication.</aside>

### Definition

`POST /keys`

### Description

Uploads a new key to the storage.

### Fields

| Key | Type   | Description                  |
|:----|:-------|:-----------------------------|
| key | string | Armor-encoded PGP public key |

## Vote on a key

_Not implemented_

# Labels

_Not implemented_

# Threads

_Not implemented_

# Tokens

## Get current token info

## Create a new token (sign in)

## Delete a token (log out)
