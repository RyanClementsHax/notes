# Networking

## VPC (Virtual Private Cloud)
- does not span over region
- default vpc created when account created
- spans all availability zones in the region
- subnets
  - exist in availability zones
  - cannot span availability zones
  - public/private subnets
    - determined by routing table entries
    - if there exists entries that point a subnet to the IGW, it can reach the internet
    - need to explicitly associate routing tables with subnets (except default routing table which lets everyone talk to everyone within a VPC)
  - one subnet per availability zone is created with an account

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