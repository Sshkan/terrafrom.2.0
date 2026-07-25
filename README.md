# terrafrom.2.0
Brief decription about terraform
Terraform Complete Guide (Step by Step)
Step 1: What is Terraform?

Terraform is an Infrastructure as Code (IaC) tool developed by HashiCorp.

Instead of manually creating servers, networks, databases, and storage from the AWS/Azure/GCP console, Terraform lets you define infrastructure in code.

Without Terraform
Login to AWS
Create EC2
Create VPC
Create Security Group
Create S3 Bucket
Configure Networking

Everything is manual.

With Terraform

Write code once.

resource "aws_instance" "web" {
  ami           = "ami-123456"
  instance_type = "t2.micro"
}

Run

terraform apply

Terraform creates everything automatically.

Step 2: Install Terraform
Windows

Download Terraform

Add Terraform to PATH

Verify

terraform version

Output

Terraform v1.x.x
Step 3: Configure Cloud Provider

For AWS install AWS CLI

aws configure

Enter

AWS Access Key
AWS Secret Key
Region
Output format

Terraform uses these credentials.

Step 4: Terraform Workflow

This is the most important interview topic.

Terraform follows this sequence.

Write Code
      ↓
terraform init
      ↓
terraform validate
      ↓
terraform fmt
      ↓
terraform plan
      ↓
terraform apply
      ↓
terraform destroy
Step 5: Terraform Files

Terraform uses .tf files.

Example

main.tf
variables.tf
outputs.tf
provider.tf
terraform.tfvars
versions.tf
backend.tf
Step 6: Provider

Provider tells Terraform which cloud to use.

Example

provider "aws" {
  region = "ap-south-1"
}
Step 7: Resource

Resource creates infrastructure.

Example

resource "aws_instance" "web" {

  ami = "ami-12345"

  instance_type = "t2.micro"

}

Here

aws_instance = Resource Type

web = Resource Name
Step 8: Variables

Variables avoid hardcoding.

Instead of

instance_type = "t2.micro"

Use

variable "instance_type" {}

Then

instance_type = var.instance_type

In

terraform.tfvars
instance_type = "t2.micro"
Step 9: Output

Outputs display values after deployment.

output "public_ip" {

value = aws_instance.web.public_ip

}

Output

34.xx.xx.xx
Step 10: Terraform Commands
terraform init

Downloads providers.

terraform init
terraform validate

Checks syntax.

terraform validate
terraform fmt

Formats code.

terraform fmt
terraform plan

Shows changes.

terraform plan

Example

+ create EC2

+ create S3

Nothing is created.

terraform apply

Creates infrastructure.

terraform apply
terraform destroy

Deletes infrastructure.

terraform destroy
Step 11: Terraform State

Very important interview topic.

Terraform stores infrastructure information in

terraform.tfstate

Example

EC2

IP

ID

ARN

Tags

Terraform compares

Desired State

↓

Current State

↓

Makes Changes
Step 12: Local State vs Remote State
Local
terraform.tfstate

Stored in your laptop.

Problem

Two engineers can overwrite each other's changes.

Remote State

Store state in

AWS S3
Azure Storage
GCS

Usually with state locking.

Step 13: Backend

Backend stores remote state.

Example

terraform {

backend "s3" {

bucket = "terraform-state"

key = "prod/terraform.tfstate"

region = "ap-south-1"

}

}
Step 14: State Locking

If two DevOps engineers run

terraform apply

simultaneously

Problems occur.

State locking prevents concurrent modifications.

AWS

S3

+

DynamoDB (traditional approach)

Newer AWS S3 locking capabilities are also supported in recent Terraform/OpenTofu versions, depending on your backend configuration.

Step 15: Data Source

Read existing resources.

Example

data "aws_ami" "ubuntu" {

most_recent = true

}

Does not create anything.

Step 16: Locals

Store repeated values.

locals {

environment = "production"

}

Use

local.environment
Step 17: Count

Create multiple resources.

resource "aws_instance" "web" {

count = 3

}

Creates

EC2-1

EC2-2

EC2-3
Step 18: for_each

Better than count.

for_each = toset([
"dev",
"qa",
"prod"
])

Creates three resources with meaningful keys.

Step 19: Dynamic Block

Generate repeated nested blocks.

Example

Security Group rules.

dynamic "ingress" {

for_each = var.rules

content {

from_port = ingress.value

}

}
Step 20: Modules

Modules avoid duplicate code.

Example

Root Module

↓

EC2 Module

↓

VPC Module

↓

S3 Module

Call module

module "ec2" {

source = "./modules/ec2"

}
Step 21: Provisioners

Run commands after creating a resource.

provisioner "remote-exec" {

inline = [

"sudo yum update"

]

}

Provisioners exist but are generally discouraged because they are less predictable. Configuration management tools such as Ansible or cloud-init/user data are often preferred.

Step 22: Dependencies

Terraform automatically builds a dependency graph.

Example

VPC

↓

Subnet

↓

EC2

Explicit dependency

depends_on = [

aws_vpc.main

]
Step 23: Workspace

Manage environments.

terraform workspace new dev

terraform workspace new qa

terraform workspace new prod
Step 24: Functions

Examples

length()

join()

lookup()

file()

upper()

lower()

replace()

jsonencode()

yamldecode()
Step 25: Conditional Expression
instance_type = var.env == "prod"

? "t3.large"

: "t2.micro"
Step 26: Lifecycle Rules

Prevent accidental deletion.

lifecycle {

prevent_destroy = true

}

Other lifecycle settings include:

create_before_destroy
ignore_changes
replace_triggered_by
Step 27: Import Existing Resources

Import manually created infrastructure.

terraform import aws_instance.web i-01234567
Step 28: Terraform Registry

Reuse community modules.

Examples

VPC Module

EKS Module

ALB Module

IAM Module
Step 29: Best Practices
Store state remotely.
Enable state locking.
Use modules.
Use variables instead of hardcoding values.
Separate environments (dev, qa, prod).
Keep secrets out of code (use environment variables or a secrets manager).
Use version control (Git).
Review terraform plan before terraform apply.
Step 30: Real DevOps Project Workflow
Developer pushes code
        │
        ▼
GitHub
        │
        ▼
Jenkins Pipeline
        │
        ▼
terraform init
        │
        ▼
terraform validate
        │
        ▼
terraform fmt
        │
        ▼
terraform plan
        │
        ▼
Approval
        │
        ▼
terraform apply
        │
        ▼
AWS Infrastructure Created
        │
        ▼
Ansible / cloud-init Configures Servers
        │
        ▼
Application Deployed
        │
        ▼
Monitoring (Prometheus + Grafana)
