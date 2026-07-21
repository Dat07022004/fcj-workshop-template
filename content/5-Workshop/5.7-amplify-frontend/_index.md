---
title: "Deploy frontend on AWS Amplify"
date: 2024-01-01
weight: 7
chapter: false
pre: " <b> 5.7. </b> "
---

> Expected result: The VibeMatch frontend runs on Amplify Hosting, uses a custom domain, and calls the backend through API Gateway.

## Prerequisites

* The frontend can be built with Vite/React.

* The API Gateway REST endpoint is working.

* The HTTPS socket domain has been configured or will be configured in the next section.

## Implementation Steps

1. Create an Amplify app and connect the frontend repository/branch.

1. Configure the app root as `frontend` if the project is a monorepo.

1. Add frontend environment variables.

1. Deploy the production/feature branch.

1. Configure SPA rewrite so routes such as `/discover` and `/profile` do not return 404 when refreshed.

1. Attach the custom domain `vibematch.cloud` to Amplify.

### Reference Configuration

```text
VITE_API_URL=https://zsc1wtu6rc.execute-api.ap-southeast-1.amazonaws.com
VITE_SOCKET_URL=https://socket.vibematch.cloud

SPA rewrite:
Source: /<*>
Target: /index.html
Type: 404 (Rewrite)
```

## Completion Check

* The frontend opens through `https://vibematch.cloud`.

* SPA routes do not return 404 after browser refresh.

* The frontend calls the API through `VITE_API_URL` without CORS errors.

* Clerk login works and profile, onboarding, and discover flows can call the backend.

![Route 53 hosted zone for vibematch.cloud](/images/5-Workshop/5.7-amplify-frontend/route53-hosted-zone.png)

![Amplify app deployed](/images/5-Workshop/5.7-amplify-frontend/amplify-app-deployed.png)

![Amplify environment variables](/images/5-Workshop/5.7-amplify-frontend/amplify-environment-variables.png)
