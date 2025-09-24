# AWS Snowball
- Highly secure, portable devices to collect and process large sets of data at the edge
- Can be used to migrate up to PB of data in and out of AWS to another location
- Come in different flavors: Compute or Storage Optimized
- Snowball: offline migration, AWS ships you a physical device which you load the data on to device and ship back to AWS, can take up to a week
- Snowball Edge: network migration using edge location connection to reduce network bandwidth issues
- Used for: issues with network capacity for a data sync, since it uses physical data transfer or edge locations
- Cannot import to S3 glacier directly, first to S3 then use lifecycle policies

# AWS Storage Gateways
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
- Block storage, iSCSI protocol, backed by S3, to back up volumes of on-prem storage
- iSCSI protocol to transfer -> volume gateway -> store as EBS snapshots on S3
- 2 Types: Cached (low latency access to recent data) or Stored Volumes (backup on-prem to S3)

## Tape Gateway
- Physical tape storage backup support via iSCSI interface
- Can be backed up to S3 and S3 Glacier

# AWS Transfer Family
- Fully managed service for FTP transfer to EFS or S3
- Pay per endpoint per hour and pay for data transfered in GB
- Security via crednetial management with LDAP, AD, Okta, AWS Cognito, etc, also needs IAM role to write/read to storage
- Authenticate users directly to Transfer Family

# AWS Data Sync
- Agent that runs on prem/other cloud to replicate data to AWS over network
- No agent needed if AWS to AWS transfer
- Can sync to all storage classes of S3, EFS, and FSx (but not EBS)
- Requires high network capacity otherwise use snowball
- Its not continous so it needs to be scheduled (hourly, daily, monthly)