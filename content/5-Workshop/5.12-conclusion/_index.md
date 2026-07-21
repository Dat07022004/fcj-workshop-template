---
title: "Conclusion"
date: 2024-01-01
weight: 12
chapter: false
pre: " <b> 5.12. </b> "
---

After completing the workshop, the VibeMatch system has a frontend running on AWS Amplify, REST API access through API Gateway, a Docker backend running on EC2 Auto Scaling behind an ALB, and application data stored in Amazon DocumentDB inside private subnets. Domain, HTTPS, and WAF were configured to improve user access and frontend protection. Testing with Postman and the browser confirmed that the API, Clerk authentication, and database connection work correctly.

* FCJ workshop template: https://github.com/Dat07022004/fcj-workshop-template

* AWS DocumentDB Developer Guide: https://docs.aws.amazon.com/documentdb/latest/developerguide/

* AWS API Gateway HTTP API Guide: https://docs.aws.amazon.com/apigateway/latest/developerguide/http-api.html

* AWS Amplify Hosting Documentation: https://docs.aws.amazon.com/amplify/latest/userguide/welcome.html

* AWS WAF Developer Guide: https://docs.aws.amazon.com/waf/latest/developerguide/
