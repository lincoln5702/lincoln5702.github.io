
# CloudWatch Logs

---
## 📚 Table of Contents
1. [CloudWatch Logs - Overview](#cloudwatch-logs---overview)
2. [Log Groups & Log Streams](#log-groups--log-streams)
3. [CloudWatch Logs Destinations](#cloudwatch-logs-destinations)
4. [Encryption of Logs](#encryption-of-logs)
5. [CloudWatch Logs Sources](#cloudwatch-logs-sources)
6. [CloudWatch Logs Insights](#cloudwatch-logs-insights)
7. [CloudWatch Logs S3 Export](#cloudwatch-logs-s3-export)
8. [CloudWatch Logs Subscriptions](#cloudwatch-logs-subscriptions)
9. [CloudWatch Logs for EC2](#cloudwatch-logs-for-ec2)
10. [CloudWatch Logs Agent vs Unified Agent](#cloudwatch-logs-agent-vs-unified-agent)
11. [CloudWatch Unified Agent Metrics](#cloudwatch-unified-agent-metrics)

---

## CloudWatch Logs - Overview
Amazon CloudWatch Logs is a centralized log management service that allows you to monitor, store, and access log files from various AWS services, applications, and systems.

### Key Concepts:
- **Log Groups**: Named containers for log streams, typically representing an application or a component.
- **Log Streams**: Sequences of log events from the same source, such as an EC2 instance or container.
- **Retention Policies**: You can set logs to never expire or define a retention period (1 day to 10 years).

---

## Log Groups & Log Streams
- **Log Group**:
  - A logical group representing an application or environment.
  - Example: `/aws/lambda/my-function`, `/ecs/app-logs`, etc.

- **Log Stream**:
  - A stream of logs coming from a particular instance, container, or log file.
  - Example: A log stream from a specific EC2 instance in an Auto Scaling group.

---

## CloudWatch Logs Destinations
CloudWatch Logs can stream or export logs to:
- **Amazon S3** (for batch exports)
- **Kinesis Data Streams** (real-time log processing)
- **Kinesis Data Firehose** (near real-time delivery to S3, Redshift, OpenSearch)
- **AWS Lambda** (real-time processing/alerts)
- **OpenSearch** (log analytics and search)

### Export Types:
| Destination | Real-Time | Use Case |
|------------|-----------|----------|
| S3 | ❌ Batch | Archival, long-term storage |
| Kinesis Streams | ✅ | Custom stream processing |
| Kinesis Firehose | ✅ | Deliver to S3, Redshift, OpenSearch |
| Lambda | ✅ | Alerts, transformations |
| OpenSearch | ✅ | Log search, visualization |

---

## Encryption of Logs
- **By default**, logs are encrypted at rest using CloudWatch service-managed encryption.
- **Optional**: Use **KMS (Key Management Service)** to encrypt logs with your own custom keys.

---

## CloudWatch Logs Sources

Logs can be sent from various AWS services and environments:
- **SDK / CloudWatch Logs Agent / Unified Agent**
- **Elastic Beanstalk**: Automatically collects application logs.
- **ECS (Elastic Container Service)**: Collects container logs.
- **Lambda**: Automatically sends function logs to CloudWatch.
- **VPC Flow Logs**: Captures traffic flow logs.
- **API Gateway**: Request/response logs.
- **CloudTrail**: Event history (when integrated via filters).
- **Route53**: DNS query logs.

---

## CloudWatch Logs Insights

A query engine for **searching and analyzing** logs in CloudWatch.
### Features:
- Search for patterns or values (e.g., find specific IPs or count ERROR messages).
- Purpose-built **query language**.
- Auto-discovers fields in JSON logs.
- Supports filtering, aggregation, sorting, and field extraction.
- Queries can be saved and added to **CloudWatch Dashboards**.
- Supports **multi-log group** and **cross-account** queries.

### Example Query:
```sql
fields @timestamp, @message
| filter @message like /ERROR/
| sort @timestamp desc
| limit 20
```

> Note: Logs Insights is a **query engine**, not a real-time processor.

---

## CloudWatch Logs S3 Export
Exports logs to Amazon S3 **in batch**, not real-time.

### Key Points:
- Logs can take **up to 12 hours** to become available for export.
- Use `CreateExportTask` API to start an export job.
- Good for **archiving**, **compliance**, or **offline analysis**.

### Limitations:
- ❌ Not real-time
- ✅ Use **Log Subscriptions** for live data streaming instead

---

## CloudWatch Logs Subscriptions
Used to **stream logs in real-time or near real-time** to other AWS services.

### Destinations:
- **Lambda**: Real-time processing
- **Kinesis Data Streams**: Advanced custom pipelines
- **Kinesis Data Firehose**: Delivery to S3, Redshift, OpenSearch
- **OpenSearch**: Direct search + visualization

### Subscription Filters:
- Define which logs to send, using **filter patterns**.
```text
ERROR
"GET /index.html"
{ $.statusCode = 500 }
```

### Cross-Account Logging:
- CloudWatch logs from one account can be **streamed to another** using Kinesis with proper IAM permissions.

### Multi-Region Aggregation:
- Multiple log groups (from multiple regions or accounts) can stream logs to a **centralized Firehose or OpenSearch** setup for unified monitoring.

---

## CloudWatch Logs for EC2
By default, EC2 instances **do not send any logs** to CloudWatch.

### To enable logging:
1. Install the **CloudWatch Agent**.
2. Configure which logs to push.
3. Attach an IAM Role with permissions to send logs.
4. Agent pushes log data to specified **log group/stream**.

> Also works for **on-premises servers**, enabling hybrid cloud monitoring.

---

## CloudWatch Logs Agent vs Unified Agent

| Feature | CloudWatch Logs Agent | CloudWatch Unified Agent |
|--------|------------------------|----------------------------|
| Logs | ✅ | ✅ |
| Custom Metrics | ❌ | ✅ |
| Legacy | ✅ | ❌ |
| Centralized Config (SSM) | ❌ | ✅ |
| Flexibility | Low | High |

> Recommendation: **Use Unified Agent** for all new setups.

---

## CloudWatch Unified Agent Metrics
The Unified Agent can collect **detailed system metrics** directly from your EC2 or on-prem server.

### Categories & Metrics:
- **CPU**: user, system, idle, steal, guest
- **Disk**: used, free, total space; read/write rates, IOPS
- **RAM**: total, used, free, inactive, cached
- **Network**: TCP/UDP connections, packets, bytes
- **Processes**: total, running, blocked, sleeping
- **Swap**: total, used, free, percent used

> These go **beyond default EC2 metrics**, giving much deeper insight into system performance.

---

## ✅ Summary
- CloudWatch Logs centralizes logging from AWS and non-AWS sources.
- You can analyze logs with **Logs Insights**, export them to **S3**, or **stream them in real time**.
- Use the **Unified Agent** for advanced metrics and log collection.
- Build **cross-account, multi-region** logging architectures using subscriptions.

---
