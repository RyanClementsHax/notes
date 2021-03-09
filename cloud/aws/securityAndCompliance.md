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

## Other security services
- AWS Organizations
  - managing multiple aws accounts
- Amazon GuardDuty
  - threat detection
- Amazon Inspector
  - analyzes VPC env for potential decurity issues
- AWS Shield
  - provides DDoS protection
  - next level of support allows for AWS to actively assist you when problems arise
- AWS WAF (Web Application Firewall)
  - monitors web requests forwarded by an ELB, CloudFront or API Gateway
  - allow/deny access based on specified conditions
- AWS Artifact
  - portal that provides access to AWS's compliance documentation such as PCI and ISO certifications, and SOC (System and Organization Control) reports