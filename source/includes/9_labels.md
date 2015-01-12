# Labels

## List all labels

```http
GET /labels HTTP/1.1
User-Agent: LavaboomClient/1.0.0
Accept: application/json
Host: api.lavaboom.io
```

```javascript
api.labels.list().then(function(resp) {
    console.log(resp);
}).catch(function{err} {
    console.log(err);
})
```

```json
{
    "success": true,
    "labels": [
        {
            "id": "<id>",
            "date_created": "<RFC3339Nano date>",
            "date_modified": "<RFC3339Nano date>",
            "name": "Inbox",
            "owner": "<user id>",
            "builtin": true,
            "emails_unread": 0,
            "emails_total": 0
        }
    ]
}
```

<aside class="notice">Requires authentication.</aside>

### Definition

`GET /labels`

### Description

Returns all labels owned by the user.

## Get a label

```http
GET /labels/:id HTTP/1.1
User-Agent: LavaboomClient/1.0.0
Accept: application/json
Host: api.lavaboom.io
```

```javascript
api.labels.get("<id>").then(function(resp) {
    console.log(resp);
}).catch(function{err} {
    console.log(err);
})
```

```json
{
    "success": true,
    "label": {
        "id": "<id>",
        "date_created": "<RFC3339Nano date>",
        "date_modified": "<RFC3339Nano date>",
        "name": "Inbox",
        "owner": "<user id>",
        "builtin": true,
        "emails_unread": 0,
        "emails_total": 0
    }
}
```

<aside class="notice">Requires authentication.</aside>

### Definition

`GET /labels/:id`

### Description

Retrieves a label by its ID.

## Create a new label

```http
POST /labels HTTP/1.1
User-Agent: LavaboomClient/1.0.0
Accept: application/json
Host: api.lavaboom.io
Content-Type: application/json
Content-Length: 27

{
    "name": "Something"
}
```

```javascript
api.labels.create({
    "name": "Something"
}).then(function(resp) {
    console.log(resp);
}).catch(function{err} {
    console.log(err);
})
```

```json
{
    "success": true,
    "label": {
        "id": "<id>",
        "date_created": "<RFC3339Nano date>",
        "date_modified": "<RFC3339Nano date>",
        "name": "Something",
        "owner": "<user id>",
        "emails_unread": 0,
        "emails_total": 0
    }
}
```

<aside class="notice">Requires authentication.</aside>

### Definition

`POST /labels`

### Description

Creates a new label.

### Fields

| Key  | Type   | Description       |
|:-----|:-------|:------------------|
| name | string | Name of the label |


## Delete a label

```http
DELETE /labels/:id HTTP/1.1
User-Agent: LavaboomClient/1.0.0
Accept: application/json
Host: api.lavaboom.io
```

```javascript
api.labels.delete("<id>").then(function(resp) {
    console.log(resp);
}).catch(function{err} {
    console.log(err);
})
```

```json
{
    "success": true
}
```

<aside class="notice">Requires authentication.</aside>

### Definition

`DELETE /labels/:id`

### Description

Deletes specified label.

## Update a label

```http
PUT /labels/:id HTTP/1.1
User-Agent: LavaboomClient/1.0.0
Accept: application/json
Host: api.lavaboom.io
Content-Type: application/json
Content-Length: 27

{
    "name": "Something"
}
```

```javascript
api.labels.update("<id>", {
    "name": "Something #2"
}).then(function(resp) {
    console.log(resp);
}).catch(function{err} {
    console.log(err);
})
```

```json
{
    "success": true,
    "label": {
        "id": "<id>",
        "date_created": "<RFC3339Nano date>",
        "date_modified": "<RFC3339Nano date>",
        "name": "Something #2",
        "owner": "<user id>",
        "emails_unread": 0,
        "emails_total": 0
    }
}
```

<aside class="notice">Requires authentication.</aside>

### Definition

`PUT /labels/:id`

### Description

Updates an existing label.

### Fields

| Key  | Type   | Description       |
|:-----|:-------|:------------------|
| name | string | Name of the label |
