# Networking and VPC

# IP Addresses
## Basics
- IP Addresses made up of 4 octets ex: 1.2.3.4
    - 1.2.3.4/32 = last octet cannot change
    - 1.2.3.4/24 = last octet can change
    - 1.2.3.4/16 = last 2 octets can change
    - 1.2.3.4/8 = last 3 octets can change
    - 1.2.3.4/0 = all 4 octets can change
- **IANA:** Internet Assigned Numbers Authority, established certain blocks of IPs as public and private
- Private IP Range only allows certain values:
    - 10.0.0.0 - 10.255.255.255 (10.0.0.0/8) for big networks
    - 172.16.0.0 - 172.31.255.255 (172.16.0.0/12) AWS default VPC
    - 192.168.0.0 - 192.168.255.255 (192.168.0.0/16) for home networks
- All the rest are public IP addresses 

## CIDR
- **Classless Inter-Domain Routing:** CIDR, domain routing, method for allocating IP addresses
- Define the IP range for IPv4
    - xx.xx.xx.xx/32 means only the 1 IP listed
    - xx.xx.xx.xx/0 means all IPs
- CIDR consists of Base IP and Subnet Mask
    - **Base IP:** represents IP contained in the range (10.0.0.0)
    - **Subnet Mask:** how many bits in the IP can change (10.0.0.0/26)
- Common subnet masks: /8 (255.0.0.0), /16 (255.255.0.0), /24 (255.255.255.0), /32 (255.255.255.255)

## Subnet Mask
- Allows part of the underlying IP to get additional next values from the Base IP
- 2^x determines number of bits
    - /32 = 2^0 = 1 IP, /31 = 2^1 = 2 IPs, /30 = 2^2 = 4 IPs, ...
    - /24 = 2^8 = 256 IPs, /16 = 2^16 = 65,536 IPs, /8 = 2^32 = 16,777,216, /0 = all IP's possible

# Virtual Private Cloud (VPC)
- **Default VPC:** Each AWS account has a default VPC, with internet access
    - if no subnet, EC2 launched in default VPC, with public and private IP
- Multiple VPC's allowed in 1 region (soft limit of 5, which can be increased)
- 5 CIDR max per VPC, minimum size /28, maximum size is /16
- If the VPC is private, then only private IPv4 ranges allowed
- VPC CIDR's should not overlap with other connected networks (either external or another AWS VPC)

## Subnets
- **Subnet:** segment of the network within a VPC
- AWS reserves 5 IP addresses for each subnet created, first 4 and last 1
    - Example 10.0.0.0/24 means 10.0.0.0, 10.0.0.1, 10.0.0.2, 10.0.0.3, and 10.0.0.255 are reserved
- Exam Tip: if you need 29 IP Addresses, don't pick /27 which is 32 IPS (because AWS takes 5), instead pick next biggest that has 5 IP buffer (/26 which is 64-5 IPs available)
- Best to choose smaller CIDR blocks for public facing subnet, careful of overlap

# Internet Gateway
- **Internet Gateway:** allows resources within a VPC to connect to the internet (public access)
- Scales horizontally, HA, redundant
- Created separately from a VPC, attaches to VPC, must be in a 1:1 ratio, only one Internet Gateway per VPC
- Need to edit route tables to allow after creation
- Flow: EC2 in public subnet -> EC2 Security Group -> Route Table -> VPC Router -> Internet Gateway -> Internet
- **Internet Gateway Route Table:** create IGW and assign to VPC, create IGW and add subnets, Enable public IPv4 on subnets/resources, to enable public add 0.0.0.0/0 to route table

# AWS Network Summary
- **Bastion Host:** public jump host for SSH access to private subnet resources, must allow bastion host port 22 to resource, disable other SSH access for security (SSM can replace)
- **NAT Instance:** not a gateway, older option, allow private EC2 instances internet access, setup in public subnet, disable Source/Destination flag on EC2
    - Must have an elastic IP assigned to it
    - Rewrites packets source IP to NAT IP
    - Must allow resource to access the NAT instance
- **NAT Gateway:** managed by AWS, provides scalable internet access to private subnets, IPv4 only and must be in a public subnet
    - created in AZ (resilient within AZ, down if AZ is down), uses elastic IP
    - allows subnet to subnet communication
    - requires an internet gateway for public access, point other route tables to NAT Gateway for access only
**NACL:** Network Access Control List, stateless subnet-level rules for in/out traffic
    - must explicity define in and out traffic, meaning 2 rules need to be defined, rules evaluated in order
    - remember to add **ephemeral ports** to allow response traffic (MS: 49152-65535, Linux: 32768-60999, DB: 1024-65535)
    - only one NACL per subnet, new NACL's deny everything by default, default NACL accepts everything
    - many rules can be applied and priortized, last rule is deny (AWS recommends priority in 100s to allow adding rules in the future)
**Security Groups:** stateful resource-level rules (if 1 way allowed, response is automatically allowed), rules evaluated before hand and remembered
**VPC Peering:** non-transtive connections between VPC's, allowed as long as no overlapping CIDRs
    - non-transitive means must be explicity enabled on both
    - must update any route table VPC CIDRs after enabling
    - allowed cross-account/cross-region
