# Multi-Account AWS EC2 Disk Utilization Monitoring Solution

## 

This project helps you check how much disk space is used on EC2 servers (virtual computers) in many AWS accounts — all from one place!

We use a tool called **Ansible** to collect this information securely and automatically.

---

## Why is this important?

- Companies often have many AWS accounts.
- Checking each server’s disk usage one by one is slow and difficult.
- This solution collects data from all accounts and makes a simple report.
- This helps keep track of resources and plan better.

---

## What tools do we use?

1. **AWS EC2**: These are virtual computers running in the cloud.
2. **AWS Accounts**: Different AWS accounts for different parts of the company.
3. **IAM Roles**: Permissions that let one account safely access resources in another.
4. **AWS Systems Manager (SSM)**: Lets us run commands on EC2 servers remotely and securely.
5. **Ansible**: A tool to automate tasks, like running commands on many servers.
6. **S3**: A storage service where we save the final report.

---

## How does it work? (Step by step)

### 1. Setup IAM Roles

- In the **main AWS account**, we create an **Ansible-Execution Role**.
- This role can "pretend" (assume) other roles in **member AWS accounts** securely.
- In each **member AWS account**, we create an **Ansible-Cross Account Role**.
- This member role allows the main role to access EC2 and SSM in that account.
- Each EC2 server has a role that allows SSM to run commands on it.

### 2. Use AWS STS for Temporary Access

- The main account's Ansible role uses **AWS STS** to get temporary permissions to access member accounts.
- This keeps the system secure — no permanent keys needed.

### 3. Use Ansible to Run Commands

- Ansible runs commands on EC2 servers in each account via SSM.
- It collects disk usage and volume details from each server.

### 4. Create Report and Save to S3

- Ansible uses a template to create a CSV report with all the data.
- The report is uploaded to a secure **S3 bucket** in the main account.
- Now you can open the report to see disk usage across all your servers.

---

## How to use this project?

### Step 1: Prepare IAM Roles

- Create the **Ansible-Execution Role** in the main account.
- Create the **Ansible-Cross Account Role** in each member account.
- Attach necessary permissions to EC2 instances to allow SSM commands.

### Step 2: Update Ansible Playbook Variables

- Add the AWS account IDs and their role ARNs.
- Specify your S3 bucket where the report will be saved.

### Step 3: Run the Ansible Playbook

- The playbook will automatically connect to all your AWS accounts.
- It will collect disk info from all EC2 instances.
- A final CSV report will be created and uploaded to S3.

---

## What does the architecture look like?

- There is a **main AWS account** where Ansible runs.
- It securely connects to **multiple AWS accounts** using IAM roles and AWS STS.
- Commands are sent to EC2 instances via **AWS Systems Manager (SSM)**.
- Data collected is saved as a report in an **S3 bucket**.

(Refer to the diagram file for a visual guide.)

---

## Security Notes

- No permanent passwords or keys are used anywhere.
- Everything is done using **roles** and **temporary permissions**.
- This is much safer and follows AWS best practices.
- All actions are logged for auditing and compliance.

---

---

## What is inside this repository?

This project folder contains everything you need to run the monitoring solution. Here is a simple explanation of the important folders and files:

- **code/playbooks**  
  This folder contains the main **Ansible playbook** files. The main playbook that runs all the tasks for collecting disk usage data is here.  
  You will run this playbook to start the monitoring process.

- **TAD**  
  This folder contains the **Technical Architecture Document (TAD)**, including the architecture diagram in PowerPoint format.  
  It visually explains how the solution works and how different AWS components interact.

- **iam**  
  This folder contains all the **IAM role policies and JSON files** needed to create the required permissions for this project.  
  It includes the permissions for the cross-account roles and EC2 instance roles.

- **README.md**  
  This is the main documentation file you are reading now. It explains how to use the project, step by step.

---

## Summary

This project helps you:

- Monitor disk usage on EC2 servers across many AWS accounts.
- Automate data collection using Ansible and SSM.
- Keep everything secure with IAM roles and temporary access.
- Get a clear report saved centrally for easy review.

---
