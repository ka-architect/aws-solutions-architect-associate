# Databases in AWS

## Types
- RDBMS: RDS, RDS Aurora - SQL, OLTP, great for JOINS
- NoSQL: DynamoDB, ElastiCache, Neptune, DocumentDB (MongoDB), Keyspaces (Cassandra) - flexible, no joins, NoSQL
- Object Store: S3 (big objects), Glacier (archive/backups)
- Data Warehouse: Redshift (OLAP), Athena, EMR - SQL analytics or business intelligence
- Search: OpenSearch (JSON) - free text, unstructured searches
- Graphs: Amazon Neptune - display relationships between data
- Ledger: Amazon Quantum Ledger DB (QLDB)
- Time-Series: Amazon Time Stream

# RDS
- Managed service for PostgreSQL, MySQL, Oracle, MSSQL, DB2, MariaDB, etc.
- Provisioned hardware on EC2 instances with EBS volumes
- Supports read replicas, and multi AZ
- RDS Proxy for connection pooling, RDS Custom for Oracle and MSSQL (access to underlying hardware)
- Used For: SQL, OLTP, relational data
- cannot encrypt a DB after creation, must encrypt a new DB or restore encrypted snapshot
- Auto-failover by flipping the canonical name record (CNAME) from primary to standby, then standby promoted to primary
- Managed relational database service, managed SQL database
- create managed DB in AWS: PostgreSQL, MySQL, MariaDB, Oracle, MSSQL, IBM DB2, AuroraDB
- No SSH access since its a managed service
- Hosted on EC2 instances, optimized for DB loads
- To force SSL on RDS:
    - Enable ``use rds.force_ssl`` setting
    - Import the Amazon RDS Root CA certificate to your servers and configure your application to use SSL to RDS
- RDS event subscriptions typically notify operational changes not INSERT, DELETE, or UPDATE changes
- **Enhanced Monitoring:** powerful feature that provides detailed metrics in real-time about the operating system (OS)
    - RDS child processes, RDS processes, and OS processes
- RDS Metrics in CloudWatch: CPU Utilization, Database Connections, and Freeable Memory

## RDS - Storage Auto Scaling
- Increases storage dynamically when RDS detects space issue based on maximum storage threshold, zero downtime
- **Maximum Storage Threshold:** DB scaling begins when threshold hit for 5 minutes straight (with 6 hours since last modification)
- Supports all RDS DB's, good for unpredicatble workloads
- Can be enabled at any time

## RDS Read Replicas (Readers)
- Read-scalability within an AZ, cross-AZ, or cross-region
- Asynchronous process for replication, this means **eventually consistent** data
- If issues with primaries, read replicas can be promoted to primary automatically
- Code change is required to have logic for applications to hit read replicas instead of primaries
- Use cases: reporting jobs and databases, performance issues on primary DB in production
- Network costs when crossing AZ, same-AZ replication is free
- When created, you are provided with reader endpoint URL
- **Multi-AZ:** DR strategy for highly available RDS with automatic failover
    - Synchronous replication since writing to primary than replicating to standby
    - Automatic failover to standby via DNS name
    - This is not a scaling process but rather standing up another copy of your database which is costly
    - But can also be used for RO/read replicas
- **Single to Multi-AZ Read Replicas:** zero downtime operation to stand up read replicas in another AZ
    - Modify DB to enable, snapshot is taken and restored to other DB, then synchronous replication enabled

## RDS Custom
- **RDS Custom:** managed versions of Oracle, MSSQL with OS and DB customization
- Setup, operation, scaling all still managed by AWS
- RDS Custom allows access to OS and DB for configuration, patching, features when Automation Mode is disabled
- This is the only difference between RDS and RDS Custom
- Hosted on EC2 instances, optimized for DB loads

