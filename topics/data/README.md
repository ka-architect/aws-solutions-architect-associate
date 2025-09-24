# Storage Comparison
Quick overview of all of the data storage options

- **S3:** object storage, static content websites
- **S3 Glacier:** object storage archival
- **EBS Volumes:** network storage for one EC2 instance at a time, or many EC2 with multi-attach
    - General Purpose SSD (gp2/gp3): can be root volume on EC2, general purpose workloads, 1GB-16GB storage, up to 16K IOPS
    - Provisioned IOPS Block Express SSD (io1/io2): can be root volume on EC2, IO-intensive workloads, up to 64TB storage, 32K to 256K IOPS
    - Hard Disk Drives HDD (st1/sc1): physical storage, low-latency, 250-500 IOPS
- **EC2 Instance Storage:** physical, ephemeral storage for EC2 with highest IOPS, data can be lost
- **EFS:** network file system for linux, POSIX file system, higher latency, HA, can be mounted to hundreds of EC2 instances
- **FSx for Windows:** network file system for Windows servers
- **FSx for Lustre:** linux file system, lustre = "linux-cluster", high performance computing workloads, temporary/persistent deployment
- **FSx for NetApp ONTAP:** high OS compatibility
- **FSx for Open ZFS:** managed ZFS file system
- **AWS Storage Gateway:** can also be hosted on prem to reduce latency
    - S3 File Gateway: object storage from on-prem to S3, S3 Glacier via lifecycle policies
    - FSx File Gateway: file storage from on-prem to S3
    - Volume Gateway: cached/stored options, backed up top EBS
    - Tape Gateway: tape volumes, iSCSI to S3 and S3 Glacier
- **AWS Transfer Family:** FTP, FTPS, SFTP interface on top of S3 or EFS
- **AWS DataSync:** schedule data synce from on-prem or another cloud to AWS, or AWS to AWS
- **AWS Snowcone/Snowball/Snow Mobile:** move large data to and from cloud/on-prem, on a physical device, can take up to a week, or use edge locations
- **Databases:** for specific workloads that require indexing or querying
