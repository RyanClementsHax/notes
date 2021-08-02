# Architecture

## Well architected framework

- guide for designing resilient, secure, etc infrastructures
- service characteristics ("ilities")
  - resiliency
    - the ability to recover from and mitigate disruptions
  - durability
    - the ability to remain functional and perform despite unexpected events
- principles
  - perform operations as code
  - annotate documentation
  - make requent, small, reversible changes
  - refine operations procedures frequently
  - anticipate failure
  - learn from all operational failures
- areas of focus
  - operational excellence (focuses on visibility into daily routines and providing continuous business value)
    - responsiveness
    - enactment of operational standards
    - automated processes to champion daily operations
    - perform operations as code
    - make frequent, small, reversible changes
    - refine operations procedures frequently
    - anticipate failure
    - learn from all operational failures
    - running and monitoring systems to deliver business value and to continually improve supporting processes and procedures
  - security
    - implement a strong identity foundation
    - enable traceability
    - apply security at all layers
    - automate security best practices
    - protect data in transit and at rest
    - keep people away from data
    - prepare for security events
  - reliability (focuses on recovering)
    - test recovery procedures
    - automatically recover from failure
    - scale horizontally to increase aggregate system availability
    - stop guessing capacity
    - manage change in automation
  - performance efficiency (focuses on monitoring the performance of a system)
    - democratize advanced technologies
    - go global in minutes
    - use serverless architectures
    - experiment more often
    - apply mechanical sympathy
  - cost optimization
    - adopt a consumption model
    - measure overall efficiency
    - stop spending money on data center operations
    - analyze and attribute expenditure
    - use managed services to reduce cost of ownership
- tool
  - reviews the state of your workloads and compares them to the framework
  - gives you free access to knowledge and best practices used by aws architects, whenever you need it
  - provides a consistent process for you to review and measure your architecture using aws best practices
  - provides recommendations for making your workloads more reliable, secure, efficient, etc

## Disaster recovery

- need to understand business objectives
  - RTO (Recovery Time Objectives)
    - how long the business can tolerate waiting for recovery
  - RPO (Recovery Point Objectives)
    - to what point should the business recover to
- methods
  - backup & restore
    - RPO / RTO: hours
    - lower priority use cases
    - restore data after event
    - deploy resources after event
    - cost: $
  - pilot light
    - RPO / RTO: 10s of mins
    - less stringent RTO and RPO
    - core services
    - start and scale resources after
    - cost: $$
  - warm standby
    - RPO / RTO: minutes
    - more stringent RTO and RPO
    - business critial services
    - scale resources after event
    - cost: $$$
  - multi-site active/active
    - zero downtime
    - near zero loss
    - mission critical services
    - cost: $$$$

## SQS (Simple Queue Service)

- like rabbitmq
- pull service
- created at the region level

## Debugging

- x-ray
  - developer tools to figure out if something is broken
  - good for microservices
  - end to end view of requests as they travel through an application
  - map of application's underlying components
  - helps improve application performance by helping you find performance bottlenecks
