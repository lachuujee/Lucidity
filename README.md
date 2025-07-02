# Multi-Account AWS EC2 Disk Utilization Monitoring Solution

## Overview

This repository contains a comprehensive solution to monitor disk utilization across EC2 instances in multiple AWS accounts using Ansible. It leverages AWS IAM roles for secure cross-account access, AWS Systems Manager (SSM) for remote command execution, and AWS APIs to gather detailed volume and usage data.

---

## Contents

- **Ansible Playbook:** Automates data collection of EC2 instances, attached EBS volumes, and their disk usage across multiple AWS accounts.

- **Jinja2 Template:** Generates a consolidated CSV report with instance and volume utilization details.

- **Architecture Diagram:** Included as a PowerPoint presentation in the TAD section, providing a detailed, well-labeled visual of the solution components, IAM roles, trust policies, and data flow.

- **Documentation:** Explanation of the IAM roles, permissions, and step-by-step execution details.

---

## Key Features

- **Centralized Monitoring:** Collects metrics securely from multiple AWS accounts using cross-account IAM roles.

- **Detailed Metrics:** Retrieves the number of attached EBS volumes, individual volume sizes, and disk usage per instance.

- **Secure Role-Based Access:** Implements least privilege IAM roles with clearly defined trust relationships, avoiding hardcoded credentials.

- **Automated Reporting:** Generates a consolidated CSV report uploaded to a centralized S3 bucket for easy analysis.

- **Extensible Design:** Easily scalable to incorporate additional AWS accounts or extend monitoring scope.

---

## Solution Architecture

The detailed architecture diagram is provided as a PowerPoint file under the TAD (Technical Architecture Document) section of this repository. It visually explains the following:

- IAM roles and their trust relationships enabling secure cross-account access.

- The flow of commands via AWS Systems Manager (SSM) to EC2 instances.

- Data aggregation and report generation steps.

- Network and security boundaries enforced by IAM and AWS service endpoints.

---

## Setup & Usage

1. **Configure IAM Roles:**

   - **Central Account Role (`AnsibleExecutionRole`):**  
     This role is created in the central monitoring account and is granted permission to assume roles in member accounts via `sts:AssumeRole`. It orchestrates data collection by assuming member account roles.

   - **Member Account Role (`AnsibleCrossAccountRole`):**  
     Created in each member AWS account, this role trusts the central account’s `AnsibleExecutionRole` and grants permissions required to describe EC2 instances, access EBS volume information, and execute commands via SSM (`ec2:DescribeInstances`, `ssm:SendCommand`, etc.).

   - **EC2 Instance Role:**  
     Each EC2 instance must have an attached IAM role with `AmazonSSMManagedInstanceCore` and `CloudWatchAgentServerPolicy` to enable Systems Manager communication and monitoring.

2. **Update Playbook Variables:**

   - Define the list of AWS account IDs and their respective member account role ARNs.

   - Specify the S3 bucket name and path for uploading the generated CSV report.

3. **Run the Ansible Playbook:**  
   Executes cross-account role assumption, gathers disk utilization data, generates a consolidated CSV report using the Jinja2 template, and uploads it securely to the specified S3 bucket.

---

## Security Considerations

- The solution strictly avoids hardcoding AWS access keys or secrets; it exclusively uses IAM roles and temporary STS credentials.

- Network security leverages AWS Systems Manager endpoints and enforced IAM policies for least privilege access.

- All operations are logged and auditable via AWS CloudTrail for compliance and troubleshooting.

---
