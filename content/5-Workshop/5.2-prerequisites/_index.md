---
title: "Environment preparation"
date: 2024-01-01
weight: 2
chapter: false
pre: " <b> 5.2. </b> "
---

> Expected result: All required tools and input information are ready for the AWS deployment labs.

## Implementation Steps

1. Sign in to the AWS Console with an account that can manage VPC, EC2, ECR, DocumentDB, API Gateway, Amplify, Route 53, ACM, WAF, and Secrets Manager.

1. Install AWS CLI and configure the profile with `aws configure`.

1. Prepare the backend/frontend source code of the VibeMatch project.

1. Prepare Docker to build the backend image.

1. Prepare the Clerk project to get the publishable key, secret key, and bearer token for testing protected APIs.

1. Prepare the domain `vibematch.cloud`, or another domain if a custom domain is used.

### Reference Commands

```powershell
$AWS_REGION = "ap-southeast-1"
$APP_NAME = "webdating"
aws sts get-caller-identity
docker --version
```

## Completion Check

* `aws sts get-caller-identity` runs successfully and returns the Account ID.

* The backend image can be built successfully on the local machine.

* AWS Console access is available and services can be created in the `ap-southeast-1` region.

