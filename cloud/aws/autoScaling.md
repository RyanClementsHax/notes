# Auto Scaling

- adding/removing instances based on demand
- configured via policy
  - schedule
  - load
  - metrics
- cannot scale fully managed services
  - can only vertically scale them if allowed
- az level service
- fleet management
  - balances across AZs
- can replace impaired instances automatically
- free of charge
- launch configuration
  - instance config template that an auto scaling group uses to launch ec2 instances
  - blueprint of the autoscaling group
  - determines config output of each instance
