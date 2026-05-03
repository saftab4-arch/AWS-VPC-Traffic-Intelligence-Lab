# AWS VPC Traffic Intelligence Lab

### (VPC Flow Logs + S3 + Athena)

---

## 📌 Overview

In this project, I built a lightweight network observability pipeline in AWS to capture, store, and analyze real VPC traffic using **VPC Flow Logs**, **Amazon S3**, and **Amazon Athena**.

The goal was to simulate real-world cloud monitoring and gain visibility into inbound/outbound traffic, rejected connections, and potential security threats.

---

## 🏗️ Architecture

```
EC2 Instance (Traffic Generator)
        ↓
VPC Flow Logs (ALL traffic)
        ↓
Amazon S3 (AWSLogs storage)
        ↓
Amazon Athena (SQL queries)
        ↓
Traffic Analysis & Security Insights
```

---

## ⚙️ Services Used

* Amazon VPC (Flow Logs)
* Amazon EC2 (Traffic generation)
* Amazon S3 (Log storage)
* Amazon Athena (Serverless query engine)

---

## 🔧 Implementation Steps

1. Created an S3 bucket to store VPC Flow Logs
2. Launched an EC2 instance in the default VPC
3. Enabled **VPC Flow Logs** at VPC level

   * Filter: ALL traffic
   * Destination: S3
4. Generated traffic from EC2 (curl, ping, package updates)
5. Verified log delivery in S3 (`AWSLogs/.../vpcflowlogs/...`)
6. Configured Athena query result location
7. Created database and external table over S3 logs
8. Queried logs using SQL for analysis

---

## 🔍 Key Queries

### Top Source IPs

```sql
SELECT srcaddr, COUNT(*) AS hits
FROM vpc_flow_logs
GROUP BY srcaddr
ORDER BY hits DESC
LIMIT 10;
```

### Rejected Traffic (Security Analysis)

```sql
SELECT srcaddr, dstport, COUNT(*) AS blocked_attempts
FROM vpc_flow_logs
WHERE action = 'REJECT'
GROUP BY srcaddr, dstport
ORDER BY blocked_attempts DESC;
```

### Most Targeted Ports

```sql
SELECT dstport, COUNT(*) AS hits
FROM vpc_flow_logs
GROUP BY dstport
ORDER BY hits DESC;
```

### Protocol Distribution

```sql
SELECT protocol, COUNT(*) AS count
FROM vpc_flow_logs
GROUP BY protocol;
```

---

## 🧠 Key Observations

* Immediately after launching an EC2 instance with a public IP, unsolicited inbound traffic was observed
* Multiple external IPs attempted connections across various ports
* Most suspicious traffic was blocked (REJECT) by security groups
* Demonstrates real-world internet scanning and background noise

---

## 🔐 Security Insight

This project highlights how:

* Public cloud resources are constantly scanned
* Security Groups act as the first line of defense
* VPC Flow Logs provide visibility into network-level activity
* Athena enables fast investigation using SQL

---

## ⚠️ Lessons Learned

* VPC Flow Logs capture **metadata (not packet payloads)**
* Logs are generated at the **ENI (network interface) level**
* S3 bucket policy is required for log delivery
* Athena requires proper schema and S3 path configuration
* Public IP exposure leads to immediate global scanning

---

## 🚀 Outcome

Successfully implemented an end-to-end AWS traffic analysis pipeline and gained hands-on experience with:

* Network observability
* Security analysis
* Log querying with SQL
* Real-world cloud traffic patterns

---

## 🧹 Cleanup

All resources were deleted after completion to avoid ongoing costs:

* EC2 instance terminated
* VPC Flow Logs removed
* S3 bucket deleted

---


