# Elastic Compute Cloud (EC2)

## Basics
- EC2 is a infrastructure as a service
- You can rent EC2, store data on EC2 (EBS), distribute load (ELB), and auto-scale (ASG)

## EC2 Sizing and Config
- EC2 can be customized based on OS, CPU, RAM, storage space, storage type, network, firewalls
 
## EC2 User Data
- Bootstrapping done via EC2 user date
- Done at EC2 first time startup
- Automates boot tasks (updates, software, download, start services, etc.)
- If you have too many tasks boot can take a long time
- Tasks run as root user for instance

## EC2 Instance Types
- EC2 types are focused on CPU, Memory, Compute, Storage Optimizations
- Naming convention exmaple: m5.2xlarge
    - m = instance class
    - 5 = generation
    - 2xlarge = size within instance class
- **General Purpose EC2:** diverse workloads such as web servers, repositories
    - balanced between compute, memory, and network
    - example: t2.micro
- **Compute-Optimized EC2:** compute inensive workloads that require high-performance processors
    - such as: batch, media transcoding, high-performance web servers, ML, HPC, modeling, gaming
    - example: c5.2xlarge
- **Memory-Optimized EC2:** fast performance for workloads that process large data in memory
    - such as: DB's, caches, business intelligence, processing unstructured data
    - example: m5.2xlarge
- **Storage-Optimized EC2:** storage intensive tasks that require high, sequential read/write access to large data sets
    - such as: OLTP, RDBMS, NoSQL DB, cache, data warehouse, data lake, distributed FS
    - example: i5.2xlarge

## Security Groups
- Fundamental network security in AWS
- Controls how traffic is allowed in/out of EC2 instances, similar to a firewall as it "resides" outside of the EC2
- Default action is to deny, so security groups allow
- Can reference by IP or security groups
- Exam Tip: timeouts when accessing EC2 directly, usually is an issue with security group
- can be implemented regionally/VPC-wide

## EC2 Instance Roles
- IAM role can be created for EC2 instance with permissions
- Role then needs to be added to EC2 instance

## Purchasing Options
- **On-Demand Instances:** short workload, predictable pricing, pay by second
    - pay for what you use, linux/windows per second after first minute are billed, else per hour
    - highest cost upfront, no upfront payment, no long term commitment
    - best for short-term and uninterrupted workloads where you cant predict how the app behaves
- **Reserved Instaces (1 or 3 years):** reserved for long workloads
    - steady-state usage such as DB's
    - can be scoped regionally or in zones, reserve all in a single AZ
    - can reserve based on attributes such as type, region, tenancy, OS
    - can only reserve for 1 or 3 years, more discount for 3 years, payment can be all/partial upfront, discount compared to on-demand
    - ability to resell in AWS marketplace if not needed
- **Convertable Reserved Instances:** long workloads with flexible instances
    - can be converted during reservation to change type, family, OS, scope, tenancy
    - less discount compared to standard reserved since dynamic
- **Savings Plans:** long workloads with commitment
    - same discount as Reserved Instances
    - commit to certain type of usage (hourly rate over x years), excess usage billed as on-demand
    - locked into family and region, but flexible across size, OS, tenancy
- **Spot Instances:** resilient, short workloads
    - higher discount that reserved instances, most cost-efficient
    - only usable when max price set is less than the spot price, can terminate at any time
    - since they can terminate at any time there is risk of data loss, not suitable for critical jobs or DBs
    - best for batch, data analysis, image processing, distributed workloads, flexible time workloads
- **Dedicated Hosts:** book an entire physical server, control its placement
    - physical EC2 server, fully dedicated to you
    - most expensive option
    - best for licensed products, or strict regulatory/compliance needs
    - gives you visibility into the lower-level hardware
- **Dedicated Instances:** no shared hardware with other AWS customers
    - run on hardware dedicated to you, may share hardware with instances in the same account
    - no control over instance placement unless stopped
- **Capacity Reservations:** reserve capacity within an AZ
    - charged as on-demand whether you run the instances or not
    - reserve on-deman instance capacity in AZ for any duration
    - you have access to EC2 capacity when needed
    - no time commitment
    - combine with regional reserved instances and savings plan to benefit from discount
    - best for short-term uninteruppted workloads that need to be in specific AZ
- Regional Purchasing Limits are set by region not by AZ, can request increase by completing form:
    - Reserved Instances: 20 per region
    - Spot Instances: Spot limit per region
    - On-Demand Instances: vCPU-based limits per region

## Which Option to Choose?
- Think of this analogy like booking hotel rooms
- On-demand: you book a room, whenever, pay listed price
- Reserved: plan to stay a long itme, plan ahead, good discount
- Savings Plan: pay for a certain suite per hour for certain period, commit to specific spending
- Spot Instances: bid on a room, stay until there is a higher bidder
- Dedicated Host: book the entire building
- Capacity Reservations: book a room for a period, full price, whether you stay or not

## Spot Instances: Deep Dive
- Define the max price for which you want to keep paying for the instance
- 2 minute window if the spot price exceeds threshold to stop (restart when price drops) or terminate (lose data)
- **Spot Block:** if you don't want the instance to be reclaimed by AWS, block for specified time frame (1-6 hours)
- **Spot Request:** definition of the instances to be claimed, can be one-time or persistent
    - **One-Time Spot Request:** request goes away as soon as instance is claimed and launched
    - **Persistent Spot Request:** when price threshold exceeded, request stays in place for next claim
        - You need to delete the spot request then the instances to stop new requests from being fulfilled
