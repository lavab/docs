# Contacts

## List all contacts

```http
GET /contacts HTTP/1.1
User-Agent: LavaboomClient/1.0.0
Accept: application/json
Host: api.lavaboom.io
```

```javascript
api.contacts.list().then(function(resp) {
    console.log(resp);
}).catch(function{err} {
    console.log(err);
})
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

```javascript
api.contacts.create({
    "data": "<encrypted PGP blob>",
    "name": "John Doe",
    "encoding": "json",
    "version_major": 1,
    "version_minor": 0,
    "pgp_fingerprints": ["fingerprint"]
}).then(function(resp) {
    console.log(resp);
}).catch(function{err} {
    console.log(err);
})
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

```javascript
api.contacts.get("<id>").then(function(resp) {
    console.log(resp);
}).catch(function{err} {
    console.log(err);
})
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

```javascript
api.contacts.update("<id>", {
    "name": "John Doe"
}).then(function(resp) {
    console.log(resp);
}).catch(function{err} {
    console.log(err);
})
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

```javascript
api.contacts.delete("<id>").then(function(resp) {
    console.log(resp);
}).catch(function{err} {
    console.log(err);
})
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
