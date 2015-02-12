# Introduction

```http
GET / HTTP/1.1
User-Agent: LavaboomClient/1.0.0
Accept: application/json
Host: api.lavaboom.io
```

```javascript
api.info("asd").then(function(resp) {
    console.log(resp);
}).catch(function{err} {
    console.log(err);
})
```

```json
{
    "message": "Lavaboom API",
    "docs_url": "https://docs.lavaboom.io/",
    "version": "v0"
}
```

Welcome to the Lavaboom API documentation. This is the API used by the official Lavaboom web interface, so everything done there can be also performed via the API.

There are multiple endpoints for you to use:

| Name | API version     | URL                       | Public |
|:-----|:----------------|:--------------------------|:-------|
| prod | _(not running)_ | https://api.lavaboom.com/ | Yes    |
| staging  | v0.x              | https://api.lavaboom.io/  | No     |
| dev  | v0.x              | https://api.lavaboom.co/  | No     |

Please note that any non-production API request must contain a special `X-Lavaboom-Key` token to be authorized.

Available client libraries:

 * [JavaScript](https://github.com/lavab/api-client-js)
