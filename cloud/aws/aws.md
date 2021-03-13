# AWS

- [hands on tutorials](https://aws.amazon.com/getting-started/hands-on/)
- create an account with your work email to help establish account ownership if things go wrong

## Regions
- distinct geographic area
- made up of 2 or more availability zones

### Availability zones
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
  - allows direct corporate connection to the cloud
- route 53
  - DNS service
- virtual private cloud

## Compute
- EC2
- Lambda

## Storage
- S3
- Glacier

## Other services
- direct connect
  - ability to connect on-prem servers into the VPC via a VPN
- quick start
  - ability to use templates for AWS architecture like bash-in-host
- aws analytics
  - many services
  - athena
    - analyze data in S3 using SQL
    - serverless
    - pay per query
    - no need to set up ETL
    - can be used to query log files stored in s3
    - can be used to generate business reports on data stored in s3
    - analyze aws cost and usage reports
    - run queries on click-stream data
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