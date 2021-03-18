# Security And Compliance

## Shared responsibility model
- aws
  - responsible for phyiscal layer through to the virtualization layer
  - responsible for the physical security of the data center
  - security 'of' the cloud
- you
  - anything above the virtualization layer
  - security 'in' the cloud

## Penetration testing
- approved pen testing assessments
  - EC2, NAT Gateways, ELB
  - RDS
  - CloudFront
  - Aurora
  - API Gateways
  - Lambda and Lambda Edge functions
  - Lightsail resources
  - Elastic Beanstalk envs
- prohibited testing
  - DNS zone walking via Amazon Route 53 hosted zones
  - DoS, DDos, simulated Dos, or simulated DDoS
  - port flooding
  - protocol flooding
  - request flooding

## AWS KMS (Key Management Service)
- can generate keys in an AWS CloudHSM hardware cluster
- can import keys from another service
- data is submitted directly to KMS for encryption/decryption using the master keys
- integrates with
  - S3 and Glacier
  - Storage Gateway
  - EBS and RDS
  - DynamoDB
  - SNS
  - CloudTrail
- uses hardware security modules (HSMs) to safely store the keys
- cloud hsm is an alternative to this where your client manages
  - "you are on your own" for key management

## Other security services
- AWS Organizations
  - managing multiple aws accounts
- Amazon GuardDuty
  - managed threat detection
  - continuously monitor and protect your aws accounts and workloads
  - multiple data source events
    - cloud trail
    - vpc flow logs
    - dns logs
  - customizable
    - add own threat lists and ip lists
- Amazon Inspector
  - for ec2 instances
  - analyzes VPC env for potential decurity issues
  - automated security assessment service
  - finds
    - exposure
    - vulnerabilities
    - deviations from best practices
  - best practice is to run it regularly (preferred weekly)
- AWS Shield
  - provides DDoS protection
  - next level of support allows for AWS to actively assist you when problems arise
  - standard
    - free
    - protects ip and transport layers
  - advanced protection
    - ddos cost protection (protection from spikes in aws usage, not something like lost business)
    - enhanced detection
    - visibility and attack notification
    - 24/7 response team
    - application layer protection
- AWS WAF (Web Application Firewall)
  - application layer
  - protects against common exploits like
    - xss
    - sql injection
  - monitors web requests forwarded by an ELB, CloudFront or API Gateway
  - allow/deny access based on specified conditions
  - integrates with aws shield advanced
- AWS Artifact
  - portal that provides access to AWS's compliance documentation such as PCI and ISO certifications, and SOC (System and Organization Control) reports
  - go-to resource for compliance related info
- AWS Secret Manager
  - self explanatory
  - rotate, manage, and retrieve secrets
  - can audit access
  - integrates with other services

## Compliance
- aws is hella compliant with a lot of things
- by using them, you can remain compliant