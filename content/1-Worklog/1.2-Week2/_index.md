---
title: "Worklog Week 2"
date: 2024-01-01
weight: 2
chapter: false
pre: " <b> 1.2. </b> "
---

## Work Completed

* Created a Route Table and configured outbound internet access through an Internet Gateway.
* Manually associated two public subnets with the public route table.
* Learned how to isolate private subnets to protect resources that should not be directly accessible from the internet.
* Studied how to divide a VPC into `/24` subnets using a Multi-AZ architecture across `ap-southeast-1a` and `ap-southeast-1b`.

## Results Achieved

* Understood the role of Route Tables, Internet Gateways, and subnet associations.
* Learned how to design public and private subnets in the same VPC.
* Understood the principle of distributing subnets across multiple Availability Zones for higher availability.
* Built the networking foundation needed for backend, load balancer, and database deployment.
