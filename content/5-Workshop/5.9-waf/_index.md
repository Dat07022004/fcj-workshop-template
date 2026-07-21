---
title: "Enable WAF for the frontend"
date: 2024-01-01
weight: 9
chapter: false
pre: " <b> 5.9. </b> "
---

> Expected result: Protect the frontend access layer with Amplify Firewall/AWS WAF.

## Prerequisites

* The frontend is running on AWS Amplify.

* The custom domain is stable.

## Implementation Steps

1. Open the Amplify app `webdating`.

1. Go to Hosting -> Firewall.

1. Enable `Enable Amplify-recommended Firewall protection`.

1. Check WAF & Shield and confirm that a Web ACL has been created by Amplify.

1. If the default Amplify domain should be locked down, enable `Restrict access to amplifyapp.com` after the custom domain is stable.

1. Monitor sampled requests and CloudWatch metrics to detect unusual requests.

## Completion Check

* The Web ACL `CreatedByAmplify-...` exists in AWS WAF.

* The frontend is still accessible through `https://vibematch.cloud`.

* Do not create another Web ACL for the frontend if Amplify Firewall already manages this Web ACL.

![Amplify Firewall recommended protection enabled](/images/5-Workshop/5.7-5.11-frontend/workshop-frontend-07.png)

![AWS WAF Web ACL created by Amplify](/images/5-Workshop/5.7-5.11-frontend/workshop-frontend-08.png)
