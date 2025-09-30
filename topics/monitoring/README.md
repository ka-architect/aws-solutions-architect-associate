# Monitoring

# CloudWatch
- Metrics are provided for every service in AWS
- Dashboarding and custom metric features
- Metric Streaming: continously stream metrics to a destination (AWS or 3rd party)
- **CloudWatch Logs:** place to store AWS app logs
- **Log Groups:** logs grouped by apps
- **Log Streams:** sorted by groups, can be specific log files or even container logs
- **CloudWatch Log Insights:** querying capability within CloudWatch logs
- Exporting to S3 is not real-time
- **CloudWatch Log Subscription:** real-time processing or analysis of log events from CloudWatch (sent to KDS, Firehose, Lambda)
- **Subscription Filter:** filter which logs are delivered
- **LiveTail:** like kibana, real-time log stream, great for debugging, free for 1 hour, else pay
- **CloudWatch Unified Agents:** installed on resources to export logs and granular metrics to CloudWatch, centralized configuration in SSM Parameter Store
- **CloudWatch Alarms:** used to trigger notifications for any metrics with various options
    - States of Alarms: OK, INSUFFICIENT_DATA, ALARM
    - Duration of alarm can be set
    - Alarm Targets: ASG (scaling process), EC2 (stop/restart/etc.), or SNS (custom)
- **Composite Alarms:** alarms triggered on multiple metrics, more specific/complex means less noise, chained together with AND/OR
- **EC2 Instance Recovery:** guarantees alarm actions will maintain EC2 settings
- Can’t directly set CloudWatch Alarms to update the ECS task count

## CloudWatch Insights:
- **CloudWatch Container Insights:** tool for dashboarding container metrics, runs as an agent in the container
- **CloudWatch Lambda Insights:** monitor and debug solutions for lambda, provided as a lambda layer
- **CloudWatch Contributor Insights:** analyze log data and create time series for top N contributor data
- **CloudWatch Application Insights:** tool for dashboarding monitored applications for debugging, reduces MTTR

# EventBridge (Event Bus)
- Scheduled Events: lambda functions, cron jobs
- Event: event rules to react to service actions
- Stores in JSON document
- 3 types of bus: Default Event Bus (AWS), 3rd Party Event Bus, Custom Event Bus (custom application events)
- Replay feature
- **Schema Registry:** structures data in bus which allows you to generate code, helps analyze events in bus, allows versioning
- Resource Based Policy: manage permission for an event bus, used when having a centralized event bus across the organization
- Use Amazon EventBridge (Amazon CloudWatch Events) to run Amazon ECS tasks when certain AWS events occur (like S3 PUT)
- Amazon EventBridge can't track the actual traffic to your ALB

# CloudTrail
- Logs all API calls made by all services, encrypted by default by SSE-S3
- Great for finding out who deleted something
- 3 event types: management, data, and insights
    - **Management Events:** operations on AWS resources, logs read and write events, control plan operations, can include Non-API
    - **Data Events:** high volume operations on AWS resources, logs and separates read and write events, data plane operations, focused on API activity
    - **Insight Events:** can help detect unusual activity, helps with resource provisioning, creates baseline of management events for normal activity
- Logs are retained for 90 days, can be exported to Athena
- Use Case: User Makes DeleteTable API call -> CloudTrail logs the API call -> EventBridge creates the Event -> SNS sends the alert
- **CloudTrail log file integrity validation:** determine whether a log file was modified, deleted, or unchanged after CloudTrail delivered it
    - enables you to assert positively that the log file itself has not changed, or that particular user credentials performed specific API activity
    - when enabled, CloudTrail creates a hash for every log file that it delivers
    - also creates and delivers **digest file** to S3 folder in log files directory that references the log files for the last hour and contains a hash of each
- **CloudTrail Lake:** run SQL-based queries to analyze CloudTrail logs, converts from JSON to ORC (columnar storage format optimized for fast retrieval)
    - Aggregates data into event data stores, based on advanced event selector criteria

# AWS Config
- auditing and reporting compliance of AWS resources
- records config changes over time allows you to be able to roll back if needed
- does not deny an action but gives you overview of resource configuration
- Use Cases: Any unrestricted SSH access? Any public buckets? How has ALB config changed over time?
- Integration: S3 to export and analyze with Athena, SNS to alert any changes
- **Config Rules:** evaluate configuration over time and alert for each change, at an interval
- Allows you to view compliance, config, and usage of resources over time
- Can automate remediation of non-compliant resources via SSM automation docs, custom automation, or lambda

# CloudWatch vs CloudTrail vs AWS Config
- CloudWatch: perfomance monitoring dashboard, events and alarms, log aggregation and analysis
- CloudTrail: global service recording API calls, define trails for specific resources
- AWS Config: record config changes and give historical view of changes, evaluate compliance, create rules for remediation

# Amazon Managed Service for Prometheus
- serverless, Prometheus-compatible monitoring service for container metrics that makes it easier to securely monitor container environments at scale
- Monitor containers running on EC2, ECS, Amazon EKS (on EC2 and on Fargate) in the cloud as well as in hybrid environments
- Use with Amazon Managed Grafana for monitoring, alerts, and dashboard views across Kubernetes environments, including both host- and application-level monitoring

# Amazon Managed Grafana
- managed and secure data visualization service that you can use to instantly query, correlate, and visualize operational metrics, logs, and traces from multiple sources