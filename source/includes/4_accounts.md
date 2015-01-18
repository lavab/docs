# Accounts

## Reserve a username

```http
POST /accounts HTTP/1.1
User-Agent: LavaboomClient/1.0.0
Accept: application/json
Host: api.lavaboom.io
Content-Type: application/json
Content-Length: 58

{
    "username": "johndoe",
    "email": "john@doe.org"
}
```

```javascript
api.accounts.create.register({
    "username": "johndoe",
    "email": "john@doe.org"
}).then(function(resp) {
    console.log(resp);
}).catch(function{err} {
    console.log(err);
})
```

```json
{
    "success": true,
    "message": "Your account has been added to the beta queue",
    "account": {
        "id": "<id>",
        "date_created": "<RFC3339Nano date>",
        "date_modified": "<RFC3339Nano date>",
        "name": "johndoe",
        "type": "beta",
        "alt_email": "john@doe.org",
        "status": "registered"
    }
}
```

### Definition

`POST /accounts`

### Description

Creates a new, non-verified account. Used for username reservations and
invitation queueing during beta, account registration after release.

### Fields

|    Key    |  Type  |                          Description                          |
| :-------- | :----- | :------------------------------------------------------------ |
| username  | string | The desired username for the account.                         |
| alt_email | string | Email to which an invitation/verification email will be sent. |

## Check token's validity

```http
POST /accounts HTTP/1.1
User-Agent: LavaboomClient/1.0.0
Accept: application/json
Host: api.lavaboom.io
Content-Type: application/json
Content-Length: 100

{
    "username": "johndoe",
    "token": "<verification token>"
}
```

```javascript
api.accounts.create.verify({
    "username": "johndoe",
    "token:": "<verification token>"
}).then(function(resp) {
    console.log(resp);
}).catch(function{err} {
    console.log(err);
})
```

```json
{
    "success": true,
    "message": "Valid token was provided"
}
```

### Definition

`POST /accounts`

### Description

After user receives a token (either in a confirmation email or after being
invited from the queue list), they open an API page that has to check the
token's validity.

### Fields

|   Key    |  Type  |              Description              |
| :------- | :----- | :------------------------------------ |
| username | string | The desired username for the account. |
| token    | string | Token to validate.                    |

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

```javascript
api.accounts.reserve.queue({
    "email": "johndoe@gmail.com"
}).then(function(resp) {
    console.log(resp);
}).catch(function{err} {
    console.log(err);
})
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

```javascript
api.accounts.reserve.username({
    "username": "johndoe",
    "email": "johndoe@gmail.com"
}).then(function(resp) {
    console.log(resp);
}).catch(function{err} {
    console.log(err);
})
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

```javascript
api.accounts.get("me").then(function(resp) {
    console.log(resp);
}).catch(function{err} {
    console.log(err);
})
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

```javascript
api.accounts.update("me", {
    "alt_email": "johndoe2@gmail.com",
    "current_password": "<sha3-256(fancypassword)>"
}).then(function(resp) {
    console.log(resp);
}).catch(function{err} {
    console.log(err);
})
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

|       Key        |      Type      |                                              Description                                              |
| :--------------- | :------------- | :---------------------------------------------------------------------------------------------------- |
| alt_email        | string         | New alternative email                                                                                 |
| current_password | string         | Current password of the account. Only required if you want to update the password.                    |
| new_password     | string         | The new desired password, hashed using SHA3-256. It's checked against 10000 most used passwords list. |
| factor_type      | string         | Type of the 2FA. Only YubiKey is known to work.                                                       |
| factor_value     | list\<string\> | 2FA value, for example the static prefix of a YubiKey.                                                |
| settings         | object         | Application data stored by the frontend.                                                              |
| public_key       | string         | Fingerprint of the public key.                                                                        |
|                  |                |                                                                                                       |

## Delete own account

```http
DELETE /accounts/me HTTP/1.1
User-Agent: LavaboomClient/1.0.0
Accept: application/json
Host: api.lavaboom.io
```

```javascript
api.accounts.delete("me").then(function(resp) {
    console.log(resp);
}).catch(function{err} {
    console.log(err);
})
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

```javascript
api.accounts.wipeData("me").then(function(resp) {
    console.log(resp);
}).catch(function{err} {
    console.log(err);
})
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