**VPC Endpoints:** provide private access to public resources securely via **AWS PrivateLink (AWS private network)**
    - no need for NAT Gateways, VPC peering, public internet, Route Tables, however requires ENI and NLB
    - **Gateway Endpoints:** free endpoints for public AWS services within VPC such as S3, DynamoDB, CloudFormation, SSM
    - **Interface Endpoints:** paid endpoints for all AWS services including the above (since not free, use gateway when possible)
- **VPC Flow Logs:** set up at VPC, subnet, and ENI, monitors ACCEPT/REJECT traffic, good for debugging
    - can be exported to S3, CloudWatch Logs, or KDF
    - srcaddr/destaddr, srcport/destport, action
    - can help determine whether issue with NACL or SG (1 way ACCEPT and 1 way REJECT for same request is NACL issue, else can also be SG)
- **Site-to-Site VPN:** (S2S VPN), connection from AWS to another site over the internet, good backup for Direct Connect resiliency
    - encrypted over public network, is slower than Direct Connect since using ISP
    - **Customer Gateway:** must be applied to target destination side of the connection
    - **Virtual Private Gateway:** must be applied on AWS side of the connection, must enable Route Propogation setting, customized Autonomous System Number (ASN)
- **AWS VPN CloudHub:** hub/spoke model to manage and secure S2S VPN connections that exist
- **Direct Connect:** dedicated private connection to AWS to another site, long initial setup, faster and more secure than S2S, needs Virtual Gateway on VPC
    - encrypted only when combined with VPN
    - Make sure to enable VLAN connection from site to DX location (public/private VIF depending on AWS resource target)
    - DX location consists of 2 cages, AWS and Customer, paying for access to this
    - Must use DX GW to connect multi-region VPC's, DX will have private VIF into those regions
    - **Dedicated Connection:** physical ethernet port dedicated to customer, request made to AWS then completed by DX partners (up to 100GBPS)
    - **Hosted Connection:** request made and completed by DX partners (up to 10GBPS for selected partners)
    - Increase resiliency of the Connection by adding multi DX locations and S2S VPN
- **Direct Connect Gateway:** ability to set up direct connect to multiple VPC's
- **Classic Link:** not AWS PrivateLink, connect EC2-Classic privately to VPC
- **Transit Gateway:** regional, allows for transitive peering to VPC, Direct Connect, VPN, centralized place instead of managing individual connections
    - Supports multi-cast IP, multi-S2S, cross-region (peer Transit Gateways in other regions together), cross-account
    - helps increase bandwidth of S2S VPN connection to AWS via ECMP by leveraging all S2S VPN flows
    - **Equal Cost Multi Path:** ECMP, routing strategy to forward a pack over multiple best paths
- **VPC Traffic Mirroring:** capture/monitor/filter ENI traffic for content inspection, threat monitoring, or debugging
- **Egress-Only Internet Gateway:** an IPv6 NAT Gateway (NAT Gateway only for IPv4)
    - prevents IPv6 from being access by the internet
    - must update route tables to include NAT Gateway to allow public internet access
    - to enable public also use Internet Gateway, then NAT Gateway, update the route tables!
    - IPv6 allow all looks like this: ::/0
- **AWS Network Firewall:** protect entire VPC with firewall for layer 3 to layer 7 by inspecting all traffic within the VPC, centralized rule management
    - Uses a gateway load balancer internally
    - **Traffic Filtering:** ALLOW/DROP/ALERT rule matches
    - **Active Flow Inspection:** realtime protection against network threats, intrustion protection

# Cost Analysis

## Network
- Traffic is free coming into EC2 from public
- Private IP communication within an AZ is free, Private IP commuinication across AZ is not free
- Public IP communication to another AZ costs money
- Whenever possible use Private IP communication to reduce costs
- Try to minimize egress traffic as much as possible (egress means traffic leaving AWS) as ingress is usually free
    - Determine whether its best to deploy in AWS or within another site for cost savings (think data size when communicating between resources API vs Display)
- Direct Connect co-located in the same region helps lower egress costs

## S3
- S3 to internet/computer is not free (egress)
- S3 Transfer Acceleration for faster times but additional cost on top of egress
- S3 to CloudFront is free, CloudFront egress is not free, but cheaper than S3 egress
- S3 CRR also has a cost

## NAT Gateway vs VPC Endpoint
- NAT Gateway to Internet Gateway introduces hops and delays and costs
- Instead leverage VPC endpoints whenever possible
- Remember route table modifications

# Network Security
- Outer VPC-Level: AWS Firewall Manager to manage protection across VPC, AWS Shield to protect against Layer 4 Attacks
- Inner VPC-Level: AWS Network Firewall to protect the network layer within VPC (layer 3 to layer 7)
- Outer Subnet-Level: NACL restricts access to subnet, sits just outside the subnet
- Outer Resource-Level: AWS WAF to protect from layer 7 attacks, Security Groups restrict access to resources