# TerraFargatePUM Project Plan

## 1. Set up your development environment 

1. Install required tools
   1. Install Terraform
   2. Install AWS CLI
   3. Install kubectl
   4. Install eksctl
2. Configure AWS CLI with your AWS account credentials
3. Clone the GitHub repository

[Detailed Steps: Install Required Tools](InstallRequireTools.md)

## 2. Create a Terraform project

1. Initialize a new Terraform project within the cloned repository
2. Create a `variables.tf` file to store your input variables
3. Create a `main.tf` file to define your AWS resources
4. Create an `outputs.tf` file to output the necessary information

[Detailed Steps: Create Terraform Project](CreateTerraformProject.md)

## 3. Define AWS resources using Terraform

1. Create a VPC
   1. Define a new VPC with a CIDR block
   2. Configure necessary subnets
   3. Set up route tables and internet gateways
2. Create a security group
   1. Define inbound and outbound rules for your container
   2. Configure rules for the Oracle DB and application ports
3. Set up the container registry
   1. Create an Amazon ECR repository for your Windows container
4. Build and push the Windows container
   1. Create a Dockerfile for your Windows container with Oracle DB
   2. Build the container image
   3. Push the image to Amazon ECR
5. Create an EKS cluster
   1. Define a Terraform resource for the EKS cluster
   2. Specify the VPC, subnets, and security group
   3. Update your Kubernetes configuration with the EKS cluster
6. Create a Kubernetes namespace (optional)
   1. Define a Kubernetes namespace for your application
7. Create a Kubernetes deployment
   1. Define a deployment resource for the Windows container
   2. Set container resource requirements
   3. Specify environment variables, secrets, or storage options
8. Create a Kubernetes service
   1. Define a Kubernetes service resource
   2. Expose your application within the EKS cluster
9. Set up an Application Load Balancer (ALB)
   1. Create an ALB and associated target group
   2. Configure the ALB ingress to use the Kubernetes service
   3. Update security group rules to allow traffic from the ALB

[Detailed Steps: Define AWS resource in Terraform](DefineAWSwithTerraform.md)

## 4. Apply the Terraform configuration

1. Run `terraform init` to initialize the backend and download providers
2. Run `terraform plan` to review the changes
3. Run `terraform apply` to create the resources in AWS

## 5. Monitor and manage the EKS cluster and Fargate service

1. Monitor cluster, deployment, and service states
   1. Use the EKS Console, Kubernetes Dashboard, or `kubectl`
2. Scale the deployment up or down based on demand
3. Troubleshoot and resolve issues

## 6. Commit and push the Terraform configuration to GitHub

1. Add and commit the Terraform files to your local repository
2. Push the changes to the remote GitHub repository
