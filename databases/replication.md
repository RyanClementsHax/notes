# Replication

## Semi synchronous replication
- this is where one leader sends the writes to all of the followers, but doesn't send a success back to the client until at least one of the followers acknowledges
- this is poorly named because the commit on the leader doesn't wait for the commit to happen on one of the followers before committing on itself, it only waits to send the success back to the client until one of the followers recognizes the commit as well
- it is still possible for data to get out of sync if the leader goes down without sending the write to the followers

## Sending logs to replicas
- sending the queries to replicas for them to perform on their own will run into problems if the query uses non-deterministic features like getting the current time or if the query order matters
- another solution is to send the products of the queries to the replicas, but this can run into problems if the write was particularly large or if the nodes are on different versions (and thus implement serialization differently)