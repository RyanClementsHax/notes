# Billing

## Organizations
- allow you to manage billing and access to multiple aws accounts
- policy based mgmt of accounts via SCPs (Service Control Policies)
- automate the creation of new accounts
- free for all customers at no charge
- best practice is to have root account only be used for billing (no resources deployed in them)
- policies can be applied hierarchically
  - can create policy that enforces the use of tags
- linking accounts counts towards scaling discounts
- can create new accounts
- can even do that with api calls

## Pricing model
- pay as you go
- charges stop when you stop using that service
  - except for reserved instances and things like that
- automatic volume discounts
- no long term contracts or complex licensing
- no termination fees
- free tier
  - only for 12 mo (with limits)
    - ec2
    - s3
    - rds
    - cloud front
- some services are alway free
  - trusted advisor
  - iam
  - vpc
- s3
  - how much data you store
  - charged per GB stored
  - price varies per region and storage class
  - request pricing
  - lifecycle transition request
  - data retrieval, archive and restore
- ec2
  - billed per second of compute (unless reserved)
  - purchasing option
  - instance type
    - reserved instances
      - can reserve from 1 or 3 years
      - the more you put up front, the lower monthly bill
      - options
        - NURI (no up-front)
        - PURI (partial up-front)
        - AURI (all up-front)
  - AMI type
  - region
  - ip addressing
    - when you have an elastic ip that isn't being used
- data
  - wont be changed when moving data into the cloud
  - wont be charged when moving data between AZs
  - will be charged when moving data between regions
- savings plans
  - services
    - ec2
    - lambda
    - fargate
  - exchange for a commitment to a consistent amount of usage (measured in $/hour) for a 1 or 3 year term
  - up to 72% savings on compute
  - regardless of instance family, os, tenancy, region
  - use cases
    - workloads with a consistent and steady state usage
    - customers who want to use a different instance types and compute solutions across different locations
    - customers who can make monetary commitment to use ec2 over a one-or three-year term

## TCO (Total Cost of Ownership) Calculator
- free tool for calculating cost and comparing with other options

## AWS Simple Calculator
- being replaced by AWS Pricing Calculator
- estimates cost based on a scenario
- can provide a per-service breakdown of cost
- can help identify most costly scenarios

## Cost Explorer
- free tool that allows you to view charts of your costs
- forecasts what you will use over the next 13mo including the current month

## AWS Budgets
- give the user the status of user-set budgets and provide forecasts of estimated costs
- does not give graphical representation of data

## AWS Reports
- gives a composite view of costs and usage
- gives a granular perspective of usage and billing without usage-based forecasts
