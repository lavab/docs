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
