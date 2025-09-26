# S3 Object Storage

## Introduction
- S3 is one of the main components of AWS
- Infinitely scaling storage used by many websites as a backbone
- Integrates with many AWS resources

## Use Cases
- Backup and storage for files
- DR to move data to another region, snapshots
- Archiving files that havent been accessed
- Hybrid cloud storage: staging between AWS and on-prem during migrations
- Application/Media hosting
- Big data analysis, data lakes
- Static website content

## S3 - Buckets
- **Buckets:** regional, where you store objects in S3
- Can think of objects as files and buckets as directories
- Names of buckets must be globally unique across accounts and regions
- Naming convention: no uppercase, no underscore, 3-63 characters long, limitations of prefixes and suffixes

## S3 - Objects
- **Objects:** files that have a key stored within S3 bucket
- **Key:** full path of the file, can be nested (s3://bucket-name/img/pic.jpg)
    - prefix: img/, object name: pic.jpg
    - **Prefix:** everything in path(key) between bucket name and file name, leading and trailing slash included
- **Object Values:** content of the body
- Max object size is 5TB, to upload 5GB+ use multi-part upload
- **Metadata:** list of key/value pairs set by system and user
- **Tags:** unicode key/value pair (max 10), categorization, useful for security or lifecycle management
- **Version ID:** if versionining enabled, represents current version

## S3 - Bucket Policy (Security)
- User-based policies are IAM policies that determine which API calls allowed for the user
- Resource-Based Policies:
    - **Bucket Policies:** bucket-wide rules, can allow public access, cross-account access
    - **Bucket/Object Access Control List:** finer grain restrictions, can be disabled, not common
- IAM principal can allow S3 access if IAM user has allow, or resource policy has allow, and no explicit deny
- Encryption of objects handled via encryption keys
- JSON based structure like IAM policies in general
    - Resources would be the buckets or objects
    - Actions are S3 API's
- You can block public access at bucket creation, can also be set at account-level

## Static Website Hosting
- S3 can host static websites and allow access on the internet
- Enabled at creation time, can be modified
- Buckets come with a URL depending on region, make sure public access is enabled

## Versioning
- Versioning is enabled at the bucket level, in bucket properties
- If enabled, when user uploads the same file, it will create a version
- Helps protect against accidental delete, allows to restore/roll back to previous version
- **Delete Marker:** when a file is deleted with versioning on, it creates a delete marker in the version history
    - delete the delete marker to restore the previous version before delete
- If you disable versioning, versions are maintained

## S3 Replication
- Set on Orign bucket, 2 Types: Cross-Region Replication (CRR), Same-Region Replications (SRR)
- Asynchronous replication, versioning must be enabled on both source and target buckets
- Needs proper IAM permissions to S3
- Use Cases
    - CRR: compliance, low latency access, consistency in accounts
    - SRR: log aggregation, live replication between production and test environments
- Only new objects are migrated after enabling replication
- To migrate existing objects use S3 Batch Replication
- No chaining of replication, explicitly define replication from source to targets
- Delete markers can be replicated, but version deletes are not (optional setting)

## Storage Classes
- **S3 Standard: General Purpose**
    - Frequently accessed data, no retrieval fees
    - low latency, high throughput, can sustain 2 AWS failures
    - Use cases: big data analytics, mobile/gaming apps, content
- **S3 Standard IA**
    - Infrequently access data, rapid access if needed, pay-per-retrieval
    - cheaper than standard
    - 99.9% availability, used for DR/Backups
- **S3 One Zone IA**
    - HA in 1 AZ, risk of data loss of AZ destroyed, 99.5% availability
    - Used for secondary backup, recreatable data
- **S3 Glacier:** low cost storage, price for storage & object retrieval, used as archive or backup
    - **Instant Retrieval** backup access within ms (once per quarter data), minimum 90 day storage
    - **Flexible Retrieval** 3 options for retrieval, 90 day storage minimum
        - Expedited: 1-5 min, Standard: 3-5 hours, Bulk: 5-12 hours and free
- **S3 Glacier Deep Archive** long term storage, minimum 180 days, lowest cost
- **S3 Intelligent Tiering**
    - monthly monitoring and tiering fee, no retrieval charges
    - move objects between tiers based on usage
    - Frequent Access Tier (default), Infrequent Access Tier (30 days no access), Archive Instant Access Tier (90 days no access)
    - Archive Access Tier - optional, configurable for 90-700+ days
    - Deep Archive Access Tier - optional, configurable for 180-700+ days
- Can move between tiers manually or with lifecycle policies
- Durability: how many times an object lost by S3, very high availability, almost never lose files (11 9's), all classes
- Availibilty: how available a service is, very available

## S3 Express One Zone
- high performance, single AZ storage class
- Objects stored in bucket in single AZ, not in standard bucket, can choose which AZ
- Better performance than standard with a lower cost
- Low availability since within single AZ
- Use Cases: locate compute and storage in single AZ, low-latency data sensitive apps, AI/ML training, modeling, media processing, HPC
- Integrates with SageMaker, Glue, Athena, EMR, etc.

## Lifecycle Rules
- **Lifecycle Rules:** define how to move objects between storage classes automatically
- **Transition Action:** configuring when to move object to another class (move to IA after 30 days, move to Glaciers after 60 days)
- **Expiration Action:** configure objects to be deleted after x time (delete log files after 365 days, delete older versions after 90 days, delete incomplete uploads)
- Rules can be specified for certain prefix or by tags

## S3 Analytics (Storage Class Analytics)
- Helps decide when to transition to right storage class
- Gives recommendations for Standard & Standard IA, not for Glacier or One-Zone IA
- Generates report in csv file, takes about 1-2 days for analysis
- Good first step to determining how to create lifecycle rules

## Request Pays Bucket
- Usually bucket owner will pay for storage and retrieval, and all network costs
- Can set to have requestor pay for the request & retrieval costs as long as they have an AWS account for billing
- Used to share data between accounts

## Event Notifications
- Events are actions performed within S3 such as object created, deleted, restored, replicated, etc.
- Events are filterable, can create as many as required and are delivered instantly
- Can be fed into EventBridge and then to SNS, SQS, or lambda functions
- IAM policy needs to be created on destination for S3 to publish to destination, none needed for S3
- Events within EventBridge can be replayed, archived, and have reliable delivery

## Performance and Optimization
- Sacles to high request rates with 100-200ms latency
- 3500 writes/sec, 5500 reads/sec, per prefix in bucket
- No limit to number of prefixes in bucket
- **Multi-Part Upload:** break apart uploads into chunks and combine at end, parellilism, recommended starting at 100MB but required for 5GB
- **S3 Transfer Acceleration:** increase speed for upload, transfer file to AWS Edge location then sends to S3 in target region
    - supports multi-part upload
    - minimizes using public network and leverages AWS private neetwork instead

## Byte Range Fetches
- Parallelizes reads by requesting specific byte ranges
- Resilience for failures, speeds up downloads
- Can be used to retrieve parts of a file, head of a file

## Batch Operations
- Perform bulk operations on S3 objects (updating metadata, copy files, encrypt/decrypt, invoke lambda functions)
- Job consists of lists of objects, action to perform, and parameters of the action
- Can also be used to manage retries, send notifications, other reporting
- Combine with S3 Inventory (get objects) and Athena (query and filter) for full capabilities

## Storage Lens
- understad, analyze, optimize storage across organization based on 30 days usage and metrics, into a dashboard
- discover anomolies, cost savings, data protection
- Aggregate data by organization, accounts, regions, buckets, prefixes
- can export metrics to S3 in csv or parquet format
- dashboard displays metrics by region and account by default, cant be deleted but can be disabled

## Metrics
- Metrics include: Summary, Cost optimization, data protection, access management, events, performance, activity, status
- 28 metrics provided are free and available for 14 days
- Paid metrics are more advanced such as: activity, cost optimiation, data protection, status, available for 15 months
- paid metrics can be exported to cloudwatch for free and can be aggregated at the prefix level

# S3 Security

## Object Encryption
- Encyrption style determined via the header of the request
- There are 2 types of encryption, client side and server side
- There are different methods server side: SSE-S3, SS3-KMS, SSE-C
- **SSE-S3:** keys are handled and managed by AWS (AES-256), default encryption in S3, automatic yearly rotation without setting it
- **SSE-KMS:** keys handled and managed by AWS KMS, decryption is handled via API (counts towards quotes), performance issues with high throughput, enable yearly rotate setting
- **SSE-C:** keys fully managed by customers outside of AWS, key is never stored within AWS, no rotation policy
    - Must use HTTPS and pass the encryption key within the headers of the request
    - To read the files you must also provide the key
- **Client-Side Encryption:** using client-side encyrption libraries, must send encrypted to S3, must also decrypt at client side when retreiving
- Encryption in Transit is also supported, can be forced via bucket policy ``aws:SecureTransport: true/false``
- **DSSE-KMS:** double encryption via KMS is another measure that can be applied
- Bucket policies are evaluated first, then default encryption

## S3 CORS Protection
- Cross-Origin Resource Sharing (CORS) policies must be set at bucket level, restrict certain origins or allow all via *
- How CORS works:
    - Web browser based mechanism to allow requests from one origin to another
    - Controlled via header value: ``Access-Control-Allow-Origin``
    - Requesting server (origin) sends pre-flight request options to target server (cross-origin)
    - Cross origin web server will respond with if allowed from origin and what options
        - Response contains whether allowed to communicate and also what methods are allowed
    - Origin (if allowed to communicate) can then send allowed request type to cross-origin, else blcoked

## MFA Delete
- To prevent destructive actions, MFA can and should be enabled on S3 by root account/bucket owner for certain destructive actions (Permanent delete, Disable Versioning, etc)
- Versioning must be enabled to enable MFA, and can only be enabled via CLI

## Access Logs
- For auditing, logging all access, passed/failed transactions, logs can be exported to bucket in the same region
- Logs can be analyzed by Athena for further analysis
- Do not set the logging bucket as the monitoring bucket because it can create infinite loop of logs, increasing your costs
- Enable PutObject permissions on new bucket
- Enabled in bucket properties, must set target bucket

## Pre-Signed URLs
- URL generated for a duration that allows temporary access to download/upload private files
- User who generated the URL gives permissions via URL encoding for GET/PUT requests

## S3 Glacier Vault Lock
- When you need write-once-read-many (WORM) model, create a lock policy to block any changes (updates/deletes) to objects within the bucket
- Used for compliance, legal holds, data retention, etc

## S3 Object Lock
- Lock at object-level, not a bucket level, blocks object deletion for a duration
- WORM model, versioning must be enabled at the bucket-level
- 2 retention modes: Compliance and Governance
    - **Compliance:** similar to glacier vault lock, strict, prevents even root user from deleting versions, retention modes cannot be deleted/shortened when set
    - **Governance:** more flexible than compliance, blocks most users from deleting object version or alter lock settings
- **Retention Period:** protect the object for a duration, can be extended
- **Legal Hold:** protects object indefinitely, different from retention period, need IAM permission for ``s3:PutObjectLegalHold``

## Access Points
- **Access Points:** endpoints for accessing S3 prefixes, managed by IAM policy permissions
- Simplify security management on S3 buckets by shifting security from S3 to access points, and grant users/groups access to access points in IAM
- Used when secuity via users/groups gets too complicated, allows managing security at scale
- Has its own DNS Name for Internet Origin and VPC Origin
- **VPC Origin:** access points that are restricted to a VPC
    - Create a VPC endpoint to access S3 Access Point (Gateway is free/Interface is paidendpoints)
    - Permissions need to be added for VPC endpoint to access S3 bucket
- You can have security at 3 levels: Access Point, VPC Endpoint, S3 bucket policy

## S3 Object Lambda
- Use lambda to change object before retrieval by app
- Must create access point and lambda access point on bucket
- Inline object changes are cheaper than storing multiple versions of the files
- Use cases: redacting PII during object access, transforming data to another format, resizing/watermarking data

