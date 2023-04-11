## 3. Define AWS Resources Using Terraform

In this step, you will create and configure the necessary AWS resources for your project using Terraform. This includes creating a VPC, configuring subnets, setting up route tables and internet gateways, creating a security group, setting up a container registry, building and pushing your Windows container, creating an EKS cluster, and configuring an Application Load Balancer (ALB).

### 3.1. Create a VPC

#### 3.1.1. Define a New VPC with a CIDR Block

In the `main.tf` file, create a new VPC resource with a CIDR block:

```hcl
resource "aws_vpc" "this" {
  cidr_block = var.vpc_cidr

  tags = {
    Name = "TerraFargatePUM-VPC"
  }
}
```

### 3.2. Configure Necessary Subnets

#### 3.2.1. Create Public and Private Subnets

Create public and private subnets for your VPC in `main.tf`:

```hcl
# Public Subnet
resource "aws_subnet" "public" {
  vpc_id                  = aws_vpc.this.id
  cidr_block              = "10.0.1.0/24"
  availability_zone       = "${var.aws_region}a"
  map_public_ip_on_launch = true

  tags = {
    Name = "TerraFargatePUM-Public-Subnet"
  }
}

# Private Subnet
resource "aws_subnet" "private" {
  vpc_id                  = aws_vpc.this.id
  cidr_block              = "10.0.2.0/24"
  availability_zone       = "${var.aws_region}a"

  tags = {
    Name = "TerraFargatePUM-Private-Subnet"
  }
}
```

#### 3.2.2. Set Up Route Tables and Internet Gateways

Configure the route tables and internet gateways for your VPC in `main.tf`:

``` terraform
# Internet Gateway
resource "aws_internet_gateway" "this" {
  vpc_id = aws_vpc.this.id

  tags = {
    Name = "TerraFargatePUM-Internet-Gateway"
  }
}

# Route Table
resource "aws_route_table" "public" {
  vpc_id = aws_vpc.this.id

  route {
    cidr_block = "0.0.0.0/0"
    gateway_id = aws_internet_gateway.this.id
  }

  tags = {
    Name = "TerraFargatePUM-Public-Route-Table"
  }
}

# Route Table Association
resource "aws_route_table_association" "public" {
  subnet_id      = aws_subnet.public.id
  route_table_id = aws_route_table.public.id
}
```

### 3.3. Create a Security Group

#### 3.3.1. Define Inbound and Outbound Rules for Your Container

Create a security group for your container in `main.tf`, defining the necessary inbound and outbound rules for your Oracle DB and application ports:

```hcl
resource "aws_security_group" "this" {
  name        = "TerraFargatePUM-Security-Group"
  description = "Security group for TerraFargatePUM application"
  vpc_id      = aws_vpc.this.id

  ingress {
    from_port   = 0
    to_port     = 65535
    protocol    = "tcp"
    cidr_blocks = ["10.0.0.0/16"]
  }

  ingress {
    from_port   = 1521
    to_port     = 1521
    protocol    = "tcp"
    cidr_blocks = ["0.0.0.0/0"]
  }

  ingress {
    from_port   = 80
    to_port     = 80
    protocol    = "tcp"
    cidr_blocks = ["0.0.0.0/0"]
  }

  egress {
    from_port   = 0
    to_port     = 65535
    protocol    = "tcp"
    cidr_blocks = ["0.0.0.0/0"]
  }

  tags = {
    Name = "TerraFargatePUM-Security-Group"
  }
}
```

### 3.4. Set Up the Container Registry

#### 3.4.1. Create an Amazon ECR Repository for Your Windows Container

In `main.tf`, create an Amazon ECR repository for your Windows container:

```hcl
resource "aws_ecr_repository" "this" {
  name = "terrafargatepum"
}
```

### 3.5. Build and Push the Windows Container

#### 3.5.1. Create a Dockerfile for Your Windows Container with Oracle DB

Navigate to the `docker` directory within your cloned repository and create a Dockerfile for your Windows container:

```sh
cd ../docker
touch Dockerfile
```

Edit the Dockerfile to configure your Windows container with the Oracle DB and any other necessary components:

```Dockerfile
# Use an appropriate base image for your Windows container
FROM mcr.microsoft.com/windows/servercore:ltsc2019

# Install Oracle DB and other required components
# ...

# Set up any necessary environment variables, configurations, or entrypoints
# ...
```

#### 3.5.2. Build the Container Image

Build the container image using the Docker CLI:

```sh
docker build -t terrafargatepum .
```

#### 3.5.3. Push the Image to Amazon ECR

Log in to your Amazon ECR registry:

```sh
aws ecr get-login-password --region <your-aws-region> | docker login --username AWS --password-stdin <your-aws-account-id>.dkr.ecr.<your-aws-region>.amazonaws.com
```

Tag your Docker image with the ECR repository:

```sh
docker tag terrafargatepum:latest <your-aws-account-id>.dkr.ecr.<your-aws-region>.amazonaws.com/terrafargatepum:latest
```

Push your Docker image to the ECR repository:

```sh
docker push <your-aws-account-id>.dkr.ecr.<your-aws-region>.amazonaws.com/terrafargatepum:latest
```

### 3.6. Create an EKS Cluster

#### 3.6.1. Define a Terraform Resource for the EKS Cluster

In `main.tf`, create a Terraform resource for the EKS cluster:

```hcl
module "eks" {
  source = "terraform-aws-modules/eks/aws"

  cluster_name = "TerraFargatePUM"
  subnets      = [aws_subnet.private.id]

  tags = {
    Terraform = "true"
    Project   = "TerraFargatePUM"
  }

  vpc_id = aws_vpc.this.id

  # Define the Kubernetes worker nodes
  node_groups = {
    default = {
      desired_capacity = 2
      max_capacity     = 5
      min_capacity     = 1

      instance_types = ["t3.small"]
    }
  }
}
```

