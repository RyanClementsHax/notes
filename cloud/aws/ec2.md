# EC2

- provides *logical* computers
- scales
- you need to explicitly assign a public IP to access it from outside of the VPC

## AMI (Amazon Machine Image)
- OS
- community
  - free to use
- AWS marketplace
  - pay to use
  - typically comes with license
- My AMIs
  - AMIs you create yourself
- components
  - root volume template
    - OS
    - application software
  - launch permissions
  - block device mapping
    - EBS (hard drive mapping)

## Instance Type
- processor
- general purpose
- compute optimized
- accelerated computing
- memory optimized
- storage optimized

## EBS (Elastic Block Storage)
- disk
- an option for higher IOPS performance
- root volume created by default
  - deleted when instance terminated by default
- block storage (attachable and detachable)

## Purchasing options
- on demand
  - only charged by the hour
- reserved
  - leased for 1 or 3 years
- spot
  - bidding on unused instances

## How you will be charged
- purchasing options
- instance type
- EBS optimized
- AMI type
- Data transfer
- Region

## Benefits
- elastic web-scale computing
- full root control of server
- flexible options
- integrates with almost all other AWS services
- extremely reliable
- high level of built in security
- inexpensive
- very easy to start

## Primary use cases
- multi-tier web apps
- web servers
- batch processing
- video processing
- GPU intensive workloads

## Security groups
- all inbound traffic is denied by default
- all outbound traffic is allowed by default
- if certain traffic allowed inbound, by default, it allows it outbound too
- denies aren't available here
  - this needs to be done at the NACL level
- even though your rules permit some traffic, some might be denied by the NACL