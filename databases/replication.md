# Replication

## Semi synchronous replication
- this is where one leader sends the writes to all of the followers, but doesn't send a success back to the client until at least one of the followers acknowledges
- this is poorly named because the commit on the leader doesn't wait for the commit to happen on one of the followers before committing on itself, it only waits to send the success back to the client until one of the followers recognizes the commit as well
- it is still possible for data to get out of sync if the leader goes down without sending the write to the followers

## Replication stategies
- sending the queries to replicas for them to perform on their own will run into problems if the query uses non-deterministic features like getting the current time or if the query order matters
  - you can mitigate this by precomputing those non-deterministic functions, but you run into edge cases quickly
  - you could also not allow this for queries that use them and fallback to other strategies
- another solution is to send the products of the queries to the replicas, but this can run into problems if the write was particularly large or if the nodes are on different versions (and thus implement serialization differently)
- row based replication tends to work the best because it is a logical way to represent the data which allows for version mismatches and avoids managing non-determinism

## Application level replication
- this is necessary for a few use cases
  - partial replication
  - replication to a different kind of db
  - handling conflict resolution
- this is commonly done as "trigger based" replication
  - this is where sprocs and triggers handle the replication
  - this tends to be more prone to bugs and limitations
  - of course this imposes more overhead

## Replication topologies
- leader-follower
  - really good for low write and high read scenarios
  - this runs into problems when the followers are dispersed geographically because it essentially necessitates async replication

## Consistency
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
- causually related writes (i think also called consistent prefix reads)
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

## Lag
- this is when replicas fall behind by some amount of log entries
- this will lead to problems if unchecked
- this should be monitored and alleviated when it arises
- this is easier to do with leader systems, but difficult to do in leaderless systems

## Handling down nodes
- a sloppy quorum is a tradeoff
  - this is when the write is committed even though quorum couldn't be reached