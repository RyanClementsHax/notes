# EC2

- provides *logical* computers
- scales
- you need to explicitly assign a public IP to access it from outside of the VPC
- user data
  - optional
  - "bootstraping"
  - a script you want run on instance launch time
- tags
  - labeling
  - cost
  - envrionments
  - searching
  - for whatever you want really
- instance metadata
- need to do work to make it highly available

## AMI (Amazon Machine Image)
- OS
- ids are region specific
- community
  - free to use
  - use at your own risk
- AWS marketplace
  - pay to use
  - typically comes with license
  - created by partners of amazon
- My AMIs
  - AMIs you create yourself
- components
  - root volume template
    - OS
    - application software
  - launch permissions
  - block device mapping
    - EBS
      - recommended
    - instance store
      - only survives reboot
      - host based storage (physically attached)
      - faster
    - priced apart from the instance price
    - depending on the size of instance, can add more

## Instance Type
- processor
- general purpose
- compute optimized
- accelerated computing
- memory optimized
- storage optimized

## EFS (Elastic File Share)
- the aws cloud offering for a file share

## EBS (Elastic Block Storage)
- disk
- only one ebs volume to one ec2 instance
- by default delete on termination
- an option for higher IOPS performance
- root volume created by default
  - deleted when instance terminated by default
- block storage (attachable and detachable)
- can persist across the life of the instance but will delete on termination by default
- network storage (the reason why it can persist across the life of the instance)
- highly available at the AZ level
  - if az goes down, so does your ebs
- snapshots are backed up to s3
  - they are incremental
- workloads
  - hard drive
  - general (self explanitory)
    - solid state
  - provisioned iops
    - great for database instances
    - solid state

## Lifecyle
- stoping an instance means you can restart it
- terminating an instance means you totally delete it

## Purchasing options
- on demand
  - only charged by the hour
- reserved
  - leased for 1 or 3 years
- spot
  - bidding on unused instances
  - by default, persistant bids disabled

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
- stateful
  - if certain traffic allowed inbound, by default, it allows it outbound too
- denies aren't available here, only port open rules
  - this needs to be done at the NACL level
- even though your rules permit some traffic, some might be denied by the NACL