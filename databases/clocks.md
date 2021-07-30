# Clocks

- most systems use LWW (Last Write Wins) for determining transaction order
- with clock skew, writes will silently be dropped
- a database cannot distinguish between sequential writes and truly concurrent writes
- logical clocks are best used to determine ordering like with version vectors
- when using snapshot isolation with transactions in a distributed db, you can't use a monotonic or wall clock
- you need some way to sync clocks well in order to get transaction throughput tollerable
- it will never be perfect, but you can use confidence intervals with time reported
- you can then increase transaction throughput by using some higher precision clock syncing like a gps clock (google uses this in its Spanner system)
- when determining a leader, many systems will have a concept of "leasing" the leader role and using timeouts
  - this will be problematic with these clock problems
  - what if the process got the lease, experienced a GC pause for 15s, then thought it was the leader from then until it timed out?