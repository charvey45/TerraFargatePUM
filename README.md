
# TerraFargatePUM

TerraFargatePUM is a comprehensive solution to deploy and manage PUM images on AWS Fargate using EKS and Terraform. This project simplifies the process of setting up the necessary infrastructure and container orchestration, allowing you to focus on building and deploying your PUM applications.

## Project Overview
The project covers the complete deployment process, from setting up the development environment, defining AWS resources, and configuring container registries to creating and managing the EKS cluster, Fargate service, and Application Load Balancer.

## Tools, Technologies, and Skills

### AWS Services
- Amazon Virtual Private Cloud (VPC)
- Amazon Elastic Container Registry (ECR)
- Amazon Elastic Kubernetes Service (EKS)
- AWS Fargate
- Application Load Balancer (ALB)

### Infrastructure as Code (IaC)

- Terraform
- Container Technologies
- Docker
- Windows Containers

### Development Environment

- AWS CLI
- kubectl
- eksctl

### Networking

- VPC, subnets, route tables, and internet gateways
- Security groups and network access control lists (NACLs)

## High-Level Steps

    Set up your development environment by installing the required tools and configuring your AWS account credentials.

    Create a Terraform project with the necessary configuration files.
    Define AWS resources using Terraform, including VPC, subnets, security groups, ECR, EKS cluster, Kubernetes deployment and service, and ALB.

    Build and push the Windows container containing the PUM application to Amazon ECR.

    Apply the Terraform configuration to create the resources in AWS.

    Monitor and manage the EKS cluster and Fargate service using the EKS Console, Kubernetes Dashboard, or kubectl commands.

Upon completion of the project, the PUM application will be running in a Windows container on AWS Fargate within an EKS cluster, and the infrastructure will be managed using Terraform for seamless updates, scaling, and maintenance.