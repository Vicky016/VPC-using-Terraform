# README: Creating a VPC Using Terraform

This README provides step-by-step instructions for creating a Virtual Private Cloud (VPC) using Terraform. Please follow the steps below to set up your AWS VPC infrastructure. 

## Prerequisites

Before you begin, ensure that you have the following prerequisites in place:

1. [Terraform](https://www.terraform.io/downloads.html) installed on your local machine.
2. AWS credentials configured. You can set up AWS CLI or configure the `~/.aws/credentials` file.

## Getting Started

### Step 1: Clone or Create Your Terraform Project

You can either clone an existing Terraform project or create a new one. In this README, we'll assume you're starting from scratch.

### Step 2: Create the Terraform Configuration Files

#### providers.tf

Create a `providers.tf` file to configure the AWS provider. Ensure you have the AWS provider installed.

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
  region = "ap-south-1" # Specify your desired AWS region
}
```

#### main.tf

Create a `main.tf` file with the Terraform code to create a VPC, public and private subnets, internet gateway, and route tables.

```hcl
# Step 1: Create a VPC
resource "aws_vpc" "myvpc" {
  cidr_block = "10.0.0.0/16"
  tags = {
    Name = "Terraform VPC"
  }
}

# Step 2: Create a Public Subnet
resource "aws_subnet" "PublicSubnet" {
  vpc_id     = aws_vpc.myvpc.id
  cidr_block = "10.0.1.0/24"
  tags = {
    Name = "Public Subnet"
  }
}

# Step 3: Create a Private Subnet
resource "aws_subnet" "PrivateSubnet" {
  vpc_id     = aws_vpc.myvpc.id
  cidr_block = "10.0.2.0/24"
  tags = {
    Name = "Private Subnet"
  }
}

# Step 4: Create Internet Gateway
resource "aws_internet_gateway" "igw" {
  vpc_id = aws_vpc.myvpc.id
}

# Step 5: Create a Route Table for the Public Subnet
resource "aws_route_table" "PublicRT" {
  vpc_id = aws_vpc.myvpc.id

  route {
    cidr_block = "0.0.0.0/0"
    gateway_id = aws_internet_gateway.igw.id
  }
}

# Step 6: Associate the Route Table with the Public Subnet
resource "aws_route_table_association" "PublicRTassociation" {
  subnet_id      = aws_subnet.PrivateSubnet.id
  route_table_id = aws_route_table.PublicRT.id
}
```

### Step 3: Running Terraform Commands

Open your terminal and navigate to the directory containing your Terraform project. Follow these commands to manage your infrastructure:

#### Initialize the Project

Run the following command to initialize your Terraform project. This command downloads the AWS provider and sets up the working directory.

```bash
terraform init
```

#### Validate Your Configuration

Check if your Terraform configuration is syntactically correct and internally consistent.

```bash
terraform validate
```

#### Plan Your Changes

Generate an execution plan to understand what Terraform will do before making any changes to your infrastructure.

```bash
terraform plan
```

#### Apply Changes

Apply the changes defined in your Terraform configuration to create the VPC and related resources.

```bash
terraform apply
```

Terraform will prompt you to confirm the planned changes. Type "yes" to proceed.

#### Destroy Resources (Optional)

If you want to destroy the infrastructure created with Terraform, use the following command:

```bash
terraform destroy
```

Terraform will prompt you to confirm the destruction. Type "yes" to proceed.

## Conclusion

You've successfully created a VPC and related resources using Terraform. This README covers the essential steps to set up your infrastructure, validate, plan, apply changes, and optionally destroy resources. Happy Terraforming!
