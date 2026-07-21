---
title: "Test the system"
date: 2024-01-01
weight: 10
chapter: false
pre: " <b> 5.10. </b> "
---

> Expected result: Confirm that the frontend, REST API, backend container, and DocumentDB work correctly after deployment.

## Prerequisites

* The frontend URL, API Gateway URL, and socket domain are available.

* A Clerk test account is available to obtain a bearer token.

## Implementation Steps

1. Call the health API through API Gateway.

1. Call the DB health API to confirm that the backend can connect to DocumentDB.

1. Use Postman to call a protected route without a token and confirm it returns 401.

1. Get the Clerk bearer token from a logged-in frontend session and call `/api/users/me`.

1. Run the onboarding/profile flow to confirm DocumentDB write/read behavior.

1. Open browser DevTools and check requests from the frontend to API Gateway and Socket.IO traffic to `socket.vibematch.cloud`.

1. Check that the target group has 2 healthy instances.

### Reference Commands

```powershell
curl.exe "https://zsc1wtu6rc.execute-api.ap-southeast-1.amazonaws.com/api/health"
curl.exe "https://zsc1wtu6rc.execute-api.ap-southeast-1.amazonaws.com/api/health/db"
curl.exe -H "Authorization: Bearer <clerk-token>" "https://zsc1wtu6rc.execute-api.ap-southeast-1.amazonaws.com/api/users/me"
```

## Completion Check

* `/api/health` returns 200.

* `/api/health/db` returns connected.

* Protected APIs work with a valid token.

* The frontend supports login, profile, discover, and main screens without CORS errors.

* Postman confirms that the backend API and DB connection work correctly.

![VibeMatch homepage on custom domain](/images/5-Workshop/5.7-5.11-frontend/workshop-frontend-09.png)

![VibeMatch notifications page](/images/5-Workshop/5.7-5.11-frontend/workshop-frontend-10.png)

![VibeMatch matches page](/images/5-Workshop/5.7-5.11-frontend/workshop-frontend-11.png)

![VibeMatch match detail test](/images/5-Workshop/5.7-5.11-frontend/workshop-frontend-12.png)

![VibeMatch messaging page](/images/5-Workshop/5.7-5.11-frontend/workshop-frontend-13.png)

![VibeMatch video call test](/images/5-Workshop/5.7-5.11-frontend/workshop-frontend-14.png)

![VibeMatch profile page](/images/5-Workshop/5.7-5.11-frontend/workshop-frontend-15.png)
