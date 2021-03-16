# Databases

- typically you want your storage on private subnets

## RDS
- relational db service
- not highly available by default unless its aurora
  - it really is just an instance under the hood
- options
  - amazon aurora
    - serverless
  - mysql
  - mariadb
  - prostres
  - oracle
  - mssql
- automating annoying tasks like hardware provisioning and scaling
- fully managed

## DynamoDB
- similar to
  - mongo
  - cassandra
  - oracle nosql
- fully managed

## ElastiCache
- read only
- supports
  - redis
  - memcached
- does not support SQL

## Redshift
- data warehouse
- supports SQL