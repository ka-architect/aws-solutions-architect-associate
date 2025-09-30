# Serverless

## Serverless Introduction
- Serverless is pioneered by AWS via lambda, no more managing servers for developers
- Doesn't actually mean no servers, just no provisioning and managing of the servers
- Function as a Service

# AWS Lambda
- Virtual functions that run on-demand that don't require you to manage the servers at run time
- Used for short-execution code, billed for executions and the compute time (free tier available)
- Can increase RAM for better performance, can also increase resources per function (up to 10GB RAM)
- Supports many languages
- If high memory/long execution time/big file storage, dont use lambda
- Can be launched in both public and private VPCs
- Lambda function URLs are HTTP(S) endpoints dedicated to your Lambda function

## Lambda Environment Variables
- When you creating/updating Lambda function environment variables, encyrpted with KMS, decrypted at invoke
- First time creation/update of Lambda function environment variables in a region, default service key is created within AWS KMS to encrypt variables.
- To use encryption helpers after Lambda function is created, must create own KMS key instead of defualt, default key will give errors when chosen
- Creating your own key gives more flexibility, including the ability to create, rotate, disable, and define access controls, and to audit the keys

## Lambda Limits
- Limits per region: execution (memory, time, variables, disk, concurrency), deployment (size, variables)
- Concurrency Limits: 1000 concurrent executions can reserve concurrency at function level 
- Throttling: when concurrency limit is exceeded, lamdbda is throttled with 429 error, retries and then DLQ
- Request for more concurrency with Support Ticket

## Lambda Performance
- **Cold Start:** first init with new lambda function is usually slow, more code and dependencies = more init time, first request always slow rest are fast
- **Provisioned Concurrency:** eliminates cold start latency, resources allocated in advance of invoke
- **Snapstart:** improves lambda performance by invoking from initialized state, stores snapshot of memory and disk state for future use, can be versioned
- **Lambda @ Edge:** executing lambda logic at edge locations, attached to cloudfront, limits latency with serverless and global deployment, pay per use

## CloudFront Functions vs Lambda at Edge Functions
- CloudFront Functions: **viewer request** sent to CloudFront, CF sends to origin for origin response, CF responds to client with **viewer response**
    - high throughput millions per second, lightweight JS functions, high scale, latency sensitive CDN
    - Used for cache key normalization, header manipulation, JWT tokens
- Lambda at Edge: can change CF requests or responses, between viewer request and origin response, or between origin response and viewer response
    - scales to thousands per second, provides network access for external services

## Step Functions
- Allows you to build serverless workflows that use lambda to orchestrate
- Can integrate with AWS Services or On-Prem endpoints
- Can also include human intervention

# DynamoDB Serverless and Amazon Aurora DB Serverless
- Serverless RDBMS and NoSQL

# API Gateway
- Serverless gateway to allow public access to services via REST, proxies to destination (HTTP/EC2/lambda)
- Features: versioning, multiple environments, security (auth/API keys), rate limiting, throttling, API definitions
- Endpoint Types:
    - **Edge-Optimized Endpoints:** default, global clients, routed through CF, lives in one region but globally accessible
    - **Regional Endpoints:** non-CF, clients within 1 region
    - **Private Endpoints:** endpoints only accessible within the VPC via VPC ENI
- Security:
    - IAM roles for internal apps, cognito for external users, custom auth logic via lambda
    - Custom Domain Name: HTTPS via ACM, certificate must be in us-east-1 for edge endpoint, else in API Gateway region (update in R53)

# User Authentication
- **Cognito:** serverless, managed authorization service that gives users an identity to interact with application
- **Cognito User Pool:** cognito service for user sign ups for authorization, username password email phone mfa
    - can be federated with google, apple, facebook etc.
- **Cognito Identity Pool:** provide temporary AWS credentials to access AWS resources, uses User Pools as Identity Provider (federated access)
- Cognito to be used for web application users outside of AWS
- Exam Tip: think Cognito when you see hundreds of users, SAML or Mobile users questions