#### 3.6.2. Specify the VPC, Subnets, and Security Group

Update the `main.tf` file to specify the VPC, subnets, and security group for the EKS cluster:

```hcl
module "eks" {
  # ...

  vpc_id = aws_vpc.this.id
  subnets = [
    aws_subnet.private.id
  ]

  # ...
}
```

#### 3.6.3. Update Your Kubernetes Configuration with the EKS Cluster

Update your local Kubernetes configuration to use the EKS cluster:

```sh
aws eks update-kubeconfig --name TerraFargatePUM
```

### 3.7. Create a Kubernetes Namespace (Optional)

#### 3.7.1. Define a Kubernetes Namespace for Your Application

In `k8s` directory, create a file named `namespace.yaml`:

```yaml
apiVersion: v1
kind: Namespace
metadata:
  name: terrafargatepum
```

Apply the namespace to your Kubernetes cluster:

```sh
kubectl apply -f namespace.yaml
```

### 3.8. Create a Kubernetes Deployment

#### 3.8.1. Define a Deployment Resource for the Windows Container

Create a file named `deployment.yaml` in the `k8s` directory:

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: terrafargatepum
  namespace: terrafargatepum
spec:
  replicas: 1
  selector:
    matchLabels:
      app: terrafargatepum
  template:
    metadata:
      labels:
        app: terrafargatepum
    spec:
      containers:
      - name: terrafargatepum
        image: <your-aws-account-id>.dkr.ecr.<your-aws-region>.amazonaws.com/terrafargatepum:latest
        ports:
        - containerPort: 80
```

#### 3.8.2. Set Container Resource Requirements

Update the `deployment.yaml` file to set container resource requirements:

```yaml
# ...
spec:
  # ...
  template:
    # ...
    spec:
      containers:
      - name: terrafargatepum
        # ...
        resources:
          requests:
            cpu: "100m"
            memory: "256Mi"
          limits:
            cpu: "500m"
            memory: "1Gi"
```
#### 3.8.3. Specify Environment Variables, Secrets, or Storage Options

In the `deployment.yaml` file, you can specify any necessary environment variables, secrets, or storage options for your application:

\```yaml
# ...
spec:
  # ...
  template:
    # ...
    spec:
      containers:
      - name: terrafargatepum
        # ...
        env:
        - name: EXAMPLE_ENV_VARIABLE
          value: "example_value"
        # Add more environment variables, secrets, or storage options as needed
\```

Apply the deployment to your Kubernetes cluster:

\```sh
kubectl apply -f deployment.yaml
\```

### 3.9. Create a Kubernetes Service

#### 3.9.1. Define a Kubernetes Service Resource

Create a file named `service.yaml` in the `k8s` directory:

\```yaml
apiVersion: v1
kind: Service
metadata:
  name: terrafargatepum
  namespace: terrafargatepum
spec:
  selector:
    app: terrafargatepum
  ports:
    - protocol: TCP
      port: 80
      targetPort: 80
  type: ClusterIP
\```

#### 3.9.2. Expose Your Application Within the EKS Cluster

Apply the service to your Kubernetes cluster:

\```sh
kubectl apply -f service.yaml
\```

### 3.10. Set Up an Application Load Balancer (ALB)

#### 3.10.1. Create an ALB and Associated Target Group

In the `main.tf` file, create an ALB and an associated target group:

\```hcl
resource "aws_lb" "this" {
  name               = "TerraFargatePUM-ALB"
  internal           = false
  load_balancer_type = "application"
  security_groups    = [aws_security_group.this.id]
  subnets            = [aws_subnet.public.id]
}

resource "aws_lb_target_group" "this" {
  name     = "TerraFargatePUM-TargetGroup"
  port     = 80
  protocol = "HTTP"
  vpc_id   = aws_vpc.this.id
}
\```

#### 3.10.2. Configure the ALB Ingress to Use the Kubernetes Service

In the `k8s` directory, create a file named `ingress.yaml`:

\```yaml
apiVersion: networking.k8s.io/v1
kind: Ingress
metadata:
  name: terrafargatepum
  namespace: terrafargatepum
spec:
  rules:
  - http:
      paths:
      - path: /
        pathType: Prefix
        backend:
          service:
            name: terrafargatepum
            port:
              number: 80
\```

Apply the ingress to your Kubernetes cluster:

\```sh
kubectl apply -f ingress.yaml
\```

#### 3.10.3. Update Security Group Rules to Allow Traffic from the ALB

Update the security group rules in `main.tf` to allow traffic from the ALB:

\```hcl
resource "aws_security_group" "this" {
  # ...

  ingress {
    # ...
  }

  ingress {
    from_port       = 80
    to_port         = 80
    protocol        = "tcp"
    security_groups = [aws_lb.this.security_group.id]
  }

  egress {
    # ...
  }

  tags = {
    # ...
  }
}
\```

Apply the changes to your Terraform infrastructure:

\```sh
terraform init
terraform apply
\```

Now, your application should be accessible through the Application Load Balancer (ALB). You can retrieve the ALB's DNS name to access your application:

\```sh
aws lb describe-load-balancers --names TerraFargatePUM-ALB --query "LoadBalancers[].DNSName"
\```

Use the DNS name provided in the output to access your application.

With these steps completed, you have successfully defined and deployed an application using a Windows container with Oracle DB on an Amazon EKS cluster using Terraform.
