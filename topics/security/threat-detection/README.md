# Threat Detection

# AWS Web Application Firewall (AWS WFS)
- Protects from common layer 7 (HTTP) attacks such as XSS or SQL injection
- Can be deployed on ALB, API Gateway, CloudFront, AppSync, Cognito User Pool
- Cannot be deployed on NLB (Layer 4)
- Define ACL's: IP Set, HTTP Headers, size, location, rate
- Rule Group: reusable set of rules added to ACL
- WAF & ALB: since ALB doesn't have static IP enable Global Acclerator to get fixed IP Pair, set up WAF in same region as ALB

# AWS Shield and AWS Shield Advanced
- Protects against Layer 3 and Layer 4 attacks (SYN/UDP, reflection) DDoS attacks
- AWS Shield: free, enabled by default
- AWS Shield Advanced: more protection against EC2 and ELB, CloudFront, Global Accelerator, and Route 53
    - 24/7 DDoS response team (DRP), cost reimbursement from DDoS attack, automatically deploy WAF rules for HTTP attacks

# AWS Firewall Manager
- Centralized management of rules in all accounts in AWS Organization
- Common set of security policies for resources
- Can apply rules to newly created resources (cross-account in organization) to ensure compliance

# AWS WAF vs AWS Shield vs AWS Firewall Manager
- Commonly used together
- WAF: Granular protection of resources, define web ACL's
- WAF and FWM: cross-account WAF, WAF config acceleration, automating protection of new resources
- Shield: DDoS protection, WAF features, WAF rule automation, Reporting, Support Team

# DDoS Best Practices
- Edge locations: shield protection by default
- Infra: Shield and WAF, Auto scaling helps during attack, ELB helps distribute load
- Application layer: detect and filter malicious requests via CloudFront cache
    - WAF to filter and block signatures, rate-based rules, managed ruoels, CF cache and geo blocks
- Back-End Layer: protection at CloudFront, API Gateway, and ELB
    - Security Groups and Network ACL's filter traffic at ENI
    - Protect API Gateway endpoints with WAF, implement burst limits, header filtering, and API keys to auto-reject

# Amazon GuardDuty
- Uses ML for intelligent threat discovery to protect the account
- Integrates with EventBridge, Lambda, and SNS to alert
- Dedicated feature to protect from cryptocurrency attacks

# Amazon Inspector
- Service that runs security assessments on resources, scores them
- Only for the following resources:
    - EC2: analyze network, vulnerabilities
    - ECR: assess images on push
    - Lambda: scan code and dependecnies for vulnerabilities
- Compares all to CVE to make sure no known exploits
- Integrates with EventBridge and AWS Security Hub

# Amazon Macie
- Managed data security and privacy service to find and protect PII
- Uses ML and pattern recognition
