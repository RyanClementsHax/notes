# Distributed File Systems

- some can be achieved with special hardware that links storage
  - this is expensive
  - building a DFS on top of commodity hardware, like normal EC2 instances, is much cheaper

## HDFS (Hadoop Distributed File System)

- each node has a daemon that exposes the file system to the network
- no custom hardware
- a coordinator (name node) keeps track of which file blocks are on which machines
- file blocks replicated on many machines
- this has many processing frameworks built on top of it

## Map reduce

- simple framework that takes a map job, orders and pipes the output into the reduce function, then returns that as the final output
  - the final output is saved back into the DFS
  - it is used as input to other map reduce jobs
  - writing to the intermediate files is called materialization
- this abstracts the scale of the file system behind a simple interface
  - map or reduce jobs don't need to know how the data is procured
- map reduce jobs are limited, but shine when they are piped between each other
- the framework will often schedule jobs near the data to reduce network load and increase data locality
- map reduce preserves ordering even though data is processed locally and in small batches
- reducers can become hot spots if they have to perform aggregation on a bunch of rows
- because of the immutable nature, these jobs can be rerun and fault tolerant well
- this framework cannot fundamentally stream data
- some jobs can benefit from stream-like concepts
  - e.g. starting one job with the output of the previous job before the first job finishes
  - higher level frameworks called data flow engines handle the higher level sccheduling and "streaming" that makes map reduce much more efficient
- determinism in the UDFs (user defined functions) helps with fault tolerance
- iterative algorithms like graph algorithms don't map well to map reduce
  - you could run the batch jobs multiple times on the same output files until convergence, but this isn't efficient
  - the pregel model is better for this
    - this is very similar to the actor model
    - state is remembered in the UDFs and messages are passed between them
    - all messages passed in one iteration will be delivered in the next iteration
    - writing the state to durable storage between stages helps with fault tolerance
    - messages can saturate the network though
- the fault tolerant nature of this (i.e. you can rerun any task and produce the same output) creates exactly once semantics
  - i.e. the output appears as if the job was run once

### Uses

- creating search indexes
  - this is how page rankings worked for a bit
  - mappers partition a set of docs
  - reducers aggregate partitions
  - outputs are indexes that are written to a DFS
- machine learning like classification and recommendation systems

### Managing output

- could write output directly to db
  - this introduces extra load on the db
  - doesn't provide atomicity/transactional guarantees
- most systems build db files
  - these can be moved onto the db machines
  - the db can atomically switch between the two files when ready
