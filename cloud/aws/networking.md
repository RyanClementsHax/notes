# Networking

## VPC (Virtual Private Cloud)
- does not span over region
- default vpc created when account created
- spans all availability zones in the region
- subnets
  - subnets that come with default vpc are public
  - exist in availability zones
  - cannot span availability zones
  - public/private subnets
    - determined by routing table entries
    - if there exists entries that point a subnet to the IGW, it can reach the internet
    - need to explicitly associate routing tables with subnets (except default routing table which lets everyone talk to everyone within a VPC)
  - one subnet per availability zone is created with an account
- peering
  - networking connection between two VPCs that enables you to route traffic between them using private IPv4 addresses or IPv6 addresses
  - instances in either VPC can communicate with each other as if they are within the same network
  - encrypts traffic by default
  - don't have to be in the same account
  - don't have to be in the same region
  - can't have matching or overlapping cidr blocks
  - doesn't support transitive connections
  - don't have to set up single points of failure
  - use cases
    - sharing resources
    - one vpc has centralized access to some resource needed in other vpc's
    - replicating data to geographically distinct locations for fault-tolerance, disaster recovery and redundancy
- flow logs
  - only capture ip traffic-related info passing through and from network interfaces within VPC
- endpoints
  - enables private connections between your vpc and supported aws services and vpc endpoint services powered by aws private link
  - traffic doesn't leave the aws network
  - does not require an internet gateway, virtual private gateway, or NAT
  - interface endpoints
    - elastic network interface with a private ip address from the ip address range of your subnet

## IGWs (Internet Gateways)
- allow instances in VPC to communicate with internet
- highly available
- no bandwidth constraints
- default VPC already has IGW attached
- only one IGW can be attached to a single VPC at a time
- can configure routing table
- detaching an IGW from a VPC won't change the routing tables that point to it
  - so even though the routing tables will still point to the IGW, there won't be any internet access because it is detached

## Networking security
- NACL (Network Access Control List)
  - associate with subnets
  - firewall on the subnet level
  - inbound and outbound rules
  - can configure traffic between subnets
  - rules have order of processing
  - stateless
    - need to explicitly allow both inbound and outbound traffic for request response traffic
    - so inbound you prob want to expose port 80 for http, but outbound you want to expose 1024-65535 for the ephemeral ports created from the web server
  - default has everything allowed by default
- SG (Security Group)
  - firewall on the instance/serve level