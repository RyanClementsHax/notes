# Distributed File Systems

- some can be achieved with special hardware that links storage
  - this is expensive
  - building a DFS on top of commodity hardware, like normal EC2 instances, is much cheaper

## HDFS (Hadoop Distributed File System)

- each node has a daemon that exposes the file system to the network
- no custom hardware
- a coordinator (name node) keeps track of which file blocks are on which machines
- file blocks replicated on many machines

## Map reduce

- simple framework that takes a map job, orders and pipes the output into the reduce function, then returns that as the final output
- this abstracts the scale of the file system behind a simple interface
  - map or reduce jobs don't need to know how the data is procured
- map reduce jobs are limited, but shine when they are piped between each other
- the framework will often schedule jobs near the data to reduce network load and increase data locality
