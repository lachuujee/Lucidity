# Multi-Account AWS EC2 Disk Utilization Monitoring Solution

## Overview

This repository contains a comprehensive solution to monitor disk utilization across EC2 instances in multiple AWS accounts using Ansible. It leverages AWS IAM roles for secure cross-account access, AWS Systems Manager (SSM) for remote command execution, and AWS APIs to gather detailed volume and usage data.

---

## Contents

- **Ansible Playbook:** Automates data collection of EC2 instances, attached EBS volumes, and their disk usage across multiple AWS accounts.

- **Jinja2 Template:** Generates a consolidated CSV report with instance and volume utilization details.

- **Architecture Diagram:** Visual representation of the solution components, IAM roles, and data flow.

- **Documentation:** Explanation of the IAM roles, permissions, and step-by-step execution details.

---

## Key Features

- **Centralized Monitoring:** Collects metrics from multiple AWS accounts securely via cross-account IAM roles.

- **Detailed Metrics:** Retrieves number of attached EBS volumes, individual volume sizes, and disk usage.

- **Secure Role-Based Access:** Implements least privilege IAM roles with clearly defined trust relationships.

- **Automated Reporting:** Generates a consolidated CSV report and uploads it to an S3 bucket for further analysis.

- **Extensible Design:** Easily scalable for additional AWS accounts or expanded monitoring.

---

## Solution Architecture

*(Include your detailed, well-labeled architecture diagram here — explain IAM roles, trust policies, SSM communication, and data aggregation flow.)*

---

## Setup & Usage

1. **Configure IAM roles:**

   - Create and attach `AnsibleExecutionRole` in the central account with `sts:AssumeRole` permission on member accounts.

   - Create `AnsibleCrossAccountRole` in each member account trusted by the central role, with permissions for EC2 describe and SSM actions.

   - Ensure EC2 instances have SSM agent installed and an IAM role attached with `AmazonSSMManagedInstanceCore` and `CloudWatchAgentServerPolicy`.

2. **Update playbook variables:**

   - Add AWS account IDs and cross-account role ARNs.

   - Specify the target S3 bucket for report upload.

3. **Run the Ansible playbook** to collect metrics, generate the CSV report, and upload to S3.

---

## Security Considerations

- No AWS access keys or secrets are hardcoded; all access uses IAM roles and temporary credentials.

- Network security is maintained by relying on AWS Systems Manager endpoints and role-based permissions.

- All sensitive operations are audited by AWS CloudTrail.

---

