# Attachments

## List all attachments

```http
GET /attachments HTTP/1.1
User-Agent: LavaboomClient/1.0.0
Accept: application/json
Host: api.lavaboom.io
```

```javascript
api.attachments.list().then(function(resp) {
    console.log(resp);
}).catch(function{err} {
    console.log(err);
})
```

```json
{
    "success": true,
    "attachments": [
        {
            "id": "<id>",
            "date_created": "<RFC3339Nano date>",
            "date_modified": "<RFC3339Nano date>",
            "name": "test.zip",
            "owner": "<account id>",
            "encoding": "json",
            "pgp_fingerprints": ["<PGP fingerprint>"],
            "data": "<PGP-encoded payload>",
            "schema": "attachment",
            "version_major": 1,
            "version_minor": 0
        }
    ]
}
```

<aside class="notice">Requires authentication.</aside>

### Definition

`GET /attachments`

### Description

Returns all attachments owned by the user.

## Create a new attachment

```http
GET /attachments HTTP/1.1
User-Agent: LavaboomClient/1.0.0
Accept: application/json
Host: api.lavaboom.io
Content-Type: application/json
Content-Length: 175

{
    "data": "<encrypted PGP blob>",
    "name": "test.zip",
    "encoding": "json",
    "version_major": 1,
    "version_minor": 0,
    "pgp_fingerprints": ["fingerprint"]
}
```

```javascript
api.attachments.create({
    "data": "<encrypted PGP blob>",
    "name": "test.zip",
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
    "message": "A new attachment was successfully created",
    "attachment": {
        "id": "<id>",
        "date_created": "<RFC3339Nano date>",
        "date_modified": "<RFC3339Nano date>",
        "name": "test.zip",
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

`POST /attachments`

### Description

Creates a new attachment, which is an encrypted blob of data.

### Fields

| Key              | Type           | Description                                                               |
|:-----------------|:---------------|:--------------------------------------------------------------------------|
| data             | string         | PGP-encrypted attachment data                                                |
| name             | string         | Unencrypted name of the attachment                                           |
| encoding         | string         | Encrypted blob's encoding (for example `json`)                            |
| version_major    | int            | Major version of the encrypted blob                                       |
| version_minor    | int            | Minor version of the data, different minor versions should be compatible. |
| pgp_fingerprints | list\<string\> | PGP fingerprints used to encode a attachment                                 |

## Get a attachment

```http
GET /attachments/<id> HTTP/1.1
User-Agent: LavaboomClient/1.0.0
Accept: application/json
Host: api.lavaboom.io
```

```javascript
api.attachments.get("<id>").then(function(resp) {
    console.log(resp);
}).catch(function{err} {
    console.log(err);
})
```

```json
{
    "success": true,
    "attachment": {
        "id": "<id>",
        "date_created": "<RFC3339Nano date>",
        "date_modified": "<RFC3339Nano date>",
        "name": "test.zip",
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

`GET /attachments/:id`

### Description

Retrieves a attachment by its ID.

## Update a attachment

```http
PUT /attachments/<id> HTTP/1.1
User-Agent: LavaboomClient/1.0.0
Accept: application/json
Host: api.lavaboom.io
Content-Type: application/json
Content-Length: 27

{
    "name": "hello.zip"
}
```

```javascript
api.attachments.update("<id>", {
    "name": "hello.zip"
}).then(function(resp) {
    console.log(resp);
}).catch(function{err} {
    console.log(err);
})
```

```json
{
    "success": true,
    "attachment": {
        "id": "<id>",
        "date_created": "<RFC3339Nano date>",
        "date_modified": "<RFC3339Nano date>",
        "name": "hello.zip",
        "owner": "<account id>",
        "encoding": "json",
        "pgp_fingerprints": ["<PGP fingerprint>"],
        "data": "<PGP-enrypted blob>",
        "schema": "attachment",
        "version_major": 1,
        "version_minor": 0
    }
}
```

<aside class="notice">Requires authentication.</aside>

### Definition

`PUT /attachments/:id`

### Description

Updates specified attachment.

### Fields

| Key              | Type           | Description                                                               |
|:-----------------|:---------------|:--------------------------------------------------------------------------|
| data             | string         | PGP-encrypted attachment data                                                |
| name             | string         | Unencrypted name of the attachment                                           |
| encoding         | string         | Encrypted blob's encoding (for example `json`)                            |
| version_major    | int            | Major version of the encrypted blob                                       |
| version_minor    | int            | Minor version of the data, different minor versions should be compatible. |
| pgp_fingerprints | list\<string\> | PGP fingerprints used to encode a attachment                                 |

## Delete a attachment

```http
DELETE /attachments/<id> HTTP/1.1
User-Agent: LavaboomClient/1.0.0
Accept: application/json
Host: api.lavaboom.io
```

```javascript
api.attachments.delete("<id>").then(function(resp) {
    console.log(resp);
}).catch(function{err} {
    console.log(err);
})
```

```json
{
    "success": true,
    "message": "Attachment successfully removed"
}
```

<aside class="notice">Requires authentication.</aside>

### Definition

`DELETE /attachments/:id`

### Description

Deletes specified attachment.
