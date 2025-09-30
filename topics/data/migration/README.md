# AWS Snowball
- Highly secure, portable devices to collect and process large sets of data at the edge
- Can be used to migrate up to PB of data in and out of AWS to another location
- Come in different flavors: Compute or Storage Optimized
- Snowball: offline migration, AWS ships you a physical device which you load the data on to device and ship back to AWS, can take up to a week
- Snowball Edge: network migration using edge location connection to reduce network bandwidth issues
- Used for: issues with network capacity for a data sync, since it uses physical data transfer or edge locations
- Cannot import to S3 glacier directly, first to S3 then use lifecycle policies

# AWS Storage Gateways (integration)
- Used by many hybrid-cloud companies to synchronize data with on-prem, other clouds, and AWS
- Supports Block, File, and Object storage
- Use cases: DR, backup and restore, tiered storage, on-prem cache, low latency file access between locations
- Can be hosted in AWS or on-prem for less latency

## S3 File Gateway
- Supports all S3 classes but Glacier (use lifecycle)
- NFS/SMB protocol to transfer -> public S3 file gateway -> https to S3
- Supports windows and linux
- Provides cache for frequent data
- Needs permission to write to S3

## Volume Gateway
- Block storage, iSCSI protocol, backed by S3 any class, to back up volumes of on-prem storage
- iSCSI protocol to transfer -> volume gateway -> store as EBS snapshots on S3
- 2 Types: Cached (low latency access to recent data) or Stored Volumes (backup on-prem to S3)

## Tape Gateway
- Physical tape storage backup support via iSCSI interface
- Can be backed up to S3 and S3 Glacier
- Doesn’t maintain a local cache

# AWS Transfer Family
- Fully managed service for FTP transfer to EFS or S3
- Pay per endpoint per hour and pay for data transfered in GB
- Security via crednetial management with LDAP, AD, Okta, AWS Cognito, etc, also needs IAM role to write/read to storage
- Authenticate users directly to Transfer Family

# AWS Data Sync (migration)
- Agent that runs on prem/other cloud to replicate data to AWS over network
- No agent needed if AWS to AWS transfer
- Can sync to all storage classes of S3 including Glacier, EFS, and FSx (but not EBS)
- Requires high network capacity otherwise use snowball
- Its not continous so it needs to be scheduled (hourly, daily, monthly)
- Does not offer local caching for low-latency access to recently accessed files

# AWS Data Migration Service (DMS)
- Quick, secure DB migration to AWS (self-healing, resilient)
- Source DB remains available during migration
- Supports homogenous or heterogenous DB migratsions
- **Change Data Capture:** continuous data replication even with new changes
- Hardware needed for replication tasks
- Can deploy in multi-AZ: provisions and deploys a standby replica in another AZ with synchronous replication

# AWS Schema Conversion Tool (SCT)
- converts DB schema from one engine to another, not needed for same engine migration
- OLTP: SQL/ORacle to MySQL/PostgreSQL/Aurora
- OLAP: Teradata/Oracle to Redshift
- Example: Source DB on prem -> Server SCT on Prem -> EC2 in AWS public subnet with DMS -> Target DB in AWS

# RDS & Aurora Migrations
- RDS MySQL to Aurora MySQL
    - Snapshot and Restore: has downtime, cheaper
    - Continuous: using DMS, create Aurora Read Replicas, promote as cluster when replication lag is zero
- External MySQL to Aurora MySQL
    - Percona XtraBackup export to S3, create Aurora MySQL from backup, downtime but cheaper
    - Use mysqldump utility to migrate directly into Auror (slower than percona)
- RDS PostgreSQL to Aurora PostgreSQL
    - Only difference is options for external to Aurora
    - Create backup and import to SS3, use aws_S3 Aurora extension to import
- Always use DMS if both are running since no downtime

# VM Import/Export
- Migrates existing apps to EC2
- Run amazon linux 2 AMI on-prem, download AMI as VM (.iso format)

# AWS Application Discovery Service
- Helps plan on-prem migration to AWS and track via AWS Migration Hub
- Gathers information on server utilization, dependency mappings
- Can be AWS Agentless Discovery Connector or AWS App Discovery Agent
    - Agentless Discovery Connector:  VM inventory, config, performance, specs
    - Agent-Based Discovery: granular information about system, processes, performance, network

# AWS Migration Service (MGN)
- incremental replication of live on-prem servers to AWS, replicates volumes directly to AWS
- Allows for lift-and-shift which makes the process so much easier
- AWS replication installed on source
- Replicates enviornment to staging until cutover date where it will get created in production
- Minimal downtime, reduced costs

# AWS Backup
- Fully managed service to manage service backups centrally with no manual scripting or processes
- Supports volume backups, DB backups, cross-region, cross-account, PITR, on-demand, or scheduled
- **Backup Plans:** policies for backup, frequency, windows, storage, retention etc
- Stores in dedicated S3 bucket
- **AWS Backup Vault Lock:** enforces WORM state for backups in vault, prevents accidental delete of backups

# Large Datasets to AWS
- To calculate time: convert data size to gigabits/megabits and divide by bandwidth/sec
- Over Internet/S2S VPN: immediate
- Over Direct Connect is 1GBPS, long set up time for initial setup
- Snowball: time to order snowball, upload to snowball, send to AWS, load to AWS, 1 week process
    - combine with DMS for newer data
- Ongoing Replication/Transfers: S2S VPN, Direct Connect with DMS, Data Sync, Storage Gateway

# VMWare Cloud on AWS
- Migrate vSphere environment from another location to AwS
- Allows vSphere, vSAN, VMWare NSX on AWS