# EC2

- provides *logical* computers
- scales
- ip addresses
  - public
    - dynamic
      - can only assign this at launch time
    - static (elastic)
      - can have one elastic ip address associated with a running instance at no charge
      - can have up to 5 elastic ips
      - the elastic part refers to being able to associate it with any instance
      - "cheap failover"
  - you need to explicitly assign a public IP to access it from outside of the VPC
- user data
  - optional
  - "bootstraping"
  - a script you want run on instance launch time
  - cannot modify once created
  - need to use `sudo` in the script to run as the root user
- tags
  - labeling
  - cost
  - envrionments
  - searching
  - iam
  - for whatever you want really
  - case sensitive
  - can use tag editor to edit
- instance metadata
  - is available to ec2 instances by default without the need to provide an iam role for accessing it
  - can be edited after launch
- need to do work to make it highly available
- can turn on termination protection
  - disables terminating an instance
  - can disable this manually when you actually want to terminate an instance
  - off by default
- shared tenancy
  - this is the default
  - run on host potentially with other instances
  - cannot guarantee that the instance will be on the same host when restart (but you can on reboot)
  - this is why instance stores cannot survive reboot
- dedicated instance
  - you have a host and no other customers are on that host
  - but when you stop, you could start on a totally different host (that again has no other customers on it)
- dedicated host
  - you will always be the only one to have this host regardless of restarts
  - good for licenses that require this
- ec2 image builder
  - fully managed
  - helps build secure images of operating systems for use on aws
- compute optimizer
  - ML based tool that analyzes metrics of historical utilization
  - makes recommendations of compute services to be used for the workload

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
  - protects customers by performing periodic security checks on listed products
  - flexible pricing options
- My AMIs
  - AMIs you create yourself
- components
  - region
  - root volume template
    - OS
    - application software
  - architecture
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
- can copy ami into another region to use it there

## Instance Type

