# Consensus

- quorums are used for this in many distributed systems
- FLP says consensus can't be done
  - but that paper was assuming a sytem without timeouts, or if a node can fail
- you can't just send all transactions to other nodes to gain consensus
  - what if one of the nodes goes down or doesn't commit it to its commit log or if network failures are present?
- these algorithms require a majority of nodes to be working properly to function properly themselves
- can use total order broadcast for consensus
- no algorithm is perfect in light of an unreliable network

## Using coordinators

- you can't trust any one individual node's perception of the situation
- can solve with a central service, but has problems if a single node pauses and assumes that no time passed thinking that the current state of affairs is where it was before the pause
  - monotonic tokens are helpful to remediate this, but this assumes that every actor tells the truth and requires server side checks
  - meta data services like zookeeper are helpful for this
  - e.g.
    - a file service wants to restrict one client to a file at a given time
    - a lock service could be called to coordinate access
    - but what if a client acquires the lock, pauses long enough for the lock to timeout because of a GC pause or high load, in that time another service acquires the lock, and the original service resumes thinking it still has the lock
    - if a monotonic token was used, the file service would verify the token isn't less than the most recently used token
- two phase commit is a kind of consensus algorithm
  - first phase is where the coordinator sends a prepare message to all of the nodes
  - the second phase is where the coordinator tells everyone to commit or abort the transaction
  - both phases are points of no return; they are decisions that cannot be undone and the system cannot make progress unless those steps are recognized
  - this can cause everyone to be stuck waiting for the coordinator
  - it is possible to have orphan transactions if the coordinator loses memory of a transaction it started
    - all nodes cannot progress without human intervention in this case
    - heuristic decisions can be used to bail out but should only be used in grave cases
      - this is when a node commits or aborts the transaction itself if it has good reason to think the coordinator is down and it needs to make progress
- three phase commit tries to overcome some of the short comings of two phase commit
  - assumes a perfect failure detector and a network with bounded delays and response times which isn't practical
  - often not used
- coordinators need a commit log too as they are a kind of database that needs to have some guarantees on durability

## Byzantine systems

- you can't always trust that everyone is telling the truth
  - this is called a "byzantine" system if one or more actors are malicious
- systems that tolerate this are often expensive to make and maintain
- some sectors need this like aerospace where high levels of radiation manipulates bits
- P2P applications is another example
- validation mitigates weak lying caused by corruption problems
- sanitizing your inputs is always good practice
- not all consensus algorithms handle this

## Distributed transactions

- two phase commit is often used, but far from perfect
  - many systems don't offer distributed transactions because there is no silver bullet
- two types of distributed transactiosn
  - db internal distributed transactions
    - these are within a homogenous system
    - e.g. distributed transactions between postres nodes
  - heterogeneous distributed transactions
    - these are between disparate systems
    - e.g. between an application and a message queue
- these allow for exactly once message delivery guarantees
  - this does require the same protocol to be used in all parties
- distributed consensus uses two rounds of voting and quorums to ensure consensus
- this is very similar to derived data
  - transactions provide linearizability though
  - derived data is async whereas transaction provide synchronicity
  - derived data is a better approach with not great support for distributed transaction protocols like XA
