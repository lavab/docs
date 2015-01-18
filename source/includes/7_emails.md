# Emails

## List emails

```http
GET /emails?sort=+date_created HTTP/1.1
User-Agent: LavaboomClient/1.0.0
Accept: application/json
Host: api.lavaboom.io
```

```javascript
api.emails.list({
    "sort": "+date_created"
}).then(function(resp) {
    console.log(resp);
}).catch(function{err} {
    console.log(err);
})
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
            "from": "broom@lavaboom.com",
            "cc": [],
            "bcc": [],
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
Content-Length: 161

{
    "to": "johndoe@inferiormail.com",
    "subject": "Lavaboom master race!",
    "is_encrypted": false,
    "body": "Check out this cool new email service!"
}
```

```javascript
api.emails.create({
    "to": "johndoe@inferiormail.com",
    "subject": "Lavaboom master race!",
    "is_encrypted": false,
    "body": "Check out this cool new email service!"
}).then(function(resp) {
    console.log(resp);
}).catch(function{err} {
    console.log(err);
})
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

| Name                    | Type             | Description                                                                                                       |
| :---------------------- | :--------------- | :---------------------------------------------------------------------------------------------------------------- |
| to                      | list\<string\>   | Who should receive the email?                                                                                     |
| cc                      | list\<string\>   | Carbon copy                                                                                                       |
| bcc                     | list\<string\>   | Blind carbon copy                                                                                                 |
| reply_to                | string           | Reply-to field's value                                                                                            |
| thread_id               | string           | ID of the thread (can be null)                                                                                    |
| subject                 | string           | Subject of the email                                                                                              |
| body                    | string           | PGP-encrypted email body (or raw)                                                                                 |
| body_version_major      | int              | Major version of the body's blob                                                                                  |
| body_version_minor      | int              | Minor version of the body's blob                                                                                  |
| preview                 | string           | PGP-encrypted preview body (or raw)                                                                               |
| preview_version_major   | int              | Major version of the preview's blob                                                                               |
| preview_version_minor   | int              | Minor version of the preview's blob                                                                               |
| attachments             | list\<string\>   | Attachment IDs                                                                                                    |
| pgp_fingerprints        | list\<string\>   | Fingerprints of keys used to encrypt the body and preview. If this field is empty, then the email is unencrypted. |

## Get an email

```http
GET /emails/<id> HTTP/1.1
User-Agent: LavaboomClient/1.0.0
Accept: application/json
Host: api.lavaboom.io
```

```javascript
api.emails.get("<id>").then(function(resp) {
    console.log(resp);
}).catch(function{err} {
    console.log(err);
})
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

```javascript
api.emails.delete("<id>").then(function(resp) {
    console.log(resp);
}).catch(function{err} {
    console.log(err);
})
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
