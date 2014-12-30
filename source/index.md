---
language_tabs:
  - http
  - shell

toc_footers:
  - © 2014 Lavaboom GmbH

includes:
  - errors

search: true
---

# Introduction

Welcome to the Lavaboom API documentation. This is the APi used by the official Lavaboom web interface, so everything done there can be also performed via the API.

There are multiple endpoints for you to use:

| Name    | API version     | URL                         | Public |
| ------- | --------------- | --------------------------- | ------ |
| prod    | _(not running)_ | http://api.lavaboom.com/v1/ | Yes    |
| dev     | v0              | http://api.lavaboom.io/v0/  | No     |

Please note that any non-production API request must contain a special `X-Lavaboom-Key` token to be authorized.

# SockJS endpoint

There are two ways to execute API requests:

 - Connect to the SockJS endpoint and perform communication using it.
 - Directly connect to the HTTP API.

The endpoint is available at `/ws` on every API.

## Message format

Each message sent through the SockJS connection is encoded using JSON. You can identify its kind by accessing its `type` key.

| Request type | Result type  | Description                   |
| ------------ | ------------ | ----------------------------- |
| request      | result       | Executes a HTTP request       |
| subscribe    | subscribed   | Subscribes to user events     |
| unsubscribe  | unsubscribed | Unsubscribes from user events |

## Requests

```shell
sockjs-cli http://api.lavaboom.com/v0/ws << EOF
{
    "type": "request",
    "id": "123",
    "method": "GET",
    "path": "/"
}
:quit
EOF;
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
| ------- | ---------------- | ---------------------------------------- |
| id      | string           | ID returned with the result message      |
| method  | string           | Request method. GET, POST, PUT or DELETE |
| path    | string           | Path to query (for example `/tokens`)    |
| body    | string           | Request body (not required).             |
| headers | map\<string\>any | Headers to pass with the request         |

### Result message fields

| Key     | Type             | Description                         |
| ------- | ---------------- | ----------------------------------- |
| id      | string           | ID passed with the request message  |
| status  | int              | Status of the request               |
| headers | map\<string\>any | Headers generated with the response |
| body    | string           | Body of the response                |


## Subscriptions

```shell
sockjs-cli http://api.lavaboom.com/v0/ws << EOF
{
    "type": "subscribe",
    "token": "valid token",
}
:quit
EOF;
```

```json
{
    "type": "subscribed"
}
```

```shell
sockjs-cli http://api.lavaboom.com/v0/ws << EOF
{
    "type": "unsubscribe",
}
:quit
EOF;
```

```json
{
    "type": "unsubscribed"
}
```

In order to listen to events, you have to "log in" to the account using the `subscribe` message. Then, if you want to log out, send an `unsubscribe` message or log out.

### Subscribe message fields (request)

| Key   | Type   | Description                 |
| ----- | ------ | --------------------------- |
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
| ---- | ------ | ----------------------------------------------------- |
| id   | string | ID of the delivered message                           |
| name | string | Subject of the delivered message (might be encrypted) |

### Receipt fields

| Key  | Type   | Description                                           |
| ---- | ------ | ----------------------------------------------------- |
| id   | string | ID of the received message                            |
| name | string | Subject of the received message  (might be encrypted) |

# Authentication

> To authorize, use this code:

```http
require 'kittn'

api = Kittn::APIClient.authorize!('meowmeowmeow')
```

```shell
# With shell, you can just pass the correct header with each request
curl "api_endpoint_here"
  -H "Authorization: meowmeowmeow"
```

> Make sure to replace `meowmeowmeow` with your API key.

Kittn uses API keys to allow access to the API. You can register a new Kittn API key at our [developer portal](http://example.com/developers).

Kittn expects for the API key to be included in all API requests to the server in a header that looks like the following:

`Authorization: meowmeowmeow`

<aside class="notice">
You must replace `meowmeowmeow` with your personal API key.
</aside>

# Kittens

## Get All Kittens

```http
require 'kittn'

api = Kittn::APIClient.authorize!('meowmeowmeow')
api.kittens.get
```

```shell
curl "http://example.com/api/kittens"
  -H "Authorization: meowmeowmeow"
```

> The above command returns JSON structured like this:

```json
[
  {
    "id": 1,
    "name": "Fluffums",
    "breed": "calico",
    "fluffiness": 6,
    "cuteness": 7
  },
  {
    "id": 2,
    "name": "Isis",
    "breed": "unknown",
    "fluffiness": 5,
    "cuteness": 10
  }
]
```

This endpoint retrieves all kittens.

### HTTP Request

`GET http://example.com/kittens`

### Query Parameters

Parameter | Default | Description
--------- | ------- | -----------
include_cats | false | If set to true, the result will also include cats.
available | true | If set to false, the result will include kittens that have already been adopted.

<aside class="success">
Remember — a happy kitten is an authenticated kitten!
</aside>

## Get a Specific Kitten

```ruby
require 'kittn'

api = Kittn::APIClient.authorize!('meowmeowmeow')
api.kittens.get(2)
```

```python
import kittn

api = kittn.authorize('meowmeowmeow')
api.kittens.get(2)
```

```shell
curl "http://example.com/api/kittens/3"
  -H "Authorization: meowmeowmeow"
```

> The above command returns JSON structured like this:

```json
{
  "id": 2,
  "name": "Isis",
  "breed": "unknown",
  "fluffiness": 5,
  "cuteness": 10
}
```

This endpoint retrieves a specific kitten.

<aside class="warning">If you're not using an administrator API key, note that some kittens will return 403 Forbidden if they are hidden for admins only.</aside>

### HTTP Request

`GET http://example.com/kittens/<ID>`

### URL Parameters

Parameter | Description
--------- | -----------
ID | The ID of the cat to retrieve
