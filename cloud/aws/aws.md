# AWS

- [hands on tutorials](https://aws.amazon.com/getting-started/hands-on/)
- create an account with your work email to help establish account ownership if things go wrong
- not all services available in all regions
- quotas
  - all services have default limits
  - aws needs to do capacity planning
  - helps prevent you from fat fingering spinning up more instances than you wanted
- support center
  - account and billing support
  - service limit increase
    - some limits are hard like s3 object size limits
  - technical support

## Regions
- distinct geographic area
- made up of 2 or more availability zones
- set up services in multiple regions for disaster recovery

### Availability zones
- identified by the region prefix with an alphabetic character at the end (a-z)
  - what is considered 1a for one account might be totally different for another account (its randomized)
- where resources located
- contains datacenters
- availability zones in same region have low latency between each other

## Edge locations
- CDN cache locations
- where Cloud Front stores its data
- tons of these

### Points of presence
- edge places where AWS can replicate data

## Networking
- direct connect
  - ability to connect on-prem servers into the VPC via a VPN
  - without going over the internet
  - scales
  - reduces network costs
  - creates consistent network performance
  - a literal direct connection (no vpns)
- you can use vpns to connect to your private cloud
- route 53
  - DNS service
- virtual private cloud

## Compute
- EC2
- Lambda

## Storage
- S3
- RDS
- Glacier

## Other services
- quick start
  - ability to use templates for AWS architecture like bash-in-host
- aws analytics
  - many services
  - athena
    - analyze data in S3 using SQL
    - serverless (basically)
    - pay per query
    - no need to set up ETL
    - can be used to query log files stored in s3
    - can be used to generate business reports on data stored in s3
    - analyze aws cost and usage reports
    - run queries on click-stream data
    - can do this over tons of data
    - no data warehouses or clusters to manage
  - EMR (Elastic Map Reduce)
    - hadoop framework
- macie
  - uses ML and NLP to discover, classify, and protect sensitive data stored in S3
  - dashboards, reporting, and alerts
  - works directly with data stored in S3
  - can also analyze cloud trail logs
  - great for PCI-DCS and preventing id theft
- lightsail
  - private virtual server
  - for getting started very quickly
- rekognition
  - video/image analysis
- device farm (mobile lab)
  - physical devices to test mobile applications on
- mechanical turk
  - outsource processes or jobs to a distributed workforce
  - crowdsourcing marketplace
- elastic beanstalk
  - fastest way to deploy and application
  - reminds me of build packs
  - wide selection of application platforms
  - management and updates
  - variety of application deployment options
  - monitoring
  - application health
  - monitoring, logging, and tracing
  - management and updates
  - scaling
  - customization
  - compliance
- workdocs
  - aws's offering to compete with google docs
- x-ray
  - developer tools to figure out if something is broken
- kenesis
  - realtime streaming
  - analytics
    - use sql to process the data from your data stream
  - streams
    - fully managed
    - ingestion service that provides data streams to consumers (e.g. analytic applications)
  - client library
    - process and query streaming
    - used by streams
    - very complex
    - sql interface
  - firehose
    - loading data streams and not creating sql-based applications
- cloud formation
  - infrastructure as code
  - templates written as json or yml
  - automating the provisioning of rsources with these templates
  - handles dependency management
    - i.e. it creates a vpc before it creates a security group
  - stores templates in s3
    - this will charge you
    - you can store these yourself to avoid this tho
  - automatic rollback if errors occur
  - only charged for things created
  - can create templates from existing resources
  - creates resources within "stacks"
    - collection of resources that you can manage as a single unit
  - stack sets
    - enables you to create, update, or delete stacks across multiple accounts and regions in a single operation
  - change sets
    - allow you to preview how proposed changes to a stack might impact your running resources
  - UI provided to create these
- sqs (simple queue service)
  - like rabbitmq
  - pull service
  - created at the region level
- quick sight
  - showing results of data analytics on data lakes like redshift
  - imbedable dashboards
- certificate manager
  - handles ssl certs for you
  - handles cert expiry
  - public and private certs provisioned through aws cert manager for use with acm-integrated services are free
  - you only pay for the aws resources you create to run your app
  - with private cert auth, you pay monthly for the operation of the private ca and for the private certs you use
- outposts
  - bring native aws services, infra, and operating models to virtually any data center, co-location space, or on-premises facility
  - you can use the same apis, same tools, same hardware, and same functionality across on-prem and the cloud
- amazon workspaces
  - fully managed desktop service
- service catalog
  - allows it organizations to createa a portfolio of products that end users can use to deploy resources as defined by the portfolio
  - uses iam and cloud formation
- code deploy
  - self explanatory
- config
  - keep track of config changes on AWS resources
  - keeping multiple date stamped version in a reviewable history
- fargate
  - compute service for containers