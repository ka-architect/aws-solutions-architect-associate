# Elastic Container Service (ECS)

## Introduction to Containers
- **Docker:** software development platfor to deploy containerized applications
- Applications are pre-packaged images deployed as containers on any OS or machine
- Great for lift-and-shift
- **Dockerfile:** defines how the docker container will work and its dependencies
- **Docker Images:** configuration stored in dockerfile that is built and shared
- Images are stored in public repositories like dockerhub or AWS Elastic Container Registry (ECR) which is public and private
- Docker can be considered a virtualization technology, resources are shared with the host, can have many containers on a single server
- VM gives you access to the underlying hardware and OS where as docker container is run on top of the docker daemon installed on the VM

## Docker Container Management in AWS
- **Amazon Elastic Container Service (ECS):** container platform provided by AWS
- **Amazon Elastic Kubernetes Services (EKS):** AWS managed kubernetes platform
- **AWS Fargate:** serverless container platform that works with ECS and EKS
- **Amazon Elastic Container Registry (ECR):** AWS managed container image repository

## ECS - EC2 Launch Type
- ECS cluster made up of EC2 instances, can manage the health and status of the EC2s
- must provision and maintain the EC2 infrastructure, provisioned in advance
- Each EC2 must be running the ECS agent and be registered to an ECS cluster
- EC2 Instance Profile: IAM role used by ECS Agent to make calls to ECS, CloudWatch, ECR API's

## ECS - Fargate Launch Type
- ECS cluster made up of Fargate serverless instances
- No need to provision ahead of time, much easier to manage
- Managed via task definitions, tasks are run based on CPU/RAM needed
- Can scale quickly by increasing the task count instead of more EC2 servers
- **ECS Task Role:** allows each task to have a specific role, maintains task based permissions which are defined in task definitions

## ECS Integrations
- ALB: can front traffic with ECS cluster of EC2/Fargate instances
- NLB: only for high-performance/high throughput apps, or to pair with AWS PrivateLink
- EFS: multi-AZ storage for containers, when data needs to be persisted, mounted on ECS tasks (cannot use S3)
- Task Auto Scaling: manually or automatically scale number of tasks
    - Supports target tracking (metric), step scaling (alarm), scheduled scaling
- EC2 Auto Scaling: supports autoscaling for ECS with EC2, pairs with existing ASG and ECS Cluster Capacity Provider
- **ECS Cluster Capacity Provider:** provisions and scales the infra for ECS tasks within EC2 before the ASG is needed for additional EC2 infra

# Elastic Container Registry
- ECR: used to store and manage docker image files on AWS
- Can be both public and private, protected by IAM (EC2 needs to be able to pull image from ECR)
- Supports vulnerability scans, versioning, tags, lifecycle, etc.

# Elastic Kubernetes Service
- EKS: launch and manages kubernets clusters in AWS
- Kubernetes: cloud-agnostic, open source, deployment, management, and scaling of containers
- Manages health checks, failovers, pods/clusters
- Node Types:
    - **Managed Node Groups:** EC2, AWS creates and manages and registers to EKS, on-demand and spot, uses ASG
    - **Self-Managed Node Groups:** EC2, customer-managed and customer-registered to EKS
    - **Fargate:** severless, no maintanence, no infra managed
    - Use prebuild AMIs for EC2
- Containers have a Container Storage Interface which is used to connect to storages, storage type needs to be defined on EKS cluster
- Supports: EBS (EC2 only), EFS, FSx (lustre, ontap)

## EKS Auto Scaling
- The Kubernetes Horizontal Pod Autoscaler automatically scales the number of Pods in a deployment, replication controller, or replica set based on resource’s CPU utilization
- When you set a target CPU utilization percentage, the Horizontal Pod Autoscaler scales your application in or out to try to meet that target
- **Karpenter:** flexible, high-performance Kubernetes cluster autoscaler that launches appropriately sized compute resources to precisely match workload requirements
– **Cluster Autoscaler:** automatically adjusts the number of nodes in your cluster when pods fail or are rescheduled onto other nodes using Auto Scaling groups

# AWS App Runner
- Managed service that deploys apps and APIs at scale
- Start with code or container, specify options for hardware and deploy

# AWS App2Container (A2C)
- CLI tool to modernize .NET or JAVA into containers for lift-and-shift
- Accelerates modernization (no code changes), generates cloudformation templates, registers images to ECR, supports CICD pipelines
- Think of A2C when exam mentions migrating JAVA/.NET to AWS quickly

