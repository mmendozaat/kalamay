### Route 53

Record Types:
1. SOA - Start of authority ?
2. NS - name server
3. A - Answer Record - maps domain to IP
- domains >--< IP (M:M)
etc
* read record types
*NS and SOA types are created by default for each hosted zone

Notes:
CNAME vs Alias
a. Alias
- alias for AWS resource - choose the resource
- is like CNAME but is AWS-specific
- points to resources inside AWS
- can be at zone apex
- queries are free

b. CNAME
- cannot create record at zone apex
- can point to DNS record anywhere
- queries are charged
- canonical name
- name to name mapping

Hosted Zones:
1. private
- VPC - set to true enableDnsHostnames
- VPC - set to true enableDnsSupport
2. public
- created automatically after buying/transferring a domain

Health Checks
1. endpoint - point to IP
2. status of other healthchecks
3. status of cloudwatch alarms

Simple Routing Policy
- 1 record: M IP
- round robin through multiple IPs
- no health check

TTL - time to live
- time to cache

Weighted Routing Policy
- add weight per record
- can configure optional health checks

Latency-Based Routing Policy
- define region per A record
- directs to closest region

*if Routing Policy points to an LB, option to enable target healthcheck

Failover Routing Policy
- define a record type (primary or secondary)
- healthcheck required for primary

Geolocation Routing Policy
- add geolocation (singapore, oceania, default, etc)
- restrict distribution rights
- language

Multi-value Routing Policy
- returns multiple IPs
- client determines which IP to use
- cannot use LB

If all records are unhealthy
- up to 8 unhealthy records are returned

#### Traffic Flow
- visual editor for routing configurations
- build cascading routing rules/policy for a DNS record
- ex. geolocation first then latency then weighted

#### Resolver
- DNS resolution for Hybrid Cloud Environments
a. outbound endpoint
- route53 directs to outbound endpoint
- endpoint routes to on-premise DNS server
b. inbound endopoint
- internal client goes to internal DNS
- internal DNS goes to inbound endpoint
- inbound endpoint goes to Route53 to get a response

#### Global Accelerator
- use 2 static anycast IP to go anywhere
- routed via AWS global network
- pricing perGB + perHour

Steps
1. Name
2. Listener
3. endpoint groups (1 per region)
4. add endpoints (EC2) to groups 

*Private DNS
- within VPC
