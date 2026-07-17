# Driftctl — Terraform Drift Detection

<p align="center">

![Go](https://img.shields.io/badge/Go-1.24+-00ADD8?style=for-the-badge&logo=go)
![Terraform](https://img.shields.io/badge/Terraform-IaC-7B42BC?style=for-the-badge&logo=terraform)
![AWS](https://img.shields.io/badge/AWS-Cloud-FF9900?style=for-the-badge&logo=amazonaws)
![SQLite](https://img.shields.io/badge/SQLite-Database-003B57?style=for-the-badge&logo=sqlite)
![License](https://img.shields.io/badge/License-Apache_2.0-blue?style=for-the-badge)

</p>

---

## Overview

Terraform Drift Detection is a DevOps tool that continuously compares your **Terraform state** with **live AWS infrastructure** to detect configuration drift without executing:

- `terraform plan`
- `terraform apply`

The tool helps platform engineers identify infrastructure changes performed manually through the AWS Console or other automation tools before they become production issues.

---

# Why Drift Detection?

Infrastructure drift occurs whenever cloud resources no longer match what Terraform expects.

Examples include:

- Someone manually deletes an EC2 instance
- An S3 bucket tag is modified
- Security Groups are edited directly
- Resources are created outside Terraform
- Infrastructure changes bypass CI/CD

Without drift detection, these changes remain hidden until the next deployment.

---

# Features

- Terraform State Parsing
- Local State & S3 Backend Support
- AWS Infrastructure Discovery
- Resource Normalization
- Drift Detection Engine
- Missing Resource Detection
- Extra Resource Detection
- Attribute Change Detection
- Tag Change Detection
- REST API
- CLI Interface
- Scheduled Drift Scans (Cron)
- SQLite Persistence
- JSON Output
- Table Output
- Web Dashboard

---

# Architecture

<p align="center">

<img width="1448" height="1086" alt="image" src="https://github.com/user-attachments/assets/a18295c4-6ede-47ea-bc34-7e21c234b7f4" />


</p>

The workflow consists of two independent pipelines.

```
                Terraform State                         AWS APIs
                      │                                    │
                      ▼                                    ▼
              +---------------+                  +----------------+
              | State Reader  |                  | Cloud Fetcher  |
              +---------------+                  +----------------+
                      │                                    │
                      ▼                                    ▼
              +---------------+                  +----------------+
              |   Extractor   |                  |   Extractor    |
              +---------------+                  +----------------+
                      │                                    │
                      ▼                                    ▼
          +----------------------+      +----------------------+
          | Expected Resource    |      | Actual Resource      |
          | Model                |      | Model                |
          +----------------------+      +----------------------+
                      \                     /
                       \                   /
                        \                 /
                         ▼               ▼
                    +-------------------------+
                    |      Drift Engine       |
                    +-------------------------+
                               │
                               ▼
                    +-------------------------+
                    |     Drift Report        |
                    +-------------------------+
                               │
          ┌────────────────────┼────────────────────┐
          ▼                    ▼                    ▼
      CLI Output          REST API          Web Dashboard
```

The Drift Engine compares both normalized models and generates a detailed drift report.

---

# Supported Drift Types

| Drift Type | Description |
|------------|-------------|
| Missing Resource | Exists in Terraform but missing in AWS |
| Extra Resource | Exists in AWS but not in Terraform |
| Attribute Changed | Resource exists but attributes differ |
| Tag Changed | Resource tags have changed |

---

# Project Structure

```
terraform-drift-detector/
│
├── cmd/
│   └── driftctl/
│
├── configs/
│
├── internal/
│   ├── api/
│   ├── cloud/
│   ├── drift/
│   ├── extractor/
│   ├── models/
│   ├── provider/
│   ├── scheduler/
│   └── storage/
│
├── web/
│
├── testdata/
│
├── Makefile
├── go.mod
└── README.md
```

---

# Technology Stack

- Go
- Terraform
- AWS SDK
- Amazon S3
- SQLite
- REST API
- Cron Scheduler
- JSON
- Linux
- Git

---

# Installation

Clone the repository

```bash
git clone https://github.com/shashwat-shah/terraform-drift-detector.git

cd terraform-drift-detector
```

Install dependencies

```bash
go mod download
```

Build

```bash
make build
```

---

# Usage

## Scan Terraform State

```bash
./bin/driftctl scan \
--state terraform.tfstate \
--provider aws \
--region ca-central-1
```

---

## Scan S3 Backend

```bash
./bin/driftctl scan \
--state s3://terraform-state-bucket/prod/terraform.tfstate \
--provider aws \
--region ca-central-1
```

---

# Sample Output




```
SUMMARY

Total Resources:      1
Missing in Cloud:     0
Extra in Cloud:       0
Attribute Changes:    2
Tag Changes:          1

Total Findings:       3

------------------------------------------------------

attribute_changed
Resource : aws_s3_bucket

Field : versioning

attribute_changed

Field : force_destroy

tag_changed

Field : tags.Name

Expected : <nil>

Actual : shashwat
```

---

# Real Drift Scenarios Tested

## Tag Drift

<img width="1405" height="1119" alt="image" src="https://github.com/user-attachments/assets/2248b928-8a43-4982-847f-5dea21dc65d3" />


Terraform creates an S3 bucket.

AWS Console is used to manually change the bucket tag.

Drift Detector reports:

```
tag_changed

Expected:
<nil>

Actual:
shashwat
```

---

## Missing Resource

<img width="1281" height="801" alt="image" src="https://github.com/user-attachments/assets/b28ceb14-f5b6-400c-baed-9ff78e90dd9d" />



Terraform creates an S3 bucket.

Bucket is manually deleted from AWS Console.

Drift Detector reports:

```
missing_in_cloud

Severity:
Critical
```

---

# REST API

Trigger Scan

```
POST /api/v1/scans
```

List Workspaces

```
GET /api/v1/workspaces
```

Health Check

```
GET /health
```

---

# Configuration

Example

```yaml
database:

driftctl.db

workspaces:

- name: production

provider: aws

state:

backend: s3

bucket: terraform-state

key: production/terraform.tfstate

region: ca-central-1
```


---

# Roadmap

- Azure Support
- Google Cloud Support
- Multi-Account AWS Scanning
- Slack Notifications
- Email Alerts
- Prometheus Metrics
- Grafana Dashboard
- Docker Support
- Kubernetes Deployment
- GitHub Actions CI/CD
- HTML Reports
- PostgreSQL Backend
- Resource Ignore Rules

---

# Learning Outcomes

This project helped strengthen my understanding of:

- Infrastructure as Code
- Terraform State Internals
- AWS SDK
- Cloud Resource Discovery
- Infrastructure Drift Detection
- Resource Normalization
- REST API Development
- SQLite
- DevOps Automation
- Linux
- Cloud Troubleshooting

---

# Future Improvements

- Parallel resource scanning
- Exponential backoff for AWS API retries
- IAM Role support
- Enhanced test coverage
- Release pipeline
- Multi-cloud provider abstraction
- Containerized deployment
- Prometheus metrics
- Authentication & RBAC

---



# Author

**Shashwat Shah**

Cloud Engineer | DevOps Engineer

- AWS
- Terraform
- Kubernetes
- Docker
- Linux
- Jenkins
- Prometheus
- Grafana
- Go

---

If you found this project helpful, consider giving it a ⭐.
