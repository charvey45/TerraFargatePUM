# Step 1: Set Up Your Development Environment

In this step, we will set up the development environment by installing the required tools and configuring your AWS account credentials. These tools will help you manage your infrastructure as code, interact with AWS resources, and work with your Kubernetes cluster.

## 1. Install Required Tools

### 1.1. Install Terraform

Terraform is an infrastructure as code (IaC) tool that allows you to define, provision, and manage cloud resources using a declarative configuration language. We will use Terraform to create and manage the necessary AWS resources for the project.

Download and install Terraform from the official website:

[Terraform Download Page](https://www.terraform.io/downloads.html)

Follow the installation instructions for your specific operating system.

### 1.2. Install AWS CLI

The AWS Command Line Interface (CLI) is a unified tool to manage your AWS services. With the AWS CLI, you can control multiple AWS services from the command line and automate them through scripts. We will use the AWS CLI to configure your AWS account credentials and interact with various AWS services throughout the project.

Download and install the AWS CLI from the official website:

[AWS CLI Download Page](https://aws.amazon.com/cli/)

Follow the installation instructions for your specific operating system.

### 1.3. Install kubectl

Kubectl is the Kubernetes command-line tool that allows you to run commands against Kubernetes clusters. You can use kubectl to deploy applications, inspect and manage cluster resources, and view logs. We will use kubectl to interact with our EKS cluster and deploy our PUM application.

Download and install kubectl from the official Kubernetes website:

[Kubectl Download Page](https://kubernetes.io/docs/tasks/tools/install-kubectl/)

Follow the installation instructions for your specific operating system.

### 1.4. Install eksctl

Eksctl is a simple CLI tool for creating and managing Kubernetes clusters on AWS EKS. Developed by Weaveworks and officially supported by AWS, eksctl automates much of the EKS cluster setup process. We will use eksctl to create our EKS cluster and configure it to work with AWS Fargate.

Download and install eksctl from the official GitHub repository:

[Eksctl Download Page](https://eksctl.io/introduction/#installation)

Follow the installation instructions for your specific operating system.

## 2. Configure AWS CLI with Your AWS Account Credentials

Run the following command to configure your AWS CLI:

```sh
aws configure
```
Enter your AWS Access Key ID, Secret Access Key, default region, and default output format when prompted.

The AWS CLI is crucial for interacting with various AWS services throughout the project. Configuring the AWS CLI with your account credentials ensures that you have the necessary permissions to create, manage, and delete resources as needed.

## 3. Clone the GitHub Repository

Clone your GitHub repository to your local machine with the following command:

```sh
git clone https://github.com/your-username/TerraFargatePUM.git
```

Replace `your-username` with your GitHub username and `TerraFargatePUM` with the name of your repository.

This step helps you set up your local working directory with the TerraFargatePUM project files. Once you have the repository cloned, you can start working on the Terraform project and other project-related files.
