# Route 53

## Domain Name System (DNS) Basics
- When queried: translates hostnames to IP's via a hierarchal naming structure
    - .com -> google.com -> www.google.com -> api.google.com -> IP for communication
- **Registrar:** where you register your domain name, godaddy or R53
    - Registrar allows you to purchase domain names and provides you with DNS service for routing, registrar is not a DNS service on its own
- **DNS Record:** official record of your domain name which defines DNS routing
- **Zone File:** contains the DNS records
- **Nameserver:** resolves the DNS queries
- **Top Level Domain (TLD):** the first part of the query flow (.com/.net)
- **Second Level Domain (SLD):** the hostname attached to TLD (amazon.com)
- **Fully Qualified Domain Name (FQDN):** the entire domain name, not including protocol (www.amazon.com)
    - **Protocol:** connection type (http:// or https://)
    - **Subdomain:** after the protocol but before SLD (www. in www.amazon.com)
- Queries will recursively ask servers how to route the request
    - Ask Root DNS Server (ICANN) if it knows TLD (.com), confirms or denies
    - Ask TLD DNS Server (IANA) if it knows amazon.com, confirms or denies
    - Ask SLD DNS Server (Registrar) if it knows the IP for amazon.com, confirms or denies
    - If all confirmed, local DNS server (ISP) will cache the response
    - Route to the IP associated with FQDN
- **Hybrid DNS:** resolves DNS queries between VPC and other networks, via VPC peering
- **Resolver Endpoint:** inbound or outbound, for public and private DNS resolution

## Route 53
- **Route 53:** authortative DNS and registrar service managed by AWS
- Allows for health checks of your public resources, since outside of the VPC
- Can register and access DNS records within R53
- Can be a hybrid dns resolver for both public and private clients

## Records
- **Records:** defines how to route traffic to domain
- Each record contains contains the Domain Name, Record Type, Value of the Record, Routing Policy, and TTL
- Types of records:
    - **A Record:** mapping of hostname to IPv4 address
    - **AAAA Record:** mapping of a host name to IPv6 address
    - **CNAME Record:** maps hostname to another hostname, target must be a A/AAAA (zone apex)
    - **Zone Apex:** theroot domain name (example: www.google.com, not just google.com)
    - **Nameservers (NS):** name servers for the hosted zone, traffic routing to domain
- **Hosted Zones:** container for records to define routing to domains and sub-domains, AWS service with monthly fee for each hosted zone
    - **Public Hosted Zone:** records for internet-traffic routing, answers queries for public clients
    - **Private Hosted Zone:** records for routing within one or more VPC, private domain names to identify private resources
- **Records TTL:** mandatory record for caching DNS responses to reduce traffic to DNS servers, not required for ALIAS records

## Alias Records
- **Alias Records:** specific A/AAAA records for R53 which allow you to point a hostname to a private resource
- can recognize changes in resource IP
- works for root (zone apex) and non-root domains, can set to variety of AWS services, but not for EC2 DNS names
- free service with health checks available 
- Use case: Point SLD to ALB 
- CNAME allows for pointing hostname to hostname only for non-root domains

## Routing Policies
- **Routing Policy:** how DNS server responds to DNS queries, unlike LB it just responds to DNS queries
- **Simple Routing Policy:** routes traffic to a single resource, client chooses randomly if multiple values, no health checks
- **Weighted Routing Policy:** control % of requests that go to each resource, if none set it returns equally, great for blue/green setup
- **Failover Routing Policy:** uses health checks on public resources, based on health redirect traffic
    - health check controls: number of fail checks before failover, interval of health checks, can be inverted
    - **Calculated Health Check:** combine multiple health checks to determine the health, specify conditions and priority of conditions, up to 256 requests
    - cannot access private hosted zone since R53 is outside of the VPC, use cloudwatch metric instead
- **Latency-Based Routing Policy:** redirect to least latent resource based on traffic between users and regions
- **Geolocation Routing Policy:** define redirect to nearest resource based on users location, not latency, needs a default record in case of no match, can use health checks
- **Multi-Value Answer Routing Policy:** helps redirect traffic up to 8 multiple resources, not an LB since its client side
- **Geoproximity Routing Policy:** uses R53 Traffic Flow feature to, shift traffic to a specific resource with bias, can shrink or expand bias
    - All traffic west of mississippi river to us-west, all traffic east of mississippi river to us-east
    - Can change to all traffic west of rockies to us-west, all traffic east of rockies to us-east
- **IP-Based Routing Policy:** routing based on client IP's, for when you want to redirect specific ISP CIDR's to resources