# Amazon Aurora RDS/Amazon Aurora Serverless
- Serverless, AWS-propeitary, cloud-optimized DB that works well with PostgreSQL and MySQL drivers
- Costs more than RDS because of its efficiency, HA, instant failover, storage scaling features
- Supports up to 15 read replicas with very low replication lag, which can be cross-region
- Read replicas can be auto-promoted to primary if there is issue, within 30 sec, self-healing
- Optimal set up is primary + 15 replicas
- Storage is very resilient, striped over 100s of volumes
- Example: 6 copies of your DB over 3 AZ: 4 copies for writes, 3 copies for reads
- Read replicas have reader endpoint that can act as a LB and auto-scaling, at the connection-level (not statement-level), can also add new copies dynamically
- **Backtrack:** Restored to point in time with no backups
- You can also create custom endpoints for use cases like reporting, jobs, where you can query only certain DB's
- **Proxy Fleet:** Serverless, auto db initialize, auto scaling when handling unexpected loads without provisioning
- You can invoke an AWS Lambda function from an Amazon Aurora MySQL DB with a native function or a stored procedure
- Compatible API for PostgreSQL and MySQL, separates storage and compute
- Cluster: endpoints for writers and readers
- Backups on S3 either MySQL backup or PerconaXtra Backup, Restore is PITR
- Aurora Global DB, Aurora Serverless, Aurora DB Cloning (quick creation of test DB from production DB)
- Aurora Serverless uses Aurora capacity units (ACUs) to handle the scaling up/down, each ACU is 2GB of memory
    - when figuring out how to scale based on ACU's you will need memory spend on the DB (Example: 16GB = 8 ACUs since each one is 2GB )
- You can invoke an AWS Lambda function from an Amazon Aurora MySQL DB with a native function or a stored procedure

## Aurora Failover
- Amazon Aurora Replica in same or different AZ, Amazon Aurora flips the canonical name record (CNAME not A record) to healthy replica, failover within 30 sec
- Aurora Serverless and the DB/AZ becomes unavailable, Aurora will automatically recreate the DB instance in a different AZ
- Single instance (w/o Replica and w/o Serverless), Aurora will attempt to create a new DB Instance in the same AZ as original instance (best-effort basis - may not succeed)

## Aurora Global Database
- **Aurora Global Database:** cross-region Aurora replicas, easy to implement DR strategy
- low RTO to promote another region, with low replication cross-region
- Recommended: 1 primary region, up to 10 secondary regions, up to 16 replicas per region

## Babelfish for Aurora
- When converting MSSQL to PostgreSQL, allows apps to migrate with little to no code changes, since using same driver as MSSQL
- Accepts TSQL and converts to PL/PGSQL

# RDS Backups and Restore

## Backups
- Can be automated or manual, automated is 1-35 days of retention
- RDS backups can be used to store data and lower costs by terminating instances when not in use
- Automated backups in Aurora are enabled by default

## Restore
- Aurora allows point in time restore to any point from automated backups
- Restoring from backup or snapshot creates a new DB instance
- On-Prem MySQL to cloud store on S3: for RDS you can use regular backup, for Aurora MySQL use Percona XtraBackup utility

## Aurora Database Cloning
- Create a new cluster form an existing one, faster than snapshot and restore by using copy-on-write
- Great for migrating production server to test environment

# RDS Security
- At rest encryption via KMS, In-flight encryption via SSL with certs at client-side
- Primary must be encrypted before replicas
- To encrypt an unencrypted DB, first take unencrypted DB snapshot, then restore as encrypted
- can uses IAM roles to allow access, Secrets Manager for credentials, security groups to restrict access

# RDS Proxy
- **RDS Proxy:** fully managed private DB proxy endpoint, used to pool connections for all apps, reducing stress on DB
- Serverless, autoscaling, HA
- No need for code changes, reduced failover time since apps connect to endpoint and not actual DB
- Use case: lambda functions, regular service to DB connections

# ElastiCache
- Managed Redis or Memcached, in memory store for reading data
- Supports clustering (redis)/sharding (memcached) for read replicas
- Requires app code changes for first implementation

# DynamoDB
- Managed serverless NoSQL DB
- Provisioned or On-Demand (good for sudden, steep spikes of traffic) Capacity Modes
- Tables have TTL to expire data, can replace ElastiCache for this feature
- Read caching with DAX, Multi-AZ with Global Tables, Event Processing with DynamoDB Streams
- Backups via PITR or on-demand, restore creates new tables
- Made up of tables that must contain a private key (partition key + optional sort key)
- Supports rapidly evolving schemas unlike RDBMS
- Max data size is 400KB, data must be of type: Scalar (standard), Document (list/map), or Set (string, number, binary sets)
- Key-Value pair DB
- Data is stored in partitions, backed by SSDs, and automatically replicated across multiple AZs, providing HA and data durability
- Writes are unconditional by default, to prevent multiple users writing same value add CondiditonalWrite feature in the request
- **Eventually Consistent Reads:** data that may not be latest when requested, default
- **Consistent Reads:** when latest information is always required, set to ConsistentRead to true in the request, not supported across regions, impacted by latency
- **Primary Key:** unique ID for each item in the table, no two items can have same key, must be scalar type
- **Partition Key:** simple primary key, composed of one attribute, determines the logical partitions in which data is stored
- **Composite Key:** partition key and sort key, composed of two attributes, sort key is optional
- **Secondary Index:** query data in table with alternate key, in addition to queries against the primary key
    - Two Kinds of Indexes: Global secondary index and local secondary index
    - **Global Secondary Index:** index with partition key and sorty key that is different from those on table
    - **Local Secondary Index:** index with same partition key as table but different sort key
    - You can create one or more secondary indexes on a table, max 20 global, max 5 local

