# Storage Options

# EC2 Instance Store
- **EC2 Instance Store:** emphemeral storage attached physically to the EC2 instance
- Since its ephemeral its temporary storage that is lost when the EC2 instance is stopped/hardware failure, backups are your responsibility
- Exam tip: high-performance disk with better IOPS than other options, high performance IOPS usually points to EC2 instance store
- Suitable for buffer, cache, scratch data, temporary content

# Elastic Block Store (EBS)
- **Elastic Block Storage:** network drive you can attach to EC2 while they run
- Allows EC2 to persist data even after termination, unlike EC2 instance store
- Limited to only one EC2 mount at a time, think of it as a network USB stick, unless multi-attach (max 16)
- multiple EBS volumes attached to an EC2, volumes are bound to AZ
- by default, root EBS is deleted when EC2 terminated, can be modified
- created volumes by default are not deleted on termination
- since these are network drives and not physical drives, there is some latency for reads/writes
- data can be moved to another volume via snapshots
- provisioned capacity (GB/IOPS) which can be modified, billed for this provision

## EBS Snapshots
- **EBS Snapshots:** back up of an EBS volume at a point in time
- best practice is to detach EBS when creating a snapshot so new data is not created during the process
- snapshots can be copied across regions, can be restored to a new volume at volume create UI
- **EBS Snapshot Archive:** feature to move snapshot to an archive tier that is cheaper for storage, however there is a delay when restoring since we have to request it first
- **EBS Recycle Bin:** set up rules to retain deleted snapshots to recover from accidental deletion
- **Fast Snapshot Restore:** force full initialize of snapshot to have no latency on first restore, expensive, used for big data that needs to be restored quickly

## EBS Volume Types
- There are 6 types of volumes
    1) **GP2:** SSD, general purpose, price and performance balance, wide variety of workloads
    2) **GP3:** SSD, newer generation
    3) **IO1 Block Express:** SSD, high performance, mission critical, low latency, high throughput workloads
    4) **IO2 Block Express:** SSD, newer generation
    5) **ST1:** HDD, low cost, frequently access, throughput intensive workloads
    6) **SC1:** HDD, cheapest, less frequently accessed data
- volumes can be purchased by size, type, throughput (IOPS)
- only gp2/gp3 and i01/i02 block express can be used as boot volumes for EC2

## Use Cases for Volume Types
- General Purpose SSD
    - cost-effective, low latency
    - system boot, virutal desktops, dev/test environments
    - 1GB-16GB space
    - **GP2: max 16K IOPS**, 3IOPS per GB
    - **GP3: max 16K IOPS**
- Provisioned IOPS SSD (PIOPS)
    - critical business apps and apps that need more than 16K IOPS
    - sustained IOPS performance
    - great for DB workloads, workloads sensitive to storage, performance and consistency
    - **IO1 - max 64K IOPS** for nitro EC2 and 32K otherwise
    - **IO2 - max 256K IOPS**
    - supports EBS multi-attach
- Hard Disk Drives (HDD)
    - cannot be a boot volume
    - 125GB to 16TB
    - **Throughput Optimized HDSS (ST1): max 500 IOPS**, big data, data warehouse, log processing
    - **Cold HDD: max 250 IOPS**, infrequently accessed data, low-cost scenarios

## EBS Multi-Attach
- Allows EBS to be attached to multiple EC2 within the same AZ, each EC2 will have read/write access
- Only available for IO1/IO2 (PIOPS) EBS
- Max 16 attached EC2
- Use Case: high availability needed within a clustered application (Teradata)
- Must be using a cluster-aware FS (not XFS, EXT4, etc.)

## EBS Encryption
- Data at rest, in-flight, snapshots will be encrypted
- Encryption has minimal impact on latency
- KMS encyrption keys (AES-256)
- Snapshots of encrypted volumes are encrypted, to bypass make snapshot unencrypted, then encrypt after copying

