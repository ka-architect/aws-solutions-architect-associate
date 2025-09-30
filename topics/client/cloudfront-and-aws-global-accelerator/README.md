# CloudFront

## Introduction
- **CloudFront:** AWS managed content delivery network, improves read performance by caching content at edge servers
- **Edge Servers:** hundreds of locations around the globe hosted by AWS to allow access to private resources
- CloudFront can provide DDoS protection by integrating with AWS WAF, AWS Shield
- **Geo Restriction:** restrict access to CloudFront distribution based on countries via Allow/Block lists, example: copyright laws
- Pay for cost of data out per edge location, different prices for different locations
- Can reduce costs by reducing edge locations or price classes: Price Class All, Price Class 200 (most regions), Price Class 100 (least expensive regions)
- Cache Invalidation can be done via TTL expiry or bypassing with CloudFront Invalidation, can be all files or specific files
    - The Cache-Control and Expires headers control how long objects stay in the cache, low values mean more hits to origin and less to edge/cache
- You can upload certificates but you cannot force SSL on CloudFront
- Optimized for both dynamic and static content with optimized cache behavior, latency, and throughput
- **Field-Level Encryption:** securely upload sensitive data (credit card numbers) to your origin
- **Origin Access Control (OAC):** restrict access to S3 bucket from CloudFront, without making S3 public

## CloudFront Origins
- CloudFront can support public AWS origins, VPC origins, or custom origins
- Secured with origin access control (OAC), for S3 at bucket level
- S3 is a public AWS origin, combined with CloudFront can be used to upload and distribute files in buckets, S3 static websites
- Apps hosted in private subnet within VPCs can connect to CloudFront privately
- Any public HTTP endpoint can connect to cloudfront
- **Origin Failover:** ability to set up multiple origins for redundancy, must have a distribution with primary

## CloudFront vs S3 Cross Replication
- CloudFront is a cache using global edge network, can display private files without making them public
- S3 cross replication is regional, file access is not cached but real-time, and read only

## CloudFront Private Resource Access
- **CloudFront Signed URL:**  Used when you want to restrict access to files, use an RTMP distribution, or when a client doesn't support cookies
- **CloudFront Signed Cookies:** Used to provide access to multiple restricted files, and when you don’t want to change your current URLs.

## VPC Origin
- Used for delivering content from private-subnet resources without making them public subnet
- Must allow access from CloudFront edge to private resources
- Secure since CloudFront can connect to VPC then connect to resources, resources stay private

# AWS Global Accelerator
- **AWS Global Accelerator:** using Anycast IP's and edge network, helps distribute global traffic to resources within one region, minimize latency
- **Unicast IP:** most common set up, where 1 IP points to 1 server
- **Anycast IP:** all servers have the same IP and the client is redirected to the nearest server
- AWS Global Accelerator creates 2 Anycast IP's for your application, which sends traffic to edge locations, then sends to app on private network
- Provides consistent performance due to intelligent routing and failover, no client caching
- Secure because you only have 2 exposed public IP's and can integrate with AWS Shield for DDoS protection

# AWS Global Accelerator vs CloudFront
- Both use edge network locations and integrate with AWS Shield
- CloudFront: good performance for static or dynamic content, static content is cached at the edge
- AWS Global Accelerator: no caching, proxies traffic to edge locations
