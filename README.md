# Overview
This repository contains the **Infrastructure as Code (IaC)** setup for provisioning and managing an **Amazon EKS cluster** and its networking dependencies using **Terraform**.  
It also includes **GitHub Actions workflows** to automate Terraform execution in both **staging** (plan & validate) and **main** (apply) branches.

---

# GitHub Actions Workflow
The pipeline automates Terraform execution:

## Trigger Conditions
- Runs on **push** and **pull requests** targeting `stage` or `main` branch.  
- Limited to changes inside the `/terraform` folder.

## Stages
1. Checkout source code  
2. Setup Terraform  
3. Terraform Init (with S3 remote backend for state management)  
4. Terraform FMT (code formatting check)  
5. Terraform Validate (syntax validation)  
6. Terraform Plan (dry-run infrastructure changes) – *runs only in staging*  
7. Terraform Apply (apply changes to AWS) – *runs only in main*  

## Secrets Required
- `AWS_ACCESS_KEY_ID`  
- `AWS_SECRET_ACCESS_KEY`  
- `AWS_REGION`  
- `BUCKET_TF_STATE` (name of the S3 bucket for remote backend)  

---

# Branching Strategy
- **Stage Branch**  
  Runs Terraform Plan and validation. Used for testing infrastructure changes.  

- **Main Branch**  
  Runs Terraform Apply. Used to deploy real infrastructure changes into AWS.  

---

# Remote State Management
Terraform state is stored in **Amazon S3** to ensure:
- Centralized state management  
- Collaboration across teams  
- Prevention of duplicated infrastructure  

---

# Infrastructure Components
## VPC
- CIDR block: `172.20.0.0/16`  
- Public & Private subnets across 3 AZs  
- NAT Gateway (single for cost optimization)  

## EKS Cluster
- Version: `1.28`  
- Public endpoint enabled  
- Managed Node Groups:  
  - `t3.small` instances  
  - Auto-scaling: min=1, max=3, desired=2  

## Networking
- Elastic IP for NAT  
- Internet Gateway  
- Subnet tags for EKS & ELB discovery  

---

# Outputs
After successful deployment, Terraform provides:
- EKS Cluster Name  
- EKS Cluster Endpoint  
- AWS Region  
- Security Group ID  
