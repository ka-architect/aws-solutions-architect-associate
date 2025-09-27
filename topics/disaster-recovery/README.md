# DR Strategies

## DR Overview
- Disasters: events that have negative impact on continuity or finances
- DR strategies help prepare for and recover from disasters
- **Recovery Point Objective:** RPO, how often you make backups, what point in time to recover to
    - Data loss willing to accept, measured as time between last backup and disaster
- **Recovery Time Objective:** RTO, when can we recover from a disaster
    - Downtime willing to accept, measured as time between disaster and recovery

## Backup and Restore (highest RPO and RTO)
- cheapest option, low complexity
- Storage Gateway (highest RPO), Snowball (1 week RPO), Snapshots (1-24 hours RPO) AMI snapshots (high RTO)

## Pilot Light (lower RTO and RPO)
- better than Backup and Restore
- Small version of app is running in another location
- Faster than backup and restore since critical core is already up and running
- More expensive than backup and restore because of the infra
- Example: RDS replication, stopped EC2 instance, R53 failover

## Warm Stand By (lower RPO and RTO)
- Full system is up and running at a minimum size, scaled out during disaster
- More expensive than pilot light
- Example: RDS replication to secondary, EC2 ASG at minimum pointing to primary DB, R53 failover to ELB, EC2 failover to RDS secondary

## Mutli-Site/Active-Active/Hot Site (very low RTO and RPO)
- most expensive set up
- full production scale running on multiple sites
- Example: Route 53 failover to one location during disaster, almost instant
- Instead of RDS switch to Aurora Global tables

## DR Tips
- Backup: EBS snapshots, EFS multi-AZ, RDS automated backup/snapshots/multi-AZ replication, regular pushes to S3 with lifecycle management, replication, snowball/storage gateway
- High Availability: Route 53 DNS migration, RDS/ElastiCache multi-AZ, EFS, S3, S2S VPN recovery for DX
- Replication: RDS replication cross-region, Aurora Global DB, Dynamo Global Tables, storage gateway on prem
- Automation: elastic beanstalk to deploy, CloudWatch Instance Recovery/Reboot if alarms fail, AWS Lambda
- Chaos Testing: randomly invoke disasters in your environment, random terminations

## AWS Elastic Disaster Recovery (AWS DRS)
- provides continuous block-level replication, recovery orchestration, and automated server conversion capabilities
- crash-consistent recovery point objective (RPO) of seconds, and a recovery time objective (RTO) typically ranging between 5–20 minutes
- Performs failover to AWS for immediate recovery but then failback to the original source infrastructure is necessary
- To complete the failback, the AWS Elastic Disaster Recovery Failback Client is installed on the target server, and specific credentials are generated
- Recovery instances are launched in AWS from a snapshot. 
- Cross-Region or cross-AZ failover and failback can be executed from the AWS DRS Console
- Once failback is finished, the recovery instance can be terminated, deleted, or disconnected
- To establish a secure data replication process, configure AWS Elastic Disaster Recovery on your source servers
- Replicates data to a dedicated subnet within your AWS account located in the AWS Region of your choice by utilizing a staging area design
- Optimizes cost-efficiency with cost-effective storage and low compute resources for continuous replication