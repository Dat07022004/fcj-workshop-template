---
title: "Create VPC, subnets, and network layer"
date: 2024-01-01
weight: 3
chapter: false
pre: " <b> 5.3. </b> "
---

> Expected result: Create a layered network where the ALB is in public subnets, backend EC2 instances are in private application subnets, and DocumentDB is in private database subnets.

## Prerequisites

* The `ap-southeast-1` region has been selected.

* A VPC CIDR block is available, for example `10.20.0.0/16`.

## Implementation Steps

1. Create the VPC `webdating-vpc` and enable DNS support and DNS hostnames.

1. Create 2 public subnets in 2 Availability Zones for the internet-facing ALB.

1. Create 2 private application subnets in 2 Availability Zones for backend EC2 instances managed by Auto Scaling Group.

1. Create 2 private database subnets in 2 Availability Zones for the DocumentDB subnet group.

1. Attach an Internet Gateway to the VPC and create a `0.0.0.0/0` route for the public route table.

1. Create NAT Gateway for private application subnets if outbound internet access is required. For high availability, use 2 NAT Gateways, one per AZ.

1. Create VPC endpoints for ECR API, ECR Docker, S3, CloudWatch Logs, SSM, SSM Messages, Secrets Manager, and STS so private EC2 instances can operate reliably.

1. Create security groups: `alb-sg`, `ec2-backend-sg`, `docdb-sg`, and `endpoint-sg`.

## Completion Check

* The public route table has a route to the Internet Gateway.

* The private application route table has a route to NAT Gateway or enough required VPC endpoints.

* `alb-sg` allows ports 80/443 from the internet.

* `ec2-backend-sg` only accepts port 3000 from `alb-sg`.

* `docdb-sg` only accepts port 27017 from `ec2-backend-sg`.

![VPC created with DNS support and hostnames enabled](/images/5-Workshop/5.3-network-layer/vpc-details.png)

![Subnets, route tables, and network connections](/images/5-Workshop/5.3-network-layer/subnets-route-tables-network.png)

![NAT Gateways for private application subnets](/images/5-Workshop/5.3-network-layer/nat-gateways.png)

![VPC endpoints created](/images/5-Workshop/5.3-network-layer/vpc-endpoints.png)

![Security group inbound rules for VPC endpoint](/images/5-Workshop/5.3-network-layer/endpoint-security-group.png)
