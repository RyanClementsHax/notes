# Billing

## Organizations
- allow you to manage billing and access to multiple aws accounts
- policy based mgmt of accounts via SCPs (Service Control Policies)
- automate the creation of new accounts
- free for all customers at no charge
- best practice is to have root account only be used for billing (no resources deployed in them)
- policies can be applied hierarchically
- linking accounts counts towards scaling discounts

## Pricing model
- pay as you go
- charges stop when you stop using that service
  - except for reserved instances and things like that
- volume discounts
- no long term contracts or complex licensing
- no termination fees
- free tier
  - only for 12 mo
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
  - AMI type
  - region

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
