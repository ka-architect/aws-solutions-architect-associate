# Amazon Relational Database Service (RDS)

## Introduction
- Managed relational database service, managed SQL database
- create managed DB in AWS: PostgreSQL, MySQL, MariaDB, Oracle, MSSQL, IBM DB2, AuroraDB
- No SSH access since its a managed service
- Hosted on EC2 instances, optimized for DB loads

## RDS - Storage Auto Scaling
- Increases storage dynamically when RDS detects space issue based on maximum storage threshold
- **Maximum Storage Threshold:** DB scaling begins when threshold hit for 5 minutes straight (with 6 hours since last modification)
- Supports all RDS DB's, good for unpredicatble workloads

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

# Amazon Aurora RDS
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

## Aurora Global Database
- **Aurora Global Database:** cross-region Aurora replicas, easy to implement DR strategy
- low RTO to promote another region, with low replication cross-region
- Recommended: 1 primary region, up to 10 secondary regions, up to 16 replicas per region

## Babelfish for Aurora
- When converting MSSQL to PostgreSQL, allows apps to migrate with little to no code changes, since using same driver as MSSQL
- Accepts TSQL and converts to PL/PGSQL

# Backups and Restore

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
- **ElastiCache:** fully managed service for Redis or Memcached
- **Cache:** in-memory DB with high performance and low latency to reduce read traffic on databases
    - Caching allows for stateless applications but storing the state
- Many code changes required if implementing caching for the first time
- App will query the cache for data
    - **Cache Hit:** data exists in cache
    - **Cache Miss:** data does not exist in cache, app goes to DB for data, then writes to cache
- Must have an invalidation strategy to make sure its most recent data by implementing a TTL
- Supports reader endpoints and in-flight encryptions

## Caching Patterns
- **Lazy Load:** read data stored, get state
- **Write Through:** add/update cache when written to DB, not stale data
- **Session Store:** store session data with TTL

## Redis
- Multi-AZ with auto-failover, add read replicas for scale, very HA
- Data durability using AOF persistence, with backup and restore features
- Security via IAM policies and Redis Auth
- **Redis Auth:** credentials created at cluster, extra layer of security
- Supports sets and sorted sets
- **Redis Sorted Sets:** sorted sets that can be stored in memory that guarantees uniqueness and ordering in real-time
- Use case: gaming leaderboard

## Memcached
- multi-node for partioning of data via sharding, high performance via multi-threading
- no replication, no HA, non-persistent which creates risk for data loss
- serverless back up and restore
- security via IAM policies and SASL auth
