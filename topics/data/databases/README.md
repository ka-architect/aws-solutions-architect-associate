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

## RDS
- Managed service for PostgreSQL, MySQL, Oracle, MSSQL, DB2, MariaDB, etc.
- Provisioned hardware on EC2 instances with EBS volumes
- Supports read replicas, and multi AZ
- RDS Proxy for connection pooling, RDS Custom for Oracle and MSSQL (access to underlying hardware)
- Used For: SQL, OLTP, relational data
- cannot encrypt a DB after creation, must encrypt a new DB or restore encrypted snapshot
- Auto-failover by flipping the canonical name record (CNAME) from primary to standby, then standby promoted to primary

## Aurora
- Compatible API for PostgreSQL and MySQL, separates storage and compute
- Cluster: endpoints for writers and readers
- Backups on S3 either MySQL backup or PerconaXtra Backup, Restore is PITR
- Aurora Global DB, Aurora Serverless, Aurora DB Cloning (quick creation of test DB from production DB)

## ElastiCache
- Managed Redis or Memcached, in memory store for reading data
- Supports clustering (redis)/sharding (memcached) for read replicas
- Requires app code changes for first implementation

## DynamoDB
- Managed serverless NoSQL DB
- Provisioned or On-Demand (good for sudden, steep spikes of traffic) Capacity Modes
- Tables have TTL to expire data, can replace ElastiCache for this feature
- Read caching with DAX, Multi-AZ with Global Tables, Event Processing with DynamoDB Streams
- Backups via PITR or on-demand, restore creates new tables

## S3
- Serverless, infinite scaling, 5TB max file size, allows versioning
- key-value store for big objects
- Many tiers managed via lifecycle policy
- Improve performace with batch operations, multi-part upload, S3 Transfer Accelerator
- Client and Server Side encryption

## DocumentDB
- AWS managed version of MongoDB that stores, queries, and indexes JSON data
- Supports millions of requests per second
- Similar deployment as Aurora, auto-scaling for storage

## Neptune
- Fully managed GraphDB, used for graph data sets with relationships
- Can store and query billions of relations
- **Neptune Streams:** realtime ordered sequence of every change in the DB, accessible via REST

## Keyspaces
- AWS managed Cassandra NoSQL database, allows Cassandra Query Language (CQL)
- Serverless and scalable, auto scales tables up and down based on traffic
- 1000s of requests per second
- Similar capacity modes as DynamoDB

## Amazon Time Stream
- AWS Managed, serverless, scalable TimeSeries DB
- Can scale storage and analysis of trillions of events per day
- Great for IOT, near real-time analytics
- Much faster and cheaper than RDBMS