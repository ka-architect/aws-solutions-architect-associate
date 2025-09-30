# Data and Analytics

# Athena
- Serverless query service to analyze data stored in S3, using SQL (built on Presto)
- Pay per TB of data scanned
- Supports CSV, JSON, Parquet (columnar) and other formats, and compression
- Cost Savings
    - Columnar data type for less scans
    - Compression
    - Partition Data Sets in S3 (easy to query the prefixes) s3://bucket/year=1991/day=1
    - Use larger files, many small files = more memory
- **Athena Federated Query:** ability to run SQL queries across DB's (AWS/On-Prem) and store results in S3
- can COPY Apache Parquet and Apache ORC file formats from S3 to Redshift cluster

# Amazon Redshift (Data Warehouse)
- OLAP - Online Analytical Processing (analytics/data warehouse)
- Used for computations on data
- Based on PostgreSQL but not OLTP
- Stores data in columns which is great for performance, usually sub 1sec performance
- Can be provisioned (EC2 instances) or serverless
- Faster querying power compared to Athena
- Cluster Setup
    - Leader Node: for query planning and results aggregation
    - Compute Node: runs query and sends results to leader
- Snapshots and multi-AZ, can copy snapshots to another region, enabled within the cluster
- **Cross-Region Copy:** enable feature for each cluster and configure backup location and retention, all new snapshots are then copied to the specified region.
- Redshift Ingestion: KDF (via S3 copy), Manual S3 copy, or Batch JDBC on EC2
- **Redshift Spectrum:** Analyze data in S3 without loading into Redshift by using cluster leader, more processing power and no time to load into Redshift

# OpenSearch (Kibana)
- Similar to ElasticSearch/Kibana
- Search any field and returns any/partial matches, no need for primary key like DynamoDB
- Provisioned or Serverless Mode
- Can stream from DynamoDB, KDS, Neptune, to search for the action and then you can query the DB

# Amazon EMR (Hadoop)
- Fully managed Hadoop clusters for analyzing and processing big data and store 
- EMR = Elastic Map Reduce
- Bundled with tools like Spark, HBase, Presto, Apache Flink
- Node Types:
    - **Master Node:** cluster management, coordinates tasks, health check, long-running
    - **Core Node:** run tasks and store data - long running
    - **Task Node:** optional run tasks - short lived
    - Best to used reserved or on-demand for master and core nodes
    - Use spot nodes for task
- Cluster Types: long-running or **transient** (tear down after analysis)
- Used to transform and move large amounts of data into and out of other AWS stores such as S3 and DynamoDB

# QuickSight (Tableau)
- Similar to tableau or power BI
- Serverless, ML powered, BI service used to create interactive dashboards
- Fast, autoscaling, embeddable, pay per session pricing
- **SPICE Engine:** in-memory computation for data imported directly to QS
- Security: Users and groups can be created (separate in IAM), uses column-level security to grant access, can publish data to certain users/groups

# AWS Glue (ETL)
- Fully managed and serverless service used for ETL data for analytics
- **Glue Data Catalog:** feature that provides catalog of data sets by creating DB and tables of metadata, can be a source for other analytics service
- **Glue Data Crawler:** data sources are crawled and data written to Glue Data Catalog
- **Glue Job Bookmarks:** prevents reprocessing of old data
- **Glue Data Brew:** clean/normalize data with pre-built transformation
- **Glue Studio:** UI to manage Glue Jobs
- **Glue Streaming ETL:** built on spark structured steaming, run real-time ETL jobs instead of batch
- Can converst CSV to Parquet then send to S3/Athena/Redshift

# AWS Data Lake
- **Data Lake:** central place for all data to be analyzed
- Fully managed service to setup data lakes in days, usually months
- Automate discovery, cleaning, transforming, and ingesting data into lake
- Built on top of AWS Glue, out-of-the-box blueprints for AWS services and on prem databases
- Fine grain access control for access to data via applications (row/column level security)
- Centralized permissions - so many sources with different permissions
- Cross-account sharing is free with AWS Lake Formation
- S3 is the storage layer for Lake Formation
- You can registering existing S3 buckets that contain your data, creates new buckets for the data lake and imports data into them
- Tag-based access control ensures that only authorized users can access sensitive data, by attaching tags to data
- Integrates with AWS Security Hub to monitor the security of your data lake and receive alerts when potential security issues are detected. 
- Enable Lake Formation principals to RW data with access controlled by Lake Formation permissions:
    – S3 locations must be registered with Lake Formation
    – Principals who create Data Catalog tables pointing to underlying data locations must have permissions
    – Principals who RW data must have Lake Formation data access permissions on the Data Catalog tables
    – Principals who RW data must have the ``lakeformation:GetDataAccess`` IAM permission
- When integrated service requests access to Lake Formation, temporary credentials given to access the data

# Apache Flink
- Fully managed service that allows you to run Flink on a managed cluster
- **Apache Flink:** framework for real-time processing of data streams
- Provisioned/parallel compute with auto-scaling
- Backup via checkpoints and snapshots
- Integrates with KDS, MSK/Managed Kafka, but not integrate with firehose

# Amazon MSK
- AWS Managed service for streaming kafka, can be serverless
- Alternative to Amazon Kinesis
- AWS manages the cluster: brokers, zookeepers, data stored on EBS volumes

# Apache Kafka
- streaming data service, for kafka
- cluster with brokers, producers push to topics, replicated to brokers, consumed by polling the topics

# KDS vs. Kafka
- File size limit: KDS-1MB, Kafka is 1+ MB
- Paritionining: KDS-Shards, Kafka is Topics with partitions
- Scaling: KDS - splitting shards, Kafka can only increase partitions
- Encryption: both TLS inflight, Kafka Plain or encrypted at rest, both at rest enc with KMS

# Serverless Ingestion Pipeline Example
- Source -> KDS -> Firehose -> S3 -> Lambda -> Athena -> S3 -> Redshift -> QuickSight Dashboard