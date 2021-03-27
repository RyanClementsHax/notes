# Management

## Workflow services
- quick start
  - ability to use templates for AWS architecture like bash-in-host
  - automates deployments aligned with best practice
  - cloud formation templates are included
  - doesn't create rds instances
- workdocs
  - aws's offering to compete with google docs
- service catalog
  - allows it organizations to create a portfolio of products that end users can use to deploy resources as defined by the portfolio
  - uses iam and cloud formation
  - helps ensure that people are launching services that meet organizational constraints or configuration
  - can create catalogs of products by importing cloud formation templates
  - can be multi-tiered application architectures

## Management services
- license manager
  - differentiating, maintaining third-party software provisioning vendor licenses
  - decreases the risk of license expiry and the penalties
- systems manager
  - unifying resources across regions into one UI
  - can view, automate, and monitor operational tasks
  - makes it possible to 'run command' to ec2 instances with the appropriate iam role
  - isn't configured on an instance
- resource groups
  - collection of aws resources in a single aws region and match the criteria specified by the group's query
  - good for bulk actions
    - applying updates or security patches
    - upgrading applications
    - opening or closing ports to network traffic
    - collecting specific log and monitoring data from your fleet of instances
  - tag based
  - cloud formation stack-based
  - can be nested
  - use cases
    - application with different environments
    - projects managed by multiple departments or individuals
    - set of resources that you use together for a common project or that you want to manage or monitor as a group
    - set of resources related to applications that run on a specific platform such as android or ios
  - permissions are on the account level
  - can use to configure custom consoles
- ram (Resource Access Manager)
  - allows users to share resources with other aws accounts or via aws organizations
- amazon workspaces
  - fully managed desktop service
- cloud directory
  - web based directories to make it easy for your to organize and manage all your application resources such as users, groups, locations, devices, policies, and the rich relationships around them
  - supports hierarchies

## Other services
- cloud9
  - web based ide
  - serverless