- processor
- general purpose
- compute optimized
- accelerated computing
- memory optimized
- storage optimized
- organized into families (FIGHT DR MC'PXZ AU)
  - F: FPGA
  - I: IOPS
  - G: Graphics
  - H: High disk throughput
  - T: cheap general purpose (think T2 micro)
  - D: density
  - R: RAM
  - M: main choice for general purpose apps
  - C: for compute
  - P: Graphics (think pics)
  - X: extreme memory
  - Z: extreme memory AND cpu
  - A: arm based workloads
  - U: bare metal

## Networking

- ENI (Elastic Network Interface)
  - virtual network card for ec2 instances
  - it allows
    - a primary private IPv4 addr from the IPv4 addr range of your vpc
    - one or more secondary private IPv4 addresses from the IPv4 address range of your vpc
    - one elastic IP address (IPv4) per private IPv4 address
    - one public IPv4 address
    - one or more IPv6 addresses
    - one or more security groups
    - a MAC address
    - a src/dest check flag
    - a description
  - scenarios
    - create a mgmt network
    - use network and security appliances in your vpc
    - create dual-homed instances with norkloads/roles on distinct subnets
      - like one for logging and another for production traffic
    - create a low-budget, high-availability solution
  - cannot detach the primary ENI of one EC2 instance and connect it to another instance for moving its elastic IP
  - can configure a security group for the ENI and restrict traffic to the ec2 instance
  - can detach a secondary ENI containing a private ip from one ec2 instance and attach it to another
  - can attach an elastic ip to an ec2 instance in another subnet by releasing it from the ENI in the current subnet to which it is currently attached to
- EN (Enhanced Networking)
  - single root I/O virtualization (SR-IOV) to provide high-performance networking capabilities on supported instance types
  - SR-IOV is a method of device virtualization that provides higher I/O performance and lower CPU utilization when compared to traditional virtualized network interfaces
  - enhanced networking provides higher bandwidth, higher packet per second (PPS) performance, and consistently lower inter-instance latencies
  - there is no additional charge for using enhanced networking
  - use where you want good network performance
  - can be enabled using
    - ENA (Elastic Network Adapter)
      - supports network speeds of up to 100 Gbps for supported instance types
    - Intel 825999 VF (Virtual Function) interface
      - supports network speeds of up to 10 Gbps for supported instance types
      - typically used on older instances
    - probably want to choose ENA over the VF option in every circumstance
- EFA (Elastic Fabric Adapter)
  - network device that you can attach to you  ec2 instance to accelerate HPC (High Performance Computing) and machine learning applications
  - lower and more consistent latency and higher throughput than the TCP transport used in cloud-based HPC systems
  - can use OS-bypass
    - enables HPC and machine learning applications to bypass the OS kernel and communicate directly with the EFA device
    - makes it a lot faster with a lot lower latency
    - not supported with windows, only linux

## EFS (Elastic File Share)

- the aws cloud offering for a file share
- petabyte scale

## EBS (Elastic Block Storage)

- disk
- in same AZ as instance
- only one ebs volume to one ec2 instance
- block storage (attachable and detachable)
- can persist across the life of the instance but will delete on termination by default
- network storage (the reason why it can persist across the life of the instance)
- highly available at the AZ level
  - replicated within AZ
  - if az goes down, so does your ebs
- snapshots are backed up to s3
  - they are incremental
- root volume created by default
  - deleted when instance terminated by default
  - where os installed
  - can encrypt these
  - can be one of these
    - general purpose
    - provisioned iops
    - magnetic (standard)
  - encrypting
    - used to be very laborsome to create an encrypted root device snapshot
    - simple option when adding a volume on an ec2 instance at start time
    - if encrypting a root volume after an instance was started
      - create a snapshot
      - copy that snapshot and check the encypt check box in that process
      - create an ami from that snapshot
      - launch a new instance from that ami
      - note: cannot create an instance with an unencrypted root volume from an ami with an encrypted root volume
    - snapshots of encrypted volumes are encrypted automatically
    - volumes restored from encrypted snapshots are encrypted automatically
    - can only share snapshots if unencrypted
    - these shapshots can be shared with other aws accounts or made public
- additional volumes can be added on
  - these won't be deleted by default
  - general (self explanitory) (gp2)
    - solid state
    - balance price/performance for most workloads
    - 16k iops
    - 1 GiB - 16 TiB
  - provisioned iops (io1)
    - great for database instances
    - solid state
    - 64k iops
    - 4 GiB - 16 TiB
  - cold HDD (sc1)
    - low cost HDD volume for less frequently accessed workloads
    - file servers
    - 250 iopps
    - 500 GiB - 16 TiB
  - throughput optimized HDD (st1)
    - low cost HDD volume designed for frequently accessed, throughput intensive workloads
    - big data and data warehouses
    - 500 iops
    - 500 GiB - 16 TiB
  - magnetic (standard)
    - previous generation HDD
    - workloads where data is infrequently accessed
    - 1 GiB - 1 TiB
    - 40 - 200 iops
- modifying volumes in run time
  - can modify size and storage type
  - might require you to nudge the os by running some esoteric bash command to get the os to realize something changed
  - may take some time for performance changes to take full effect
- moving a volume to a different AZ
  - using snapshots
    - take a snapshot
    - create an image from it
      - need to choose virtualization
        - PV (Paravirtual)
        - HVM (hardware virtual): uses hardware-assist technology provided by the aws platform where the guest vm runs as if it were on a native harware platform except that it uses PV network and storage drivers for improved performance
          - default and more versitile
        - some instance types support both, while others support only one
    - launch an instance from that image in a new AZ
  - moving an image
    - create an ami from the snapshot
    - copy the ami into a different region
- best practice to stop instance before taking snapshot, but can take a snapshot while the instance is running
- stopping an instance preserves its EBS volume
- terminating an instance removes its volumes
  - good to prevent continued charging for ebs volumes associated with an instance you dont use

## Instance store

- ephemeral
- not supported by all instance types
- cannot attach additional instance store volumes after creating the instance
- cannot be stopped
- if underlying host fails, you lose all of your data
- by default root volume deleted on termination

## Lifecyle

- stoping an instance means you can restart it
- terminating an instance means you totally delete it

## Purchasing options

- on demand
  - charged by the second at the hourly rate
  - use cases
    - users who prefer the low cost and flexibility of ec2 without upfront payment or long-term commitments
    - applications that have spikes in workload that cannot be interrupted
- reserved
  - leased for 1 or 3 years
  - standard
    - up to 75% off of on demand instances
    - the more you pay up front and the longer contract, the greater the discount
  - convertable
    - up to 54% off of on demand instances
    - can change the attributes of the RI as long as the exchange results in the creation of reserved instances of greater or equal value
  - scheduled
    - available to launch within the time windows to reserve
  - within an account or accounts that have access to these instances, its first come first serve
  - can't always guarantee capacity
  - can extend boundaries across AZs
  - can sell unused reserved instances on the marketplace
- dedicated hosts
  - phyisical ec2 server dedicated for your use
  - allows you to use your existing server-bound software licenses
  - useful for
    - regulatory requirements that may not support multi-tenant virtualization
    - great for licensing which does not support multi-tenancy or cloud deployments
    - can be purchased on demand
- spot
  - bidding on unused instances
  - up to 90% discount
  - by default, persistant bids disabled
  - if spot instance terminated by aws, you don't pay for that full hour
  - if you terminate that instance, you pay for that full hour
  - use cases
    - flexible start and end times
    - applications only feasible at very low compute prices
    - users with fault-tolerant and/or stateless workloads
    - big data and analytics
    - containerized workloads
    - CI/CD and testing
    - web services
    - image and media rendering
    - HPC
  - not good for
    - persistent workloads
    - critical jobs
    - databases
  - spot blocks
    - can stop your spot instances from stopping even if the spot price goes over your max spot price
    - you can set this for between 1 to 6h currently
  - spot requests
    - maximum price
    - desited number of instances
    - launch specification
    - request type
      - one-time
        - doesn't try to restart after you lose your instance
      - presistent
        - restarts once the spot price is <= your max price
    - valid from
    - valid until
    - there are state charts for this
  - spot fleets
    - collection of spot instances and optionally on-demand instances
    - attempts to launch the number of spot instances and on-demand instances to meet the target capacity you specified in the spot fleet request
    - the request for spot instances is fulfilled if there is available capacity and the maximum price you specified in the request exceeds the surrent spot price
    - the spot fleet also attempts to maintain its target capacity fleet if your spot instances are interrupted
    - set up different launch pools
    - can have multiple launch pools
    - will stop launching instances once you reach your price threshold or capacity desire
    - strategies
      - lowest price (default)
        - spot instances come from the pool with the lowest price
      - capacityOptimized
        - spot instances come from the pool with optimal capacity for the number of instances launching
      - diversified
        - spot instances are distributed across all pools
      - instancePoolsToUseCount
        - spot instances are distributed across the number of spot instance pools you specified
        - this parameter in valid only when used in combination with lowestPrice

## Hibernate

- ec2 startup process
  - os boots
  - bootstrap scripts run
  - application loads
- os is told to perform hibernate
- saves contents from the instance memory to ebs root volume
- then persist the root volume and any attached ebs volumes
- circumvents startup process
- previously attached data volumes are reattached and instance retains its instance id
- available for
  - windows
  - linux 2 ami
  - ubuntu
  - on-demand instances
  - reserved instances
- good for
  - long-running processes
  - services that take time to initialize
- limitations
  - instance ram must be less than 150 GB
  - can't be hibernated for more than 60 days

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
  - also can't deny any IP ranges
  - this needs to be done at the NACL level
- even though your rules permit some traffic, some might be denied by the NACL
- any changes take effect IMMEDIATELY
- have a many-to-many relationship with instances
- rdp port: 3389
- no charges associated with these
- can change security groups even when instances running