## DynamoDB AutoScaling
- Dynamically adjusts provisioned throughput capacity in response to load
- Enables a table or a global secondary index to increase its provisioned read/write capacity to handle sudden increases in traffic without throttling
- Auto Scaling is not enabled in a DynamoDB table created using AWS CLI

## DynamoDB Partitioning
- Uses partition key value as input to an internal hash function, output from the hash determines the partition where item stored
- All items with the same partition key stored together, sorted by sort key value (no two items can have the same partition key value if no sort key used)
- A partition key must distribute I/O requests evenly, to stop “hot” partitions and throttling
- Provisioned I/O capacity for the table is divided evenly among these physical partitions
- Optimal usage of a table’s provisioned throughput depends on workload and partition-key design
- The more distinct partition key values that your workload accesses, the more those requests will be spread across the partitioned space
- The less distinct partition key values, the less evenly spread it would be across the partitioned space, which effectively slows the performance
- You will use your provisioned throughput more efficiently as the ratio of partition key values accessed to the total number of partition key values increases
- Composite primary key will provide more partition for the table and improve the performance

## Global Tables
- DynamoDB tables that are replicated across regions, that can be replicated both ways
- must have DynamoDB streams enabled
- low latency access in multiple regions, R/W to table in any region (Active/Active)

## Backup
- Point In Time Recovery or On-Demand backups
- Export to S3, Import from S3
- Use athena to query the backups, ETL on top of backups
- Cannot copy DynamoDB on-demand backups to a different account or Region

## DynamoDB Capacity Modes
- **Read Capacity Units (RCU)** and **Write Capacity Units (WCU)**
- Strongly consistent reads/2 eventually consistent reads = 1 RCU up to 4KB in size, more than 4KB = more RCU
- 1 WCU per write of 1KB, more than 1KB = more WCU
- Throttling protects against overuse of capacity units, throughput set per table, can also throttle read requests exceeds for an index
    - Throttle returns HTTP 400 code and ``ProvisionedThroughputExceededException``
- Provisioned mode: plan R/W per second ahead of time, pay for RCU and WCU (default)
- On-Demand mode: no RCU/WCU planning needed, scales to workload, pay per use
    - Great for steep, sudden spikes on DB

## Dynamo DB Accelerator (DAX)
- fully managed in memory cache for DynamoDB used to resolve read congestion
- no need to modify app logic, because its compatible with DB APIs
- can be used with elasticache, DAX is better for individual objects cache, query and scan cache

## Dynamo DB Streams
- Not enabled by default
- Store ordered stream of all table actions in real time with 24 hour retention, limited consumers, can be processed via lambda/KDS
- If using lambda, associate the stream ARN with a Lambda function that you write
- KDS streams has 1 year retention, more consumers and more processing

# S3
- Serverless, infinite scaling, 5TB max file size, allows versioning
- key-value store for big objects
- Many tiers managed via lifecycle policy
- Improve performace with batch operations, multi-part upload, S3 Transfer Accelerator
- Client and Server Side encryption

# DocumentDB
- AWS managed version of MongoDB that stores, queries, and indexes JSON data
- Supports millions of requests per second
- Similar deployment as Aurora, auto-scaling for storage

# Neptune
- Fully managed GraphDB, used for graph data sets with relationships
- Can store and query billions of relations
- **Neptune Streams:** realtime ordered sequence of every change in the DB, accessible via REST

# Keyspaces
- AWS managed Cassandra NoSQL database, allows Cassandra Query Language (CQL)
- Serverless and scalable, auto scales tables up and down based on traffic
- 1000s of requests per second
- Similar capacity modes as DynamoDB

# Amazon Time Stream
- AWS Managed, serverless, scalable TimeSeries DB
- Can scale storage and analysis of trillions of events per day
- Great for IOT, near real-time analytics
- Much faster and cheaper than RDBMS