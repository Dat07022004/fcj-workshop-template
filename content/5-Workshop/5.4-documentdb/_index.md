---
title: "Create Amazon DocumentDB"
date: 2024-01-01
weight: 4
chapter: false
pre: " <b> 5.4. </b> "
---

> Expected result: Create a private DocumentDB cluster with 1 writer/primary instance and 1 reader replica as the standby/failover target.

## Prerequisites

* Private database subnets are available in 2 Availability Zones.

* `docdb-sg` allows port 27017 from the backend EC2 security group.

## Implementation Steps

1. Create a DB subnet group from 2 private database subnets.

1. Create a cluster parameter group for DocumentDB 5.0 and keep TLS enabled.

1. Create the `webdating-docdb` cluster with a master username such as `webdating_admin`.

1. Create the writer instance in one Availability Zone.

1. Create a reader replica in another Availability Zone as the failover target.

1. Wait until the cluster and instances become `available`.

1. Get the cluster endpoint and reader endpoint.

1. Create the `/webdating/backend/DATABASE_URL` secret in Secrets Manager.

### Reference Commands

```powershell
$DOCDB_ENDPOINT = "webdating-docdb.cluster-cfu04me6ymt5.ap-southeast-1.docdb.amazonaws.com"
$DATABASE_URL = "mongodb://webdating_admin:<docdb-password>@$DOCDB_ENDPOINT:27017/webdating?tls=true&tlsCAFile=/etc/docdb/global-bundle.pem&replicaSet=rs0&readPreference=secondaryPreferred&retryWrites=false&authSource=admin&authMechanism=SCRAM-SHA-1"
aws secretsmanager create-secret --region
$AWS_REGION --name "/webdating/backend/DATABASE_URL" --secret-string
$DATABASE_URL
```

## Completion Check

* The DocumentDB cluster has an endpoint like `webdating-docdb.cluster-...ap-southeast-1.docdb.amazonaws.com`.

* There are 2 instances: 1 writer and 1 reader replica.

* The DocumentDB security group only opens port 27017 from the backend EC2 security group.

* `DATABASE_URL` includes `tls=true`, `tlsCAFile`, `replicaSet=rs0`, `retryWrites=false`, `authSource=admin`, and `authMechanism=SCRAM-SHA-1`.

![DocumentDB cluster webdating-docdb with writer and reader instances available](/images/5-Workshop/5.4-documentdb/documentdb-cluster.png)
