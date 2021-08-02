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
- resource tags
  - can add to lots of stuff that helps with classification, identification, etc
- storage devices
  - when decommissioned, they are destroyed in accordance with industry-standard practices

## Regions

- distinct geographic area
- made up of 2 or more availability zones
- set up services in multiple regions for disaster recovery

### Availability zones

- identified by the region prefix with an alphabetic character at the end (a-z)
  - what is considered 1a for one account might be totally different for another account (its randomized)
- where resources located
- contains one or more datacenters
- availability zones in same region have low latency between each other

## Edge locations

- CDN cache locations
- where Cloud Front stores its data
- tons of these

### Points of presence

- edge places where AWS can replicate data

## Other services

- lightsail
  - private virtual server
  - for getting started very quickly
  - upgrade to ec2
    - copy existing lightsail instance to ec2
    - can choose from available ec2 pricing options among others
- mechanical turk
  - outsource processes or jobs to a distributed workforce
  - crowdsourcing marketplace
- outposts
  - bring native aws services, infra, and operating models to virtually any data center, co-location space, or on-premises facility
  - you can use the same apis, same tools, same hardware, and same functionality across on-prem and the cloud
  - can restrict access to these products using permissions
- pin point
  - to engage your customers by sending them targeted and transactional email, sms, push notifications, and voice messages
- chime
  - communications service
  - used for online meetings, video conferencing, calls, and chat
- SES (Simple Email Service)
  - cloud based email sending service
  - smtp interface and sdk support
