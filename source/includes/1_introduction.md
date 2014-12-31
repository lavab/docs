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