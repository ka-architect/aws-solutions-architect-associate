# Messaging

## Introduction
- Messaging is used to decouple applications
- Different types of messaging: Queue Model, Publish/Subscribe Model, Data Streaming Model

# AWS Simple Queue Service (SQS)
- Fully managed AWS queue model messaging service 
- Features: unlimited throughput, unlimited messages, short-lived message retention (default 4 days), low latency
- Message Types: At-Least-Once Delivery (duplicates) or Best Effort Ordering (order)
- **At-Least-Once Delivery:** messages are processed at least once, can have duplicate messages
- **Best Effor Ordering:** messages are consumed in order
- Allows producers and consumers to scale independently (EC2 or Lambda functions)
- ASG on consumers can be by Queue Length CloudWatch metric
- Supports in-flight (HTTPS) and at-rest (KMS) encryption
- Access controlled via IAM and resource policies
- 256KB max message size
- You can set priority to a queue, but cannot set priority to items in the queue
- For decoupling on-prem/cloud use SQS and Amazon Simple Workflow Service (SWF) together

## Message Visibiility Timeout
- **Invisible Message:** when message is polled by consumer it becomes invisible to other consumers (for a duration)
- After timeout and message is not deleted it becomes visible again
- Creates risk for reprocessing if consumer A crashed after processing but not deleting the message in queue
- Consumer has access to Message Visibility API where it can increase the timeout if there is an issue

## SQS Long Polling
- **Long Polling:** allows consumer to wait for messages for a duration, limits SQS API calls, reducing latency and costs
- Enabled at the queue level

## SQS FIFO Queues
- first in first out message ordering in SQS queue as messages can be received out of order
- Queue name must end with .fifo, to have exactly once send functionality
- Removes duplication via duplication ID
- Messages are also processed in order by consumers

# AWS Simple Notifiation Service (SNS)
- AWS managed service to allow one message to one or many receivers
- Publish/Subscribe model, where many applications can subscribe to a single SNS topic, asynchronous communication between consumers
- Supports 12.5 million subscriptions per topic, and up to 100k topics
- Producers and Subscribers can be EC2, lambda, or many other AWS services
- Publish Types: Topic Publish (topic-level publishing) or Direct Publishing (publishing directly to application endpoints)
- In-flight, at-rest, and client side encryption
- IAM policies and Reousrce access policies for security
- **SNS Message Filtering:** JSON policy used to filter messages sent to subscribers, without filter subscriber receives every message

## SNS FIFO Topics
- Supports FIFO ordering of events
- Must have .fifo in topic name, subscribers don't need to be FIFO but can be
- ORdering by MessageGroupID and deduplication
- Exactly Once Delivery (FIFO) vs At Least Once Delivery

# SNS and SQS Fan-Out
- Pattern used when a single message needs to go to multiple queues
- SQS queues are the subscriber to the topic, makes this asynchronous
- Fully decoupled with no data loss due to persistence, delayed processing, retries, and DLQs, ability to add or remove queues in the future
- Cross-Region delivery
- SNS needs policy to write on SQS queue
- Use Case: S3 events to multiple queues or S3 events to Kinesis Data Firehose

# Kinesis Data Streams (KDS)
- **Kinesis Data Streams:** streaming model, collect and store streaming data in real-time
- Consumers can be applications, lamdbas, or other AWS services
- default 24 hour retention, up to 365 day message retention wgeb set, peristent for replay, 1MB max data size (real time data is usually very small)
- Paritionining maintains order with PartitionID included in data
- Capacity modes: provisioned or on-demand
    - **Provisioned:** choose number of shards (how big a stream is), pay per shard per hour, more shards more throughput, default
    - **On-Demand:** no need to provision or manage capacity, scales automatically based on 30 day peak, pay per stream per hour and per GB data in/out
- Consumers can be EC2 or Firehose and can store their results in DynamoDB, Redshift, or S3

# Kinesis Data Firehose (KDF)
- Fully managed service that loads streaming data near real time to a service 
- Can ingest, transform via internal function or lamdba, and load to another service
- Data is accumulated into a buffer, after buffer full/buffer threshold, it can batch data to consumer, deletes
- Auto scaling, serverless, pay per use

# KDS vs KDF
- KDS: realtime, streaming, provisioned/on-demand mode, 365 day retention, replay functionality, persisted data
- KDF: near real time, load to destintation, fully managed, auto scaling, no storage (buffer), no replay, risk for loss unless exported to S3

# Amazon MQ
- fully managed service for apps using RMQ/AMQ (MQTT, AMQP, STOMP, WSS, OpenWire), contains both queues and topics
- Costly to re-engineer to SQS/SNS so this is a bridge
- Runs on EC2 so scaling is limited, need to set up multi-AZ servers

# Messaging Comparison
- SQS: queue-based model
    - consumer pulls data then deletes after processing, unlimited consumers
    - no need to provision capacity, scales to thousands of messages
    - ordering with fifo, otherwise at-least-once delivery
    - messaging delay capabilities
- SNS: publish/subscribe model
    - push data to manay subscribers, up to 12.5 million subscriber, and 100k topics
    - data is not persisted, lost if not delivered
    - no need to provision throughput
    - fifo capabilities for ordered events
    - can be integrated with SQS for fanout method
- Kinesis: streaming model
    - standard consumption: pull data, 2MBPS/shard
    - enhanced fan out model
    - ability to replay data due to peristence
    - meant for realtime data
    - ordering at shard level via partitioning
    - data expiration
    - provisioned (manual shard) or on-demand capacity (auto shard)


