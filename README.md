# Terraform AWS EC2 Infrastructure Provisioning (Sydney — `ap-southeast-2`)

This repository demonstrates Infrastructure as Code (IaC) using **HashiCorp Terraform** to automate the provisioning of an **Amazon EC2** instance in the **Asia Pacific (Sydney) — `ap-southeast-2`** region.

---

## Architecture & Configuration Overview

| Parameter | Configuration Value |
| :--- | :--- |
| **IaC Tool** | Terraform (`hashicorp/aws` provider `~> 5.0`) |
| **AWS Region** | Asia Pacific (Sydney) — `ap-southeast-2` |
| **Resource Type** | `aws_instance` (`TF-server`) |
| **AMI ID** | `ami-0ba218121447b72f3` (Sydney Region AMI) |
| **Instance Type** | `t3.micro` (AWS Free Tier Eligible) |
| **Public IP** | Enabled (`associate_public_ip_address = true`) |
| **Key Pair** | `TF-server-key` |
| **Instance Tag** | `Name = "My_test_instance"` |

---

## Project Structure

```text
terraform-aws-ec2-sydney/
├── main.tf          # Terraform provider and EC2 instance configuration
└── README.md        # Project documentation and deployment guide
```

---

## Terraform Configuration (`main.tf`)

```hcl
terraform {
  required_providers {
    aws = {
      source  = "hashicorp/aws"
      version = "~> 5.0"
    }
  }
}

# Configure the AWS Provider
provider "aws" {
  region = "ap-southeast-2"
}

resource "aws_instance" "TF-server" {
  ami                         = "ami-0ba218121447b72f3"
  instance_type               = "t3.micro"
  associate_public_ip_address = true
  key_name                    = "TF-server-key"

  tags = {
    Name = "My_test_instance"
  }
}
```

---

## Prerequisites

* **Terraform CLI** installed (`v1.5+` recommended).
* **AWS CLI** installed and configured with valid credentials (`aws configure`) or an attached EC2 IAM Role with AmazonEC2FullAccess permissions.
* An existing EC2 Key Pair named **`TF-server-key`** in the **Asia Pacific (Sydney) `ap-southeast-2`** region.

---

## Step-by-Step Deployment Guide

### 1. Initialize the Terraform Working Directory
Downloads the official `hashicorp/aws` provider plugin and generates `.terraform.lock.hcl`:
```bash
terraform init
```

### 2. Validate & Preview the Execution Plan
Checks syntax and previews the exact AWS resources Terraform will create:
```bash
terraform plan
```

### 3. Provision the EC2 Instance
Applies the configuration and launches **`My_test_instance`** in Sydney (`ap-southeast-2`):
```bash
terraform apply
```
*(Type `yes` when prompted to confirm creation.)*

### 4. Verify in AWS Console
1. Open the **AWS Management Console** and switch the region to **Asia Pacific (Sydney) `ap-southeast-2`**.
2. Navigate to **EC2 → Instances** and confirm **`My_test_instance`** is in the **`Running`** state with a Public IPv4 address assigned.

### 5. Destroy Infrastructure (Cleanup)
To avoid ongoing AWS charges after testing, tear down all managed resources in one command:
```bash
terraform destroy
```
*(Type `yes` when prompted to terminate the instance.)*

---

## Key Troubleshooting Lessons Resolved

* **Region-Specific AMI IDs (`InvalidAMIID.NotFound`):** AWS AMI IDs are unique per region. Always verify that the `ami` ID matches the exact region defined in the `provider "aws"` block (`ap-southeast-2`).
* **AWS Key Pair Naming (`InvalidKeyPair.NotFound`):** Reference only the AWS Key Pair name (`"TF-server-key"`) in `key_name`, omitting the local `.pem` file extension.
* **Free Tier Instance Eligibility (`InvalidParameterCombination`):** Accounts restricted to Free Tier instances must use eligible instance sizes such as `t3.micro` or `t2.micro` rather than paid sizes like `t3.medium`.
* **Declarative Resource References:** Ensure any referenced `aws_subnet` or `aws_security_group` resource is explicitly declared in `.tf` files before referencing its `.id` attribute.
