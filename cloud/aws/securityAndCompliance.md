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
    - [some simulated dos is allowed](https://aws.amazon.com/security/ddos-simulation-testing/) with constraints
    - if permitted, only allowed up to 50,000 requests/sec
  - port flooding
  - protocol flooding
  - request flooding
- customer service policy for penetration testing
  - the policy defining this
  - i think it's also called the acceptable use policy

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
- uses envelope encryption with other services
  - data is first encrypted with the key in that service, then encrypted using the CMK (Customer Master Key) in KMS
- cannot manage these keys at all
  - can't delete
  - can't rotate
  - can't set policies
  - can't integrate them with iam

## Login/permission services

- STS (Security Token Service)
  - web service that enables you to request temporary, limited-privilege credentials for aws iam users, federated users, or aws services with roles
- sso
  - central management of access to accounts and business applications
- cognito
  - handles identity mgmt
  - offers pools and identiy pools
  - user pools are user directories that provide sign-up and sign-in options for your app users
  - identity pools provide aws creds to grant your users access to other aws services
  - syncs data across all devices
- directory service
  - provides multiple ways to use amazon cloud directory and microsoft active directory with other aws services

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
    - enhanced detection (real-time)
    - visibility and attack notification
    - 24/7 response team
    - application layer protection for applications on or using
      - ec2
      - elb
      - cloud front
      - global accelerator
      - route 53
- AWS WAF (Web Application Firewall)
  - application layer
  - protects against common exploits like
    - xss
    - sql injection
  - monitors web requests forwarded by an ELB, CloudFront or API Gateway
  - allow/deny access based on specified conditions
  - integrates with aws shield advanced
  - firewall manager
    - helps in the administration of WAF by presenting a centralized point of setting firewall rules across different web resources
    - manages vpc security groups, aws shield advanced, and waf rules on one platform even across multiple aws accounts
  - integrates with
    - cloudfront
    - application load balancer
    - api gateway
    - appsync
  - does not integrate with
    - internet gateway
    - ec2 directly
    - s3 (like static sites hosted on there)
- AWS Artifact
  - portal that provides access to AWS's compliance documentation such as PCI and ISO certifications, and SOC (System and Organization Control) reports
  - go-to resource for compliance related info
  - can view and manage agreements with aws
    - such as BAA (Business Associate Addendum)
  - no cost
  - self-service portal
- AWS Secret Manager
  - self explanatory
  - rotate, manage, and retrieve secrets
  - can audit access
  - integrates with other services
  - can make api calls to it to grab secrets data
- AWS Security Hub
  - view of across account security status status
  - security alerts
  - identifies deviations and best practices and suggests recommended resolution steps
- Security Bulletins
  - aws publishes security bulletins about the latest security and privacy events with aws services on the Security Bulletins page
- certificate manager
  - handles ssl certs for you
  - handles cert expiry
  - public and private certs provisioned through aws cert manager for use with acm-integrated services are free
  - you only pay for the aws resources you create to run your app
  - with private cert auth, you pay monthly for the operation of the private ca and for the private certs you use
  - two ways to verify the issuance of a security cert
    - creating a CNAME record in the hosted zone of the domain
    - via email confirmation send to the requester's email address
- config
  - keep track of config changes on AWS resources using the configuration recorder
  - real time
  - keeping multiple date stamped version in a reviewable history
  - also good for auditing
  - stored in s3 as log files

## Compliance

- aws is hella compliant with a lot of things
- by using them, you can remain compliant
