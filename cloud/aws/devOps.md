# Dev Ops

## Opsworks
- config mgmt service
- helps you configure and operate applications using Chef and Puppet
- has concept of layers that form a stack
- when using for servers in customer data centers
  - the servers should have linux and windows operating systems
  - the servers should have access to aws public endpoints
- automates the configuration of ec2 instances including replication
- can be used for ec2 or on prem
- any instances created by opsworks that are manually terminated will be automatically restarted as part of opsworks self-healing feature
  - this can lead to unexpected prices
  - if you need to terminate an instance, you must use opsworks to terminate that instance

## Code suite

### Codestar
- unified user interface
- enabling you to manage your software development activities in one place easily
- set up entire CD toolchain in minutes
- secure

### Codepipeline
- managed service for automation of delivery pipeline for application updates
- uses codecommit, codebuild, and codedeploy under the hood
- cannot alone provision IT infrastructure
- [single tenant pipelines](https://aws.amazon.com/blogs/devops/cross-account-ci-cd-pipeline-single-tenant-saas/)
- integrating with jenkins
  - [here](https://docs.aws.amazon.com/codepipeline/latest/userguide/tutorials-four-stage-pipeline.html?icmpid=docs_acp_console) is a tutorial on how to set this up
- [creating custom actions](https://docs.aws.amazon.com/codepipeline/latest/userguide/actions-create-custom-action.html)

### Codebuild
- managed service for pipeline tasks
- used by codepipeline for tasks
- building docker images
  - doesn't do a good job of caching docker images
    - to get around this [you have to create an ECR repository as the cache](https://aws.amazon.com/blogs/devops/reducing-docker-image-build-time-on-aws-codebuild-using-an-external-cache/)
  - if you run the task without specifying your own vpc and you don't use docker with authentication, you will run into throttling errors even if you don't reach anywhere near the throttle because codebuild's default vpc config will have tons of other people's build tasks in it which will all look like they are coming from the same IP which is what docker uses to throttle anonymous pulls
    - to get around this, [you either need to create an account and use it, use ECR as your repository, or throw the code build task in your own vpc](https://cloudkatha.com/too-many-requests-you-reached-pull-rate-limit/#:~:text=Reddit%20Pinterest%20WhatsApp-,AWS%20CodeBuild%3A%20toomanyrequests%3A%20You%20have%20reached%20your%20pull%20rate%20limit,Docker%20Hub%20went%20into%20effect.&text=Free%20Docker%20Hub%20users%20are,pull%20requests%20per%20six%20hours.)

### Codedeploy
  - self explanatory
  - automate installation of applications to hosts, ec2 instances, lambda, or on prem servers
  - uses targets like ec2, s3, and beanstalk for deploying generated artifacts

## Cloud formation
  - infrastructure as code
  - templates written as json or yml
  - automating the provisioning of rsources with these templates
  - handles dependency management
    - i.e. it creates a vpc before it creates a security group
  - stores templates in s3
    - this will charge you
    - you can store these yourself to avoid this tho
  - automatic rollback if errors occur
  - only charged for things created
  - can create templates from existing resources
  - creates resources within "stacks"
    - collection of resources that you can manage as a single unit
  - stack sets
    - enables you to create, update, or delete stacks across multiple accounts and regions in a single operation
  - change sets
    - allow you to preview how proposed changes to a stack might impact your running resources
  - drift protection
    - detect any changes you made to resources outside of cloud formation templates
  - UI provided to create these
  - automates deployments of aws resources, but not applications and code onto hosts
    - not to on prem tho