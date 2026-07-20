---
title: "Blog 2 - Processing Amazon S3 objects at scale with AWS Step Functions Distributed Map"
date: 2024-01-01
weight: 2
chapter: false
pre: " <b> 3.2. </b> "
---

This blog discusses how AWS Step Functions Distributed Map can simplify large-scale processing of Amazon S3 objects. In modern data systems, S3 often stores application logs, customer data, machine learning datasets, reports, and event data. As the number of objects grows, the main challenge becomes processing thousands or millions of objects reliably, in parallel, and with less operational overhead.

The key idea is combining S3 prefix iteration with `LOAD_AND_FLATTEN`. With this approach, a workflow can use `S3ListObjectsV2` to find objects under a specific prefix, then read and flatten the contents of those files into individual records inside the same Map state.

![AWS Step Functions Distributed Map S3 workflow](/images/3-BlogsPosted/3.2-Blog2/step-functions-distributed-map-s3.png)

## Main ideas

* Distributed Map runs many processing branches in parallel for large datasets.
* `LOAD_AND_FLATTEN` lets Step Functions process the actual contents of S3 objects, not only object metadata.
* Supported data formats include CSV, JSON, JSONL, and PARQUET.
* A log analytics workflow can count `INFO`, `WARNING`, and `ERROR` records, publish CloudWatch metrics, store summaries in DynamoDB, and invoke Lambda for final aggregation.
* Prefixes should be designed carefully, usually ending with `/`, to avoid accidentally matching unrelated objects.
* Objects under the same prefix should use the same data format.

## Personal takeaway

This feature reduces the amount of custom orchestration code needed in data pipelines. Instead of separately listing objects, building manifests, reading files, parsing records, and coordinating parallel processing, several of those steps can be handled inside one Distributed Map state.

For serverless and event-driven systems, this makes S3 processing workflows easier to scale and operate. It is especially useful for log analysis, batch processing, data lake ingestion, reporting, and machine learning data preparation.

Facebook post: [View Blog 2 on AWS Study Group](https://www.facebook.com/groups/awsstudygroupfcj/posts/2206913116740315)
