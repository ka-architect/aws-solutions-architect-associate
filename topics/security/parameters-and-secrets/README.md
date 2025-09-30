# SSM Parameter Store (Environment Variables)
- secure storage from configuration and secrets
- Encrypted via KMS, serverless, easy SDK, parameters are stored per account and per region
- Versioning can be enabled for data, security with IAM
- Parameter Tiers: Standard (limited parameters and size) and Advanced (pay)
- Advanced Parameter Policies: can assign TTL to keep data fresh, integrate with EventBridge to alert for expiring parameters

# AWS Secrets
- Meant for storing secrets with forced rotation (optional)
- Forced rotation is the difference between this and SSM
- Integrates with RDS, encrypted with KMS
- Exam Tip: DB credential management is best with AWS Secrets
- Ability to replicate secrets across region (primary and replica set up, replicas kept in sync)
- HA with ability to promote replica to primary
- Used For: multi-region apps with multi-region DBs

# Amazon Certificate Manager (ACM)
- Service for provisioning, managing, and deploying TLS/SSL certificates
- Supports both public and private certificates
- Integrates with LB's, CloudFront, API Gateway but cannot be used with EC2 (certificates cannot be extracted)
- Can be used to request public certifcates and enrolled for auto renewal
- Importing public certificates has no automatic renewal, but can be alerted via EventBridge
- Can also use AWS Config to check for expiry as well
- Can configure number of days before expiry in both ACM and AWS Config
- ACM & LBs: can provision and maintain ALB certs, enables http to https redirect
- ACM & API GW: depends on endpoint type, create domain name in API Gateway, store cert location (edge endpoint: us-east-1, regional: same region as gateway)
    - setup CNAME/A-ALIAS record in R53 based on region

# IAM Certificate Manager
- If ACM is not available in your region, use AWS CLI to upload your third-party certificate to the IAM certificate store.

# CloudHSM
- AWS CloudHSM is a cloud-based hardware security module (HSM) that enables you to generate and use your own keys
- Failing admin login more than twice zeroizes HSM appliance (all keys, certificates, and other data on the HSM is destroyed)
- No way to recover your keys if you lose your credentials, use multiple HSMs in mutli-AZ in production to avoid loss
