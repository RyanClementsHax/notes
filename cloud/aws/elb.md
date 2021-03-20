# ELB

- evenly distributes traffic between EC2 instances that are associated with it
- works only across availability zones within the same region
  - preferred you distribute across availability zones, actually, because of the fault tolerance if one availability zone goes down
  - if you need load balancing across regions, visit route 53
- can route traffic away from unhealthy instance
- can specify type of load balancer
- you have to manually set up the AZs its associated with
- determines health from pings
- auto scaling will handle spinning up new instances
- ssl offloading
  - elb can handle tls termination
- can handle routing based on
  - url params (application load balancer)
  - tcp (network load balancer)
    - for speed
    - all of the ec2 instances in the backend can share the same static ip
- also supports routing to lambda functions