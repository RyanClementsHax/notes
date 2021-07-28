# Partitioning

- this often plays in hand with replication, but is independent from replication scheme
- this is when data is split using some method between nodes
- often partitions are replicated on many nodes for fault tolerance
  - those partitions would then follow some replication scheme

## Replication topologies
- partitions typically follow a leader-followers replication scheme where one node is the leader for a given partition or set of paritions and other nodes are followers
  - one node is not the leader for ALL parititions, only a subset
  - this way, leader resposibilities can be spread across many nodes
- if paritions aren't even then this is called *skewed*
- if one node receives more load because it is the leader for some "hot" paritions, then it is called a *hot spot*
  - it is impossible to avoid all situations that create hot spots
  - e.g. a celebrity might suddenly create a hot spot if they post something that receives a lot of writes
  - this can be mitigated in the application layer by adding randomness, but this leads to more book keeping, more reads to read any data written, and makes things like range queries harder

## Assignment
- if paritioning is used, the question is, "how do you assign parititions to nodes in a way that allows for scale?"
- this doesn't always have to be even
  - if nodes have different hardware, it could be advantageous to have more partitions on the node running on faster hardware

### Random assignment
- this is when parititions are randomly assigned to nodes
- this causes problems when reading and writing since clients don't know which node to query so you have to query all of them
- this sort of defeats the purpose of partitioning

### Timestamps
- this is when partition keys are timestamps
- this naturally leads to hot spots
  - e.g. the data produced during a given day would only go to one node while other nodes remain idle
- to mitigate hot spots, prefixing the timestamp with some application specific value works well
  - e.g. prefix the timestamp with the IOT sensor name that is producing the data

### Consistent hashing
- this is when a key or set of keys are hashed and then that hash serves as the partition key
- this leads to better spread of data across nodes
- the hash function doesn't need to be strong for cryptography (ex: mongo uses MD5)
- not all hash funcs are good
  - e.g. Java's Object.hashCode can create a different hash for different processes it is called within
- this doesn't work well with range queries since the data is no longer ordered on one node since
- compound keys are good to alleviate this problem
  - the first key is hashed and serves as the partition key
  - then the other keys can easily have range queries performed on them
  - this lends itself to one-to-many relationships well

### hash mod n
- this is when a hash is taken, then mod'ed by the number of nodes to do assignment
- avoid this ALWAYS
- this causes major problems when trying to rebalance paritions since you need to recalculate this function for EVERYTHING which subsequently results in so much traffic being sent between nodes to move the data around

## Secondary indexes
- this is difficult

### Local indexes
- also known as document partition indexes
- this is when indexes are kept on each node for only the data it has
- this leads to the scatter gather pattern where in order to query against these indexes, you need to query each one of these nodes and aggregate their responses since each node has an incomplete index
- this is also more prone to suffer from tail latency where one slow response slows the whole thing down

### Global indexes
- this is when the index is also partitioned over nodes
- writes are more complex since it has more partitions and nodes to write to
- this of course also runs into consistency problems

## Rebalancing
- this is when paritions are reassigned or modified across the nodes that have them
- this will take resources to do this and will slow down your system
- partition splitting is when a partition is split between nodes to handle rebalancing
  - this is not always favorable

### Fixed partitions
- this is when the parititon assignments are decided up front
- this runs into expensive rebalancing and partition splitting
- most people mitigate by creating more parititons than nodes
  - it should be high enough to account for future growth
  - this number is a sweet spot
    - too high and you run into large overhead
    - too low and you run into rebalancing problems
- the parition size is proportional to the data size

### Dynamic partitions
- this is when partitions rebalance themselves based on the size of partitions
- this alleviates the problem of having to come up with just the right number up front
- if not configured, all partitions will start of on one node which will lead to all the traffic going to that node
  - when configured, you have to know something about the partition distribution upfront to set this number however
- this works well with key ranges and hash assignments
- the number of partitions created is proportional to the size of the data

### Fixed number of partitions per node
- this is like dynamic in that it scales with the data, but is configured upfront to limit the number of partitions on a given node
- this sometimes leads to unfair splits, but it averages over many nodes

### Automatic vs manual rebalancing
- when rebalancing happens, sometimes you want it to be automatic and sometimes you want human intervention

#### Automatic
- this works well with auto remediation tools
- this can be unpredictable
- can be predictable

#### Manual
- this works well when automatic rebalancing is too complicated, or too dangerous to do
- some tools create automated rebalancing plans that are then given to humans for approval

## Service discovery
- when partitioning happens, clients need to be able to find the right node to talk to in order to query against the right data
- this becomes more difficult when the partitions change over time

### Query all nodes
- a client could send the query to all nodes in order to find the right one
- this leads to a lot of load though

### Forwarding requests
- in this way, a client would connect to any node, then the node would forward the request to the node that has the right data if it doesn't have it

### Using the routing tier
- a load balancer aware of the partitions or partitioning scheme could forward requests from clients to the proper node

### Client aware discovery
- the logic to query the right node could be bundled on the client side
- this isn't as clean since clients now have to be aware of the way the database is set up

### Consensus
- in all service discovery strategies, the need for consensus is present
- all interested parties need to agree on what nodes have what partitions
- this needs to react to changes in nodes and partition assignments

#### External services
- services like zookeeper can keep track of all this meta data that all interested parties can subscribe to
- this requires a dependence on another service though

#### Consensus protocols
- some implementations use protocols like the gossip protocol to spread this information around
- avoids a dependence on another service
- this makes the clients more complex however

#### Avoiding rebalancing
- having statically assigned partitions avoids the need to manage changes in partitions which keeps service discovery simple