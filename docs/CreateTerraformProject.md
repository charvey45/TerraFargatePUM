## 2. Create a Terraform Project

In this step, we will create a Terraform project within the cloned GitHub repository. This project will include the necessary configuration files for defining AWS resources, storing input variables, and outputting the necessary information.

### 2.1. Initialize a New Terraform Project

Navigate to the `terraform` directory within your cloned repository:

```sh
cd TerraFargatePUM/terraform
```

Initialize a new Terraform project by creating an empty `main.tf` file:

```sh
touch main.tf
```

The `main.tf` file is the primary configuration file for your Terraform project. It will contain the definitions of the AWS resources required for your project, such as VPC, subnets, security groups, ECR, EKS cluster, Kubernetes deployment, and ALB.

### 2.2. Create a `variables.tf` File

Create a `variables.tf` file to store your input variables:

```sh
touch variables.tf
```

Edit the `variables.tf` file to define your input variables, such as AWS region, VPC CIDR block, and container image URL.

Example:

```hcl
variable "aws_region" {
  description = "AWS region where the resources will be created"
  default     = "us-west-2"
}

variable "vpc_cidr" {
  description = "CIDR block for the VPC"
  default     = "10.0.0.0/16"
}

variable "container_image" {
  description = "URL of the container image in Amazon ECR"
}
```

The `variables.tf` file helps manage input variables for your Terraform project. These variables allow you to customize the project's configurations without modifying the main.tf file directly.

### 2.3. Create a `main.tf` File

Edit the `main.tf` file to define your AWS resources, such as VPC, subnets, security groups, ECR, EKS cluster, Kubernetes deployment, and ALB. Use the Terraform AWS provider and other relevant modules to configure these resources.

Here's an example of how you might define a VPC resource:

```hcl
resource "aws_vpc" "this" {
  cidr_block = var.vpc_cidr

  tags = {
    Name = "TerraFargatePUM-VPC"
  }
}
```

The `main.tf` file is the core of your Terraform project, where you define the infrastructure components as code. Using the AWS provider, you can create and manage AWS resources in a declarative manner, making it easier to maintain and understand your infrastructure.

### 2.4. Create an `outputs.tf` File

Create an `outputs.tf` file to output the necessary information:

```sh
touch outputs.tf
```

Edit the `outputs.tf` file to define the outputs you want to display after the Terraform project is applied. For example, you might output the ALB's DNS name or the EKS cluster's endpoint:

```hcl
output "alb_dns_name" {
  description = "The DNS name of the ALB"
  value       = aws_lb.this.dns_name
}

output "eks_cluster_endpoint" {
  description = "The endpoint of the EKS cluster"
  value       = aws_eks_cluster.this.endpoint
}
```

The `outputs.tf` file is used to display relevant information about your infrastructure after your Terraform project is applied. This can include resource attributes, such as DNS names, endpoints, or other essential details needed for further configuration or management.

With the Terraform project created, you now have a solid foundation for defining, provisioning, and managing your AWS infrastructure using a declarative approach. This project will help you create and configure the necessary resources for deploying and running your PUM application on AWS Fargate using EKS and an ALB.

