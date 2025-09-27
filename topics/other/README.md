# Elastic Beanstalk
- Developer centric view of deploying on AWS, managed service that handles components
- Provides consistency across apps and environment while deploying
- Components:
    - **Application Component:** collection of Beanstalk components, environments, versions, configs
    - **Application Version:** an iteration of your apps code
    - **Environment Component:** collection of AWS resources running an app version
- Ability to create multiple enviornments (production, dev, test)
- Tiers:
    - **Web Server Tier:** client access to the environment
    - **Worker Tier:** no client, data layer, messaging layer
    - Web and worker together by having web publish
- Deployment Modes:
    - **Single Instance:** dev purposes, limited scale
    - **HA with LB:** production, bigger scale
- Uses CloudFormation to orchestrate the build

# Cloud Formation
- Deploy and manage infra at scale, using YAML/JSON to define resource information
- **Infrastructure Composer:** visualtion of CloudFormation teamplace, resources, and relations
- Create IAM roles for service to create, update, delete resources (using ``iam:PassRole``)

# AWS Simple Email Server (SES)
- Fully managed service to securely send emails globally at scale
- Allows inbound and outbound to receive replies
- Provides metrics about emails (delievered, rejected, sent, open, etc.)

# Amazon Pinpoint
- Scalable 2-way marketing communication service
- SMS, email, push, voice, in-app messaging
- Streaming events to SNS, KDF, CloudWatch logs
- Overlap between SNS and SES: pinpoint can create templates, schedules, targets, full campaigns

# System Manager - SSM Session Manager
- Allows SSH on EC2 and on-prem servers securely
- No need for resources to have SSH access
- EC2 has SSM agent running on it, to allow just enalble IAM permission for ``SSMManagedInstanceCore``
- Commands:
    - Run Command: execute scripts or run commands
    - Patch Manager: automated patching of servers
    - Maintenance Windows: schedule when to perform actions
    - Automation: simplifies common maintenance and deployment of resources (restarts/snapshots)

# AWS Cost Explorer
- Billing service to visualize and manage AWS costs and usage overtime
- High level or granular
- Choose savings plans, forecast usage up to 1 year, alternative to reserved instances

# AWS Cost Anamoly Detection
- Service that uses ML to monitor costs, monitor unusual spend, and get RCA for issues

# Amazon App Flow
- Fully managed service for SaaS integrations, private and secure or AWS PrivateLink
- No need to create custom integrations, can use existing API's to integrate
- Saves data on S3, Redshift, and salesforce
- Data transformation capabilities such as filter and validate

# AWS Outposts
- server racks offering AWS infrastucture, services, APIs and tools to build apps on prem
- used by many hybrid-cloud organizations to "extend cloud on prem"
- customer has responisibilty for physical security of the rack
- provides low-latency access to on-prem, local data processing since data lives close to you
- easy migration plan

# AWS Batch
- Fully managed batch processing at scale, just submit and schedule
- bunches EC2 spot instances dynamically to complete the job
- docker image that runs on ECS
- Batch vs. Lambda: batch has no limits for time, runtime, disk space, and relies on EC2

# AWS Amplify
- Web/Mobile app developement tool
- Select from set of tools and services to build and deploy full stacks
- Connect code or repo to upload directly
- Considered to be the Elastic Beanstalk for web/mobile apps

# AWS Instance Scheduler
- AWS solution deployed through CloudFormation
- Ability to stop/start instances providing cost reduction for unused instances
- Can be used to shut down all instances that are not used during off-business hours
- Schedules managed in DynamoDB

# AWS ParallelCluster
- AWS-supported open-source cluster management tool to deploy and manage High-Performance Computing (HPC) clusters on AWS
- Uses a simple text file to model and provision resources for HPC apps through secure automation

# AWS Workspaces
- Allows you to create the virtual desktops in your VPC