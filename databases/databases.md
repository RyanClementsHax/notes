# Databases

## Append only logs
- these are constructs used in db implementations that simplify writings and helps to make the db more resilient to faults
- this helps with faults because it avoids partially written records
- this construct only works well with data locality else you will do a bunch of random seeks

## Indexes
- hash maps
  - these don't perform well on disk because of performance problems with random seeks and hash collision resolution

### Multidimensional indexes
- these are structures that allow you to index based on more than one field
- these are helpful for queries like those that act on geospacial data (longitude and latitude)
- multidimensional indexes can be sort of spoofed with clever mapping of keys to another range of keys (e.g. clever concatination of two domains to one range)

## Compaction
- if compaction can't keep up, reads slow down cuz they need to check more segment files

## Document databases
- compared to their relational counterparts, they win in data locality since the document is often laid out in contiguous memory and isn't split up
- increasing the size of a document should be avoided since it would need to copy the document to a new location

## In memory databases
- these don't have the same problem of needing to represent the data in disk compatible structures like B trees
  - e.g. redis can keep sets and priority queues in memory
- some memory dbs try to provide durability by using battery powered ram and other special hardware
- theory says that in mem dbs can represent more memory than available ram via good eviction policies

## Columnar storage
- these databases lay out tables as collections of columns rather than collections of rows
- it lends itself well to compression and cpu caching
  - cpu caching works well because more data can fit into one page
- sorting entries in column sotrage needs to be synched by row across columns
  - e.g. row 23 is the 23rd entry in each of its column tables
  - this means that choosing indexes and sorting tha taligns with the queries you run is important
  - sorting entries like this means that compression works very very well
- making indexes for each query you will run is practical given the copied, read only nature of the data
  - with multiple indexes, writing becomes harder
- disk bandwidth is typically the bottleneck for these dbs