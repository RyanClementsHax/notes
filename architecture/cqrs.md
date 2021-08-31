# CQRS

- command query responsibility segregation
- one thing it allows for is multiple read views
- it also allows for denormalized read views since the event log is the source of truth

## Keeping readers in sync with writers

- could use transactions
  - within the db
  - or a distributed transaction
- could use total order broadcast
