# Support
- different plans
  - basic (free with any account)
  - developer: $29/mo
    - 1 primary contact
    - unlimited cases
    - only email access during normal business hours
    - architectural guidance: general
  - business: $100/mo
    - phone, email, and chat access
    - unlimited contacts (IAM supported)
    - aws support api
      - some of the features of the aws support center via an api
    - architectural guidance: contextual to use-cases
    - interoperability and configuration guidance and troubleshooting
    - access to infrastructure event mgmt for additional fee
    - full set of advisor checks
  - enterprise: $15,000/mo
    - access to TAM (Technical Account Manager)
      - technical point of contact who provides advocacy and guidance to help plan and build solutions using best practices and proactively keep your aws env operating healthy
    - no fee for infrastructure event mgmt
    - well architected reviews
    - access to proactive reivews, workshops, and deep dives
    - architectural guidance: consultative review and guidace based on your applications
    - access to online self-paced labs
    - support consierge service
      - billing and account experts that specializze in working with enterprise accounts
      - billing and account inquiries
      - implement billing and account best practices
- all plans have default support
- [support severity](https://docs.aws.amazon.com/awssupport/latest/user/case-management.html#choosing-severity)
  - general guidance
    - developer plan or up required
    - development question
    - feature request
  - system impaired
    - developer plan or up required
    - non-critical functions behaving abnormally
    - time-sensitive development questions
  - production system impaired
    - business plan or up required
    - important functions of your application are impaired or degraded
  - production system down
    - buesiness plan or up required
    - your business is significantly impacted
    - important functions of your application aren't available
  - business critical system down
    - enterprise plan required
    - your business is at risk
    - critical functions of your application aren't available

## AWS Trusted Advisor
- all accounts have access to this
- helps with
  - cost optimization
  - performance
  - security
  - fault tolerance
  - service limits
- access to the 7 core trusted advisor checks and guidance to provision your resources following best practices to increase performance and improve security
- core checks
  - security groups
  - iam use
  - MFA
  - EBS public snapshots
  - RDS public snapshots
  - service limits
  - S3 bucket permissions
- notifications
  - need to be set up in the dashboard
- basic and dev support customers get access to 6 security checks and 50 service limit checks
- business and enterprise support customers get accss to all 115 checks

## AWS Service Health Dashboard
- displays general status of aws services
- can set up alerts, cloud watch events
- scheduled changes

## AWS Personal Health Dashboard
- like service health dashboard, but only contains the services that you would be affected by
- provides alerts and remediate guidance when aws is experiencing events that may impact you
- a personalized view of the health of AWS services, and alerts when your resources are impacted
- all accounts have access to this

## Handling compromised accounts
- change your AWS root account password
- change all IAM users' passwords
- delete or rotate all programmatic api access keys
- delete any resources in your account that you did not create
- respond to any notifications you received from aws through the aws support center and/or contact aws support to open a support case

## IEM (Infrastructure Event Management)
- service to help prepare enterprise support customers (and business support customers for an additional fee) plan for large-scale events
- such as
  - application launches
  - infrastructure migrations
  - marketing events
- strategic planning assistance before your event
- real-time support during the important moments

## APN Consulting Partners
- professional services firms that help customers design, architect, build, migrate, and manage their workloads and applications on aws
- include
  - system integrators
  - strategic consultancies
  - agencies
  - managed service providers
  - value-added resellers

## APN Technology Partners
- software solutions that are either hosted on, or integrated with aws
- include
  - ISVs (Independent Software Vendors)
  - SaaS
  - PaaS
  - developer tools
  - management and security vendors

## AWS Support Concierge Service
- assists customers with accounts and billing inquiries

## AWS Abuse Team
- assists when aws resourcees are being used to engage in
  - span
  - port scanning
  - DOS
  - intrusion attempts
  - hosting objectionable or copyrighted content
  - distributing malware
- anyone can report abuse, not just customers
- not customer support (that is different)

## AWS Operations Support
- enterprise support program
- provides operations assessments and analysis to identify gaps across the operations lifecycle
- also gives recommendations based on best practices

## Migration Acceleration Program
- designed to help enterprises that are committed to a migration journey achieve a range of business benefits by migrating existing workloads to aws
- provide
  - consulting support
  - training
  - services credits to reduce risk of migrating to the cloud
  - build a strong operational goundation
  - help offset initial cost of migrations
- includes
  - migration methodology for executing legacy migrations in a methodical way
  - robust set of tools to automate and accelerate common migration scenarios
  - experienced partner ecosystem
  - progessional services team