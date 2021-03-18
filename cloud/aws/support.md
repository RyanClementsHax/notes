# Support
- different plans
  - basic (free with any account)
  - developer: $29/mo
    - 1 primary contact
    - unlimited cases
    - only email access during normal business hours
    - architectural guidance: general
  - business: $100/mo
    - phone email and chat access
    - unlimited contacts (IAM supported)
    - aws support api
    - architectural guidance: contextual to use-cases
    - interoperability and configuration guidance and troubleshooting
    - access to infrastructure event mgmt for additional fee
    - full set of advisor checks
  - enterprise: $15,000/mo
    - access to TAM (Technical Account Manager)
    - no fee for infrastructure event mgmt
    - well architected reviews
    - access to proactive reivews, workshops, and deep dives
    - architectural guidance:  consultative review and guidace based on your applications
    - access to online self-paced labs
- all plans have default support

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