# Ansible Playbooks for EC2 Disk Utilization Monitoring

## Overview

This folder contains the Ansible playbooks responsible for automating the collection of disk utilization data from EC2 instances across multiple AWS accounts.

---

## What does the main playbook do?

- Connects securely to multiple AWS accounts by assuming cross-account IAM roles.
- Uses AWS Systems Manager (SSM) to remotely execute commands on EC2 instances without needing SSH access.
- Collects detailed information about EC2 instances and their attached EBS volumes, including disk usage metrics.
- Aggregates the collected data and generates a consolidated CSV report.
- Uploads the report to a centralized S3 bucket for easy access and analysis.

---

## How does it work?

1. **Role Assumption:**  
   The playbook starts by assuming the **Ansible-Cross Account Role** in each member AWS account using AWS Security Token Service (STS). This provides temporary, secure permissions to access resources in that account.

2. **Command Execution via SSM:**  
   Using the permissions from the assumed role, the playbook sends commands to EC2 instances through AWS Systems Manager (SSM). This avoids the need for direct network access or SSH keys.

3. **Data Collection:**  
   The playbook runs scripts on EC2 instances to gather disk usage data and volume details (e.g., size, number of volumes).

4. **Report Generation:**  
   The collected data is processed and formatted using a Jinja2 template to create a clear, consolidated CSV report.

5. **Upload to S3:**  
   Finally, the report is uploaded to the specified S3 bucket in the central AWS account for centralized storage and easy retrieval.

---

## Why use this playbook?

- Automates a complex, repetitive task across many accounts and servers.
- Uses secure, AWS best practice methods for cross-account access and remote command execution.
- Provides centralized visibility into EC2 disk utilization to aid in capacity planning and cost optimization.

---