- **Spot Fleet:** define capacity and price to claim a set of spot instances (or on-demand instances) 
    - ultimate cost-savings option
    - tries its best to match the target capacity within the price constraints
    - can have different attributes such as OS and AZ since picking from different pools (you can define the launch pools)
    - you can define a strategy to allocate spot instances
    - allow you to automatically request spots with best price
- **Spot Instance Allocation Strategies:**
    1. Lowest Price: pool with the lowest price (cost, short workloads)
    2. Diversified: distributed across all pools (availiability, long workloads)
    3. Capacity-Optimized: pool with optimal capacity for number of instances
    4. Price-Capacity Optimized: recommended option, pools with highest capacity available then select pool with lowest price (most workloads)

## Launch Templates
- Choose between commonly used requests or manually configure launch pattern with launch parameters
- Launch Parameters: AMI, Key-Pair, Rols, Max Price (Spot), Duration, Load Balancing, Capacity, System Specs, Network
- Define how an EC2 is created and cannot be modified once created (need to create a new one when changing any parameters)

## Public vs Private vs Elastic IP
- **Public IP:** accessible to public over internet, comes with EC2 (changes on restart)
- **Private IP:** accessible to machines in the same network (intranet), comes with EC2 (static), communicates with public via Internet Gateway
- **Elastic IP:** public IPv4 you own as long as you don't delete it

## Elastic IP
- restarting EC2 instances changes the public IP, which can be a problem when serving traffic/requests over internet
- pay per IP assigned, monthly, billed until deleted even if not assigned
- Attachable to one instance at a time, EC2 or network interface
- In case of underlying resource failure, you can map to another instance
- Use cases for Elastic IP are limited/complex since you need to register to DNS, better to use ALB instead of a public IP

## EC2 Placement Groups
- **Placement Group:** gives you control of your EC2 placement within AWS
- **Placement Group Strategies:**
    - **Cluster Placement Group:** cluster EC2's within same AZ
        - low latency, high performance, low availability
        - enable enhanced networking for 10 GBPS bandwidth
        - best for big data jobs, low-latency and high-throughput workloads
    - **Spread Placement Group:** spread EC2's within multiple AZ's
        - max 7 per AZ, critical applications
        - low failure risk, high availability
        - best for apps needing isolated failures
    - **Partition Placement Group:** spreads EC2's across different partitions/racks within AZ/multi-AZ
        - can have 100s of EC2 per group
        - each partition is a rack in AWS, by having more racks EC2s are distributed, safe from rack failures within AZ
        - good for big data apps like hadoop, DB, kafka
- Can get capacity errors when adding instances in same placement group due to shared hardware restrainsts, restart all instances to get all on same hardware

## EC2 Networking
- Enhanced networking, no additional cost, uses single root I/O virtualization (SR-IOV) to provide high-performance networking capabilities on supported instance types
- SR-IOV is a method of device virtualization that provides higher I/O performance and lower CPU utilization when compared to traditional virtualized network interfaces
- Provides higher bandwidth, higher packet per second (PPS) performance, and consistently lower inter-instance latencies
- **Elastic Network Adapter (ENA):** provides enhanced networking capabilities for EC2, up to 100 Gbps, provide traditional IP networking features required for VPC
- **Elastic Fabric Adapter (EFA):** is simply an ENA, with additional OS-bypass functionality
- **OS-bypass:** access model that allows HPC and ML apps to communicate directly with network interface to provide low-latency, reliable transport
- EFA not supported on Windows instances, ENA supported for Windows

## Elastic Network Interface (ENI)
- **Elastic Network Interface (ENI):** logical component within a VPC that represents a virtual network card
- Gives resources access to the network, can be assigned to other resources for failover
- Created within specific AZ
- Each ENI can have the following:
    - Private IPv4, one or more secondary IPv4
    - One elastic IPv4 per private IPv4
    - One public IPv4
    - One or more security groups
    - MAC address

## EC2 Hibernate
- **Hibernate EC2:** ability to preserve state in EC2 when not in use, for a duration
- Different from stop/terminate
- RAM is preserved and written to EBS storage (root EBS must be encrypted)
- Faster startup
- Use cases: services that take long to initialize, long-running processing, RAM state needs to be saved
- Not possible to enable or disable hibernation for an instance after it has been launched

## EC2 Stop Notes
- Only EBS-backed instances can be stopped and restarted, an instance store-backed instance can only be rebooted or terminated
- An EC2 instance has an underlying physical host computer, when stopped AWS moves instance to new host
- Elastic IP address is detached from EC2-Classic, Elastic IP stays if EC2-VPC instance

## Amazon Machine Image (AMI)
- **Amazon Machine Image (AMI):** pre-packaged customization of the EC2 instance, can be saved and used by multiple instances to reduce boot time
- Powers the EC2 instance
- Allows you to add your own software, configuration, OS, monitoring, etc.
- You can choose between public AMIs (common AWS-provided), custom AMIs, or marketplace AMI's (user-created for sale)
- Allows EC2 user data to be saved, when creating from AMI, EC2 user data is appended to the already existing user data within AMI
