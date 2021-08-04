# Messaging

## Prefetch

- messaging libraries like RabbitMQ have a prefetch option that will configure how many messages it grabs
- if your prefetch is too big, the messages you acquire if processing them brings in large files can lead to out of memory (OOM) errors

## High performance queues

- if you can tolerate message loss, using redis as a queue can function well (bbc.com uses this pattern at a massive scale)

## Limitations

- [event sourcing: why using a message broker is a bad idea](https://diogojoma.medium.com/event-sourcing-why-using-a-message-broker-is-a-bad-idea-ddc11089c876?)
  - can run into consistency issues when you commit data to a db in a transaction, then add a message onto a bus as a separate operation
  - if the message dispatch fails, you have problems
  - its easier to use the transactional outbox pattern and event stores to implement this type of functionality

## Acknowledgements

- guaranteeing delivery requires atomic commit protocols

## Log based messaging systems

- these keep a log of all of the messages in an append only log
- these are good for preserving order and ensuring some durability on messages
- if clients keep track of their place in this log (i.e. offset) the broker is simplified
- keeping old messages is good for debugging
  - e.g. a message can be replayed on a local system for debugging without disturbing a prod cluster

## Order

- messages may be processed out of order even if the broker preservers order
- using a single consumer will preserve order but this can cause head of line blocking
- log based messaging systems work well for resiliency and ordering since they preserve messages as entries in an append only log

## Consistency with other systems

- sometimes you want to update an index, cache, or db alongside publishing a message
- to avoid the overhead of integrating various message consumers to listen for the message, you can use dual writes in applications
  - problem 1: race conditions can happen if there are multiple systems making these calls since the disparate systems don't share a single source of truth
    - could use something like version vectors to mitigate
  - problem 2: how do you ensure some transactional consistency if something fails?
    - need some atomic commit guarantee
- CDC (Change Data Capture) solves this problem by making the db the source of truth where other systems "subscribe" to the feed of changes produced by writes to the db
  - many systems read the db's raw log files to produce a stream of events
  - log based messaging systems are good to put this into
  - many dbs provide higher level apis for this stream to avoid coupling to the raw files
  - log compaction helps with the sheer volume of these changes
  - replication lag still applies
