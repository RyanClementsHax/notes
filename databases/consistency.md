# Consistency

## Consistency guarantees
- read after write
  - ways to mitigate
    - reading from the leader in cases where you need this and read from the follower in other cases
      - this sort of defeats the point of scaling
    - reading from the leader after a write for a given period of time
    - having the client remember the time stamp of the given read and use that to check replication lag
      - this requires stricter clock synchronization
- monotonic reads
  - ways to mitigate
    - user reads from the same replica
      - e.g. hash requests by user id
- consistent prefix reads
  - this is when any node can see any writes that have causal relationships in the proper order
  - ways to mitigate
    - could force these writes to happen on the same partition
      - not always efficitient
- edge cases to consider
  - what if a user has multiple devices they are accessing the data from?
    - this necessitates that some meta data is stored in a centralized service
  - what if a user's request isn't handled by the same data center?

### Avoiding conflict
- auto incrementing keys make it much harder for replicas to converge and leads to very annoying bugs
- can ensure consistency by having consistent writes go through the same leader
- use synchronous leader replication, but this defeats the point of horizontally scaling

## Edge cases
- data center going down
- user changes location (thus connecting to a different leader)
- quorum writes not being rolled back even though committed on a few nodes

## Resolving conflict
- use a timestamp on the writes and use a last write wins approach
  - this does require some sort of clock sync
  - can alleviate with something like lamport clocks or vector(?) clocks
  - if clocks aren't synced, problems can arise due to clock skew
- use some unique id to break ties
- can assign each replica an arbitrary id and use that to break ties
- some writes can tollerate "merging" the two together
- could bubble up to application level conflict resolution
  - this should be very fast
- use [CRDTs](https://en.wikipedia.org/wiki/Conflict-free_replicated_data_type)
- when using a quorum, you only need the read number to be guaranteed to overlap the write number
- commutative operations
  - these are operations that can be applied in any order like addition and subtraction
  - these operations makes conflict resolution trivial by nature
- version vectors
  - these are vectors that track the version numbers each node is on for a given piece of data
  - these help the node determine causal vs concurrent writes
  - these need extra overhead to handle things like deletes