# Elastic File System (EFS)
- **Elastic File System (EFS):** managed network file system (NFS) for linux systems (POSIX), that can be mounted on EC2 in any AZ
- Highly available, auto-scaling, pay per use, no provisioning or capacity planning, expensive
- NFSv4.1 protocol
- Access controlled via security groups
- Encryption at rest via KMS
- Use cases: content management, web serving, data sharing, word press

## EFS Performance
- Can scale to 1000s of concurrent NFS clients, 10GBPS, can scale to PB of data automatically
- Performance Mode: set at creation time
    - General Purpose (default): latency-sensitive workloads like web server or CMS
    - Max I/O: high latency, high throughput, high parallelism for big data or media workloads
- Throughput Mode: set at creation time
    - Bursting to 1TB: 50MBPS with burst up to 100 MBPS
    - Provisioned - set throughput regardless of storage (1GBPS for 1TB)
    - Elastic - auto-scaling based on workload, up to 3GBPS for reads, up to 1GBPS for writes, unpredictable workloads

## EFS Storage Classes
- Storage Tiers: (lifecycle management, moving files after N days no access)
    - **Standard:** freequently access files
    - **EFS-IA:** infrequently accessed files, cost to retrieve files, low price to store
    - **Archive:** rarly accessed data (few times per year), cheaper than IA
    - You can implement lifecyle policies to move files between tiers
- Availability & Duration:
    - **Standard:** multi-AZ, great for production
    - **One-Zone:** one AZ, great for dev/backup, enabled by default, compatible with EFS-IA to be EFS One Zone-IA

# EC2 Instance Store vs EBS vs EFS
- EC2 Instance Store: attached to EC2, lose EC2 lose data
- EBS:
    - one instance attach, multi-attach with IO1/IO2 volumes
    - locked to one AZ
    - GP2: IOPS increases if disk size increases, GP3/IO1: can increase IOPS independently
    - To migrate data you need to take snapshot and restore to another volume
    - Root EBS (gp2,gp3,io1,io2) deleted on termination by default, created EBS does not delete by default
- EC2:
    - network file storage, linux file system (POSIX)
    - can mount to 100s of EC2 across AZ, highly available
    - most expensive, but there are cost savings with storage tiers

# AWS FSx
- **AWS FSx:** fullly managed service that allows you to integrate 3rd party file systems to AWS
- Support for Windows, Linux, NetApp ONTAP, or Open ZFS

## AWS FSx for Windows
- Fully managed Windows file storage that supports SMB and Windows NTFS, supports Microsoft FS structures and Namespaces
- Security: Windows AD, Security Groups, ACL's, user quotas, VPN, Direct Connect
- Can be multi-AZ, with daily backups to S3 for DR
- Can be mounted on EC2 Instances (Linux or Windows)
- Scales up to 10GBPS, millions of IOPS, stores 100PB of data
- Options: SSD (perofrmance for DBs, analytics, media) or HDD (low cost, for home directories, content)

## AWS FSx for Lustre
- Linux-based paralle distributed file system
- Scales up to 100s GBPS, millions of IOPS, very low latency
- Used for: HPC, large-scale compute, ML, modeling
- Can be deployed as Scratch FS (temporary storage) or Persistent (long-term storage)
- Options: SSD or HDD, with network Access (VPN/Direct Connect)
- Integrates with S3

## AWS FSx for NetApp ONTAP
- AWS managed service for high-performance, scalable file storage based on NetApp ONTAP file system
- Compatible with NFS, SMB, iSCSI protocal and many OS and AWS services
- Used to move workloads from ONTAP or NAS to AWS, new worklaods
- Auto-scaling for storage, snapshots, point-in-time instantaneous cloning

## AWS FSx for OpenZFS
- AWS managed service for OpenZFS file system with low cost
- Compatable with NFS only and many OS and AWS services
- Up to a million IOPS with very low latency
- Snapshots, compression, no data de-duplication, point in time instantaneous cloning