# Autonomous Infrastructure Health & Self-Healing System

**AWS Cloud Computing Project**  
**Author:** Mahmoona Khan
**Institution:** Lahore Garrison University  
**Course:** Cloud Computing
**Instructor:** Amir Sohail

---

## Project Overview

This project implements an autonomous self-healing infrastructure system on Amazon Web Services (AWS). The system continuously monitors EC2 instances, detects failures through CloudWatch alarms, and automatically executes recovery actions via Lambda functions without human intervention.

**Architecture Flow:**
EC2 Instance (Monitored Resource)
|
v
CloudWatch (Monitor & Detect)
|
v
SNS (Alert & Notify)
|
v
EventBridge (Route Events)
|
v
Lambda (Self-Heal Logic)
|
v
Recovery (Auto-Remediation)
plain

---

## AWS Services Used

| Service | Purpose | Role |
|---------|---------|------|
| Amazon CloudWatch | Monitoring & Observability | Collects metrics, triggers alarms, creates dashboards |
| Amazon EC2 | Virtual Servers | Target infrastructure being monitored and healed |
| Amazon SNS | Notification Service | Sends alerts via email/SMS when failures detected |
| AWS Lambda | Serverless Compute | Executes self-healing logic (reboot/restart instances) |
| Amazon EventBridge | Event Routing | Routes CloudWatch alarms to Lambda functions |
| AWS IAM | Access Management | Secure permissions for all services |

---

## Architecture Layers

| Layer | Component | Function |
|-------|-----------|----------|
| Layer 1 | CloudWatch | Telemetry collection, metric monitoring, alarm generation |
| Layer 2 | EventBridge | Event routing, pattern matching, target invocation |
| Layer 3 | Lambda | Healing logic execution, EC2 API calls, state validation |
| Layer 4 | SNS | Real-time notifications to operations team |

---

## Self-Healing Workflow

1. **Detect** - CloudWatch Alarm triggers for EC2 instance (high CPU or failed status check)
2. **Notify** - Alarm publishes message to SNS Topic
3. **Orchestrate** - EventBridge Rule receives alarm and triggers Lambda function
4. **Reason** - Lambda analyzes alarm type and decides healing action
5. **Act** - Lambda executes recovery action via EC2 API
6. **Validate** - Lambda polls instance state to confirm recovery success
7. **Log** - All actions logged to CloudWatch Logs for audit trails

---

## Project Implementation

### 1. EC2 Instance Deployment
- Launched Amazon Linux 2023 AMI (t2.micro)
- Configured security group for SSH (22) and HTTP (80)
- Generated key pair for secure SSH access

### 2. SNS Topic & Subscription
- Created `self-healing-alerts` Standard Topic
- Configured email subscription endpoint
- Learned about institutional email filtering challenges with AWS notifications

### 3. CloudWatch Monitoring & Alarms
- Created `HighCPU-Alert` for CPU utilization > 80% over 5 minutes
- Configured alarm actions to publish to SNS topic
- Built composite alarms for multiple failure scenarios

### 4. Lambda Self-Healing Function
- Runtime: Python 3.12
- IAM Role: `lambda-self-healing-role` (least-privilege)
- Logic: Reboot running instances, start stopped instances, log terminated instances
- Environment variables for SNS_TOPIC_ARN

### 5. EventBridge Rule Configuration
- Rule Name: `ec2-health-check-trigger`
- Event Pattern: EC2 Instance State-change Notification
- Target: `self-healing-function` Lambda

### 6. Testing & Validation
- Lambda test event simulation
- CloudWatch Logs analysis
- CPU stress testing using `stress` tool
- End-to-end alarm trigger validation

### 7. CloudWatch Dashboard
- Dashboard Name: `Infrastructure-Health-Dashboard`
- Widgets: CPU utilization graph, network metrics, status checks, alarm states

---

## Code

### Lambda Self-Healing Function
See [src/lambda_function.py](src/lambda_function.py)

### Stress Test Commands
```bash
# Install stress tool
sudo yum install -y stress

# Run CPU stress test (4 workers, 5 minutes)
stress --cpu 4 --timeout 300

# Monitor CPU usage
top
```
### Challenges & Solutions

| Challenge                                               | Solution                                                         |
| ------------------------------------------------------- | ---------------------------------------------------------------- |
| SNS email confirmation blocked by institutional filters | Documented workaround: use SMS or personal email providers       |
| Overly broad Lambda IAM permissions                     | Implemented scoped policies following least-privilege principles |
| EventBridge pattern matching complexity                 | Careful JSON path configuration for relevant EC2 events          |

### Technologies
- Amazon Web Services (AWS)
- Python 3.12
- Boto3 SDK
- Linux (Amazon Linux 2023)

### Author
Mahmoona Khan
BS Computer Science, Lahore Garrison University
