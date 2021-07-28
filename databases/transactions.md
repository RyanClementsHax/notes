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

### Durability
- this is the guarantee that data won't be lost
- this has changed definition over time from "data is written to disk" to "data is replicated"
- this can never have a 100% guarantee