# Databases

- typically you want your storage on private subnets
- basic dbs not HA by default
- when diy
  - os access
  - ability to fine grained customization
  - have to set up your own ec2 instance and stand up ur db on that
- read replicas
  - async
  - have lag
  - good for reporting or other situations that can withstand the lag

## RDS

- relational db service
- not highly available by default unless its aurora
  - it really is just an instance under the hood
- options
  - amazon aurora
    - serverless
    - auto scaling storage capacity
    - auto scaling instances
    - HA: 6 way replication across three AZs
    - mssql or postgresql compatible
    - faster than mssql or postgres
    - continuous backup to s3
    - up to 15 read replicas
    - up to 64 TiB of auto scaling storage
    - can scale up to 128 TiB
    - self healing storage system
  - mysql
  - mariadb
  - prostres
  - oracle
  - mssql
- automating annoying tasks like hardware provisioning and scaling
- fully managed
  - multiple availability zone deployments (failover arch)
    - data replication happens sync
    - active-passive model
    - can't send read requests to secondary
    - backups happen on secondary
    - see-saw when doing updates
  - automatic backups by default
    - last 7 days by default
    - max of 35 days
    - manual snapshots should still be done though
    - can schedule backup window
  - easy to set up, maintain, and manage
  - push-button HA
  - focus on performance
  - managed infra
  - automatic patching
  - encryption at rest
  - multi-AZ deployments
  - automatic host replacement
- lisenced dbs
  - oracle
  - mssql
- open source
  - mysql
  - postgres
  - these "forklift" into aurora
- scales vertically not horizontally
- resizable compute capacity
- considered a "servered" offering
- security groups
  - used to control which IP address ranges can connect to your databases on a db instance
  - when you initially create a db instance, its firewall prevents any database access except through rules specified by an associated security group

## DynamoDB

- similar to
  - mongo
  - cassandra
  - oracle nosql
- fully managed
- acid compliant
- automatically scales to meet required throughput capacity
- provision read throughput
- provision write throughput
- ability to encrypt
- by default eventually consistent
- DAX (DynamoDB Accelerator)
  - in memory cache for DynamoDB
- dynamodb streams
  - time ordered sequence of item level modifications in any dynamodb table and stores this info in a log for 24h
  - applications can access this log and view the data items as they appeared before and after they were modified in near-real time
  - encryption at rest
- global tables
- use cases
  - serverless web applications
  - microservices data store
  - mobile backends
  - ad tech
  - gaming
  - iot

## ElastiCache

- read only
- supports
  - redis
    - can do HA
    - can do backups
  - memcached
    - optimized for speed
- does not support SQL
- fully managed
- scalable

## Redshift

- data warehouse
- supports SQL
- can choose multiple nodes
  - can support leader/worker nodes

## DocumentDB

- mongo db offering
- allows you to use existing mongo db drivers

## Neptune

- graph db
- fully managed
- supports open graph apis for both Gremlin and SPARQL

## Database migration service

- self explanatory
- from any on prem db to any in cloud db
- can consolidate dbs into redshift
- leader/worker nodes
  - leader handles client requests and receives queries
- uses schema conversion tool under the hood for heterogeneous database migrations
