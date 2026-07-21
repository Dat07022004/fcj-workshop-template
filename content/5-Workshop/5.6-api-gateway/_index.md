---
title: "Create API Gateway for frontend-to-backend access"
date: 2024-01-01
weight: 6
chapter: false
pre: " <b> 5.6. </b> "
---

> Expected result: Create a stable HTTPS endpoint so the frontend can call REST APIs without calling the HTTP ALB directly.

## Prerequisites

* The backend ALB is healthy.

* The ALB DNS is known: `webdating-backend-alb-218383004.ap-southeast-1.elb.amazonaws.com`.

## Implementation Steps

1. Create an HTTP API in API Gateway in the `ap-southeast-1` region.

1. Create an HTTP proxy integration to `http://<alb-dns>/{proxy}`.

1. Create the route `ANY /{proxy+}` and connect it to the integration.

1. Create the route `OPTIONS /{proxy+}` to handle preflight requests when needed.

1. Configure CORS for frontend origins `https://vibematch.cloud` and `https://www.vibematch.cloud`.

1. Use the `$default` stage with auto deploy enabled.

### Reference Commands

```powershell
$API_GATEWAY_URL = "https://zsc1wtu6rc.execute-api.ap-southeast-1.amazonaws.com"
curl.exe "$API_GATEWAY_URL/api/health"
curl.exe "$API_GATEWAY_URL/api/health/db"
curl.exe -i -X OPTIONS "$API_GATEWAY_URL/api/users/me" -H "Origin: https://vibematch.cloud" -H "Access-Control-Request-Method: GET" -H "Access-Control-Request-Headers: Authorization,Content-Type"
```

## Completion Check

* `GET https://zsc1wtu6rc.execute-api.ap-southeast-1.amazonaws.com/api/health` returns 200.

* Preflight `OPTIONS /api/users/me` returns valid CORS headers.

* A protected route without token returns 401 while still returning the correct `access-control-allow-origin` header.

![API Gateway HTTP API created](/images/5-Workshop/5.6-api-gateway/api-gateway-http-api.png)

![ANY and OPTIONS routes for backend HTTP API](/images/5-Workshop/5.6-api-gateway/api-gateway-routes.png)
