
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
Content-Length: 47

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
