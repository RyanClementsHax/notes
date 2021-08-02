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
  - this doesn't handle fault tolerance very well
  - this is potentially linearizable
    - it depends on the system being able to know who the leader is
    - failover may ruin linearizability if some data is lost in failover
- leaderless
  - really good for scale
  - provides weaker consistency guarantees
  - probably linearizable
    - race conditions still possible even with quorums even though some people say that a dynamo style db can be
    - it is safe to assume that this isn't linearizable
- multileader
  - avoids single point of failure as in leader-follower topology
  - extra overlead with syncing leaders
  - runs into problems when leaders have high latencies between them

## Lag

- this is when replicas fall behind by some amount of log entries
- this will lead to problems if unchecked
- this should be monitored and alleviated when it arises
- this is easier to do with leader systems, but difficult to do in leaderless systems

## Handling down nodes

- a sloppy quorum is a tradeoff
  - this is when the write is committed even though quorum couldn't be reached

## Leader electon

- can use a lock of sorts, but it must be linearizable
- services like zookeeper and etcd can provide this
