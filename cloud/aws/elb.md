# ELB

- evenly distributes traffic between EC2 instances that are associated with it
- works across availability zones
  - preferred you distribute across availability zones, actually, because of the fault tolerance if one availability zone goes down
- can route traffic away from unhealthy instance
- can specify type of load balancer