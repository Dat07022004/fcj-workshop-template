---
title: "Configure Route 53, ACM, and HTTPS for Socket.IO"
date: 2024-01-01
weight: 8
chapter: false
pre: " <b> 5.8. </b> "
---

> Expected result: Create a dedicated HTTPS domain for Socket.IO because an HTTPS frontend cannot call an HTTP socket endpoint.

## Prerequisites

* The `vibematch.cloud` domain nameservers point to Route 53.

* The backend ALB already has a healthy target group.

## Implementation Steps

1. Create a hosted zone or use the existing hosted zone for `vibematch.cloud`.

1. Create an A Alias record `socket.vibematch.cloud` pointing to the ALB.

1. Request an ACM certificate for `socket.vibematch.cloud` in the `ap-southeast-1` region.

1. Validate the certificate using the DNS record in Route 53.

1. Create an HTTPS listener on port 443 for the ALB and attach the ACM certificate.

1. Forward listener 443 to the backend target group on port 3000.

1. Open inbound port 443 on the ALB security group.

1. Update the frontend variable `VITE_SOCKET_URL=https://socket.vibematch.cloud` and redeploy Amplify.

## Completion Check

* `curl.exe -i https://socket.vibematch.cloud/api/health` returns 200 when the target group is healthy.

* The browser no longer shows Mixed Content errors for Socket.IO.

* Socket.IO should prioritize WebSocket transport, or ALB stickiness should be enabled if polling is still used.

![Route 53 records for vibematch.cloud](/images/5-Workshop/5.7-5.11-frontend/workshop-frontend-04.png)

![ACM certificate issued](/images/5-Workshop/5.7-5.11-frontend/workshop-frontend-05.png)

![ALB HTTPS listener configuration](/images/5-Workshop/5.7-5.11-frontend/workshop-frontend-06.png)
