# IAM Role Flow for Multi-Account EC2 Disk Utilization Monitoring

## Overview

This document explains the complete IAM role setup and trust relationships used in the multi-account EC2 disk utilization monitoring solution. It guides you step-by-step through how roles are created, how they trust each other, and how the cross-account access and monitoring work end-to-end.

---

## Roles Involved

| Role Name                      | Location (AWS Account)      | Purpose / Use Case                                                                    |
|------------------------        |---------------------------  |-----------------------------------------------------                                  |
| **AnsibleExecutionRole**       | Central Monitoring Account  | Attached to Ansible server EC2 instance. Allows assuming roles in member accounts.    |
| **AnsibleCrossAccountRole**    | Each Member AWS Account     | Trusted by central account role. Grants permissions to query EC2 and run SSM commands.|
| **EC2MonitoringRole**          | Each EC2 instance           | Attached to EC2 instances. Allows SSM agent & CloudWatch agent to operate correctly.  |

---

## Step-by-Step IAM Role Flow

### 1. Ansible Server Setup

- The Ansible server EC2 instance is launched in the **Central Monitoring Account**.
- This EC2 instance has the **AnsibleExecutionRole** attached as its instance profile.
- The trust policy for this role allows the EC2 service to assume it, enabling the server to get credentials automatically.

### 2. Permissions on AnsibleExecutionRole

- This role has the policy that allows it to call **`sts:AssumeRole`** on the **AnsibleCrossAccountRole** in each member AWS account.
- It also has permissions to upload files to the S3 bucket where reports are stored.

### 3. Cross-Account Role Setup

- In **each member AWS account**, an IAM role named **AnsibleCrossAccountRole** is created.
- This role’s trust policy explicitly **allows the Central Account’s AnsibleExecutionRole to assume it**.
- It contains permissions to describe EC2 instances and volumes and to send SSM commands.

### 4. Instance Profile Role on EC2 Instances

- Each EC2 instance in the member accounts is assigned the **EC2MonitoringRole**.
- This role’s trust policy allows EC2 service to assume it.
- It has managed policies:
  - `AmazonSSMManagedInstanceCore` — lets the SSM agent communicate with Systems Manager.
  - `CloudWatchAgentServerPolicy` — lets the CloudWatch agent send logs and metrics.

### 5. Execution Flow During Monitoring

- When the Ansible playbook runs on the Ansible server:
  1. It uses its **AnsibleExecutionRole** credentials to call **STS AssumeRole** API for each member account’s **AnsibleCrossAccountRole**, obtaining temporary credentials scoped to that account.
  2. Using these temporary credentials, it calls AWS EC2 APIs to discover running EC2 instances and their attached volumes.
  3. It uses AWS Systems Manager (SSM) APIs to send shell commands (like `df`) remotely to EC2 instances (using the permissions granted by the instance’s **EC2MonitoringRole**).
  4. Each EC2 instance’s SSM agent executes the command and returns disk usage data.
  5. Ansible collects all the data, processes it, generates a CSV report, and uploads it to the S3 bucket using permissions of the **AnsibleExecutionRole**.

---

## Why This Design?

- **Security:** No long-term AWS keys are stored anywhere; all access is managed by IAM roles and temporary credentials.
- **Scalability:** Adding new AWS accounts only requires creating a new `AnsibleCrossAccountRole` trusted by the central role.
- **Simplicity:** The Ansible server only needs permissions to assume roles and upload to S3, simplifying management.
- **Reliability:** Using SSM agent for remote commands avoids SSH complexities and firewall issues.

---
