# Transactions

## [OLAP vs OLTP](https://techdifferences.com/difference-between-oltp-and-olap.html#:~:text=OLTP%20and%20OLAP%20both%20are,is%20an%20analytical%20processing%20system.&text=The%20basic%20difference%20between%20OLTP,online%20database%20query%20answering%20system.)

- tldr: OLTP and OLAP both are the online processing systems but OLTP is a transactional processing while OLAP is an analytical processing system

### OLAP

- these queries can seriously affect performance of prod OLTP dbs and affect end users so you probably want a separate OLAP db

## ACID

### Acidity

- this doesnt describe any concurrency properties (that is the property of Isolation)
- this only guarantees that transactions will never leave half finished results
  - i.e. the transaction can be safely cancelled and retried

### Consistency

- this means so many things that its hard to pin down
- in this context, it mostly means that invariants within the data are preserved or have some guarantee of being preserved
- dbs can expose constraints like data types and foreign keys, but it is up to the application developer to use these tools to make sure all of the desired invariants hold

### Isolation

- this ensures that even though writes are parallel or concurrent, it appears as if they were sequential
- there are varying levels of isolation which provide different strengths of this

#### Isolation levels

- read committed
  - reads only read committed writes (no dirty reads)
  - writing only overwrites data tthat has been committed (no dirty writes)
  - still doesn't prevent certain race conditions like two transactions trying to increment a number
  - often implemented by having write locks on rows (don't want locks for reads because that would slow down the system)
  - for reads, the db remembers the old value before the transaction
  - this can lead to read skew
  - this level of isolation is fine for some applications but intolerable for others like analytics and backups
- snapshot
  - this is where each transaction only sees a snapshot of the db at a given time
  - makes reading happen from a consistent snapshot to avoid lock contention between readers and writers
  - good for analytics and backups
  - also use write locks
  - uses MVCC (Multi Version Concurrency Control) to handle multiple transactions at the same time
    - this is where each transaction has its own version of a piece of data
    - indexes also can use MVCC
  - still doesn't provide atomic writes
    - some dbs provide atomic write abilities to prevent transactions clobbering each other's writes but its up to the user to use them (this can run into problems if your ORM doesn't make use of these)
    - application locks can be used to ensure atomic writes are performed (but obviously not ideal)
    - some dbs provide automatic detection for clobbering
    - if replication is used, you will likely need to have some guarantee provided by the way you partitioned the data to avoid this
    - else you will need to resort to application level solutions
    - some dbs have the concept of constraints that get run to assert on state before the transaction is committed
  - serial snapshot isolation is fundamentally not linearizable
    - the whole point is that the snapshot doesn't include writes more recent than the snapshot
- linearilability
  - only has a recency guarantee and doesn't prevent write skew unless you do something like materializing conflicts
  - this will make the system appear as if there is only one copy
  - writes are atomic
  - this also ensures "read your writes" consistency on all replicas
  - this is a "recency" guarantee (i.e. you will always read the recently most written value)
  - in overlapping reads during a write, some reads may see new value and some may see old but when a read sees the new value all other reads must see the new value too
  - this can be used along side serializability (i.e. strict serializability)
  - serial transaction ordering and two phase locking are usually linearizable
  - uniqueness constraints need linearizability because you need to be sure no other write is concurrently claiming the resource
  - provides total order guarantee
- serializability
  - avoids many race conditions
  - guarantees that the system behaves as if the transactions happened in some serial order
  - this may reorder transactions
  - this can be used along side linearizability (i.e. strict serializability)
  - solved in a few ways
    - literally serializing the transactions
    - two phase lock
    - optimistic concurrency control
  - literally serializing the transactions
    - these generally are run on one thread to ensure ordering
    - these need to be very quick and take little memory for them to be performant
    - these, although seemingly a bottleneck, can work well at scale because OLTP tends to have very short transactions whereas OLAP pretty much doesn't need this
  - two phase lock
    - this is not two phase commit
    - this is where a transaction has a round of aquiring the locks and reading/writing in the first phase, then releasing in the second phase
    - this can lead to dead lock
      - dbs will normally arbitrarily kill one transaction in order to ensure progress
    - major performance downside because
      - locking overhead
      - reduced concurrency
    - one slow transaction can be severe for the whole system
    - lots of wasted effort when deadlock happens because of all the retries
    - locking can be loosened to imporove performance in the form of predicate locks
      - this is where a lock occurrs on a set of resources that match a given predicate
      - this helps avoid phantoms (objects that are written after a transaction gets its version but before it finishes)
      - can use more generic predicates that work better with indexes to further improve (not perfect, but has better performance)
  - optimistic concurrency control
    - literally serializing the transactions is an extreme form of pessimistic concurrency control where it is assumed that something bad will happen
    - two phase lock is another version of pessimistic concurrency control
    - optimistic concurrency control assumes the best in transactions, and only steps in to mitigate conflicts when it detects them after the transaction runs
    - this approach is better under lower load where transactions are less likely to conflict, but worse in larger loads where transactions are more likely to conflict
    - this approach also requires a detection mechanism
      - the more detail this mechanism records to detect conflict, the more overhead it adds, so there is a sweet spot here
    - SSI is one strategy that is relatively new at the time of writing this, but needs more research before becoming the industry standard even though it is a promising alternative to other approaches since it provides serializability and scales

### Durability

- this is the guarantee that data won't be lost
- this has changed definition over time from "data is written to disk" to "data is replicated"
- this can never have a 100% guarantee

## Ordering

### Total order

- when every read and write can be sequenced
- implies causal order
- provided by linearizable systems
- this is easily done with single leader replication
- cannot guarantee that this is immediately seen on all nodes however
- all you can guarantee is that when the system stabilizes, the order will be the same on all nodes
- total order broadcast
  - this requires reliable and totally ordered delivery
  - you need this for uniqueness guarantee
  - you can build linearizable systems off of this
  - this has the added benefit over lamport clocks in that there are no gaps in order numbers
- the following are equivalent concepts (i.e. if you have one, you can build any other off of it)
  - total order broadcast
  - linearizable compare and set
  - locks and leases
  - atomic commit
  - membership service
  - uniqueness constraint

### Causal order

- when all writes that are causally related can be ordered
- does not imply total order
- to provide this you need to track what transactions read what data
  - this can be done with version vectors
    - doesn't scale well
  - sequence or timestamps can be used
    - preferrably with a logical clock
    - don't need a leader to generate unique numbers across replicas though
    - one can use lamport clocks

### Lamport clocks

- not version vectors
- version vectors can distinguish if two ops are concurrent or causally dependent whereas lamport clocks enforce total ordering
