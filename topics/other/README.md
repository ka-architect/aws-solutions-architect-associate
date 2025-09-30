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
- Reduces management complexity without restricting choice or control
- Simply upload the application, and Beanstalk handles capacity, load balancing, scaling, and health monitoring

# Cloud Formation
- Deploy and manage infra at scale, using YAML/JSON to define resource information
- **Infrastructure Composer:** visualtion of CloudFormation teamplace, resources, and relations
- Create IAM roles for service to create, update, delete resources (using ``iam:PassRole``)
- Attributes of CloudFormation:
    - ``CreationPolicy``: prevent status from reaching create complete until ``cfn-signal`` received or timeout, ensures resources properly running before stack creation
    - ``DependsOn``: send a success signal after the applications are installed and configured using the ``cfn-init`` helper script
    - ``UpdatePolicy``: send a success signal after the applications are installed and configured using ``cfn-signal``, used for updating resources and for stack update rollback
- CF templates include several major sections, but ``Resources`` section is the only required section

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
- Define and submit multiple simulation jobs to be executed concurrently

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
- Does not provide higher bandwidth, higher packet per second (PPS) performance, and lower inter-instance latencies, unlike ENA or EFA

# AWS Workspaces
- Allows you to create the virtual desktops in your VPC

# AWS Artifact
- Central resource for compliance-related information, reports, and agreements
- Provides on-demand access to AWS’ security and compliance reports and select online agreements
- All AWS Accounts have access to AWS Artifact via root users and IAM users with admin permissions

# Amazon Simple Workflow Service (SWF)
- Web service that makes it easy to coordinate work across distributed application components
- Use with SQS for decoupling on-prem/cloud applications

# AWS Security Token Service (STS)
- Service that you can use to create and provide trusted users with temporary security credentials that can control access to your AWS resources

# Billing and Cost Management Console
- Generate a cost allocation report based on usage and costs grouped by your active tags
- **Cost Allocation Tags:** Tags that are used to organize your resources and cost allocation tags to track your AWS costs on a detailed level

# AWS Wavelength
- service that allows developers to create mobile applications with ultra-low latencies
- Wavelength Zones can be used to extend an Amazon VPC in order to run ultra-low latency applications that use the same AWS services, APIs, tools, and functionalities
- Combines the high bandwidth and ultralow latency of 5G networks with AWS, without leaving the mobile providers’ network

# AWS Local Zones
 - AWS infrastructure deployment that places services closer to certain locations with single-digit millisecond latency
 - Different from wavelength since wavelength is only mobile workloads

# AWS Service Health Dashboard
- Shows public events that may affect several customers in particular regions (not how specific events impacted individual AWS accounts)

# AWS Personal Health Dashboard
- Ongoing visibility into your resource performance and the availability of your AWS services and accounts (updates/outages)
- Use EventBridge rules to detect and take action specified in a rule (Send emails for upcoming patches to EC2)

# AppSync
- managed service that makes it easy to build scalable GraphQL and Pub/Sub API that connect applications to data
- **GraphQL:** language to enable client apps to fetch, change and subscribe to data from servers
- **GraphQL Query:** client specifies how data is to be structured when returned, query only for the data it needs in required format
- Interact with data sources like Amazon DynamoDB, AWS Lambda, and HTTP APIs
- AWS AppSync API creates two endpoints: GraphQL endpoint and real-time endpoint
- Write resolvers in JS and run via ``APPSYNC_JS`` runtime
- AppSync pipeline resolvers help aggregate data from multiple DB tables
- Enable easy retrieval of data from multiple sources with just a single call, streamlining presenting data

# AWS Trusted Advisor
- Draws upon best practices learned from serving hundreds of thousands of AWS customers
- Inspects your AWS environment and then makes recommendations when opportunities exist to save money, improve system availability and performance, or help close security gaps
- **Quota Monitor CloudFormation template:** can export quota limits to CloudWatch/EventBridge then use SNS/SQS/Lambda to notify
- Many different plans: Business, Developer, Enterprise On-Ramp, or Enterprise Support
- Trusted Advisor APIs (AWS Support APIs) only available for Business, Enterprise On-Ramp, or Enterprise Support plans

# AWS X-Ray
- used to debug and analyze your microservices applications with request tracing, does not record API calls