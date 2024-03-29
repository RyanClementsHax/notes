# Streaming

## Event Processing

### CEP (Complex Event Patterns)

- this allows an analysis to be done on the event log to identify patterns of interest
- a query is stored and the event log is run through it

### Analytics

- same concept as CEP
- load a query that the streams pass through
- the query outputs the result
- may want to filter the events before running them through the query to prevent unneeded processing

### Time

- which clock do you use to timestamp events and when they are processed?
- using local machine clock will skew event occurrance with event processing time due to processing delay
- cannot trust the device clock
  - it can be tampered
  - could use three timestamps to approximate actual event time
    - 1: event time according to device
    - 2: time sent according to device
    - 3: time recceived according to server clock
    - if we do 1 + (3 - 2), this gives us an approximation of what time the event actually occurred on the device relative to the server (which is hopefully more accurate)
      - this assumes that network delay is negligable

### Order

- you can't always trust that events will be received in order due to the nature of distributed systems
- in data warehouses, the nondeterministic order of these events is called SCD (Slowly Changing Dimension)
  - could alleviate by introducing a unique identifier for each verion of a record
  - this prevents you from using log compaction however

### Windowing

- sometimes you want to process only a given range of events
- how do you determine that all events have happened within a time window?
  - you could drop events that happen after you prematurely close the window
  - you could also publish a correction
  - you could trigger windows based on a special event produced by the producers when they are done producing, but if there are multiple producers, you would have to know how many producers there are which leads to extra overhead on top of managing the addition and subtraction of producers
- what if a device comes back online and dumps a bunch of old events into the system?
- three types of windows
  - tumbling windows
    - this is when the boundaries are partitions of the stream
  - hopping window
    - this is when the window is a fixed size but overlapps with other windows
  - sliding window
    - this is when the window slides along the event stream order

### Joins

- good for correlating data between two streams for purposes such as analytics
  - e.g. determining the click rate of search results
- cannot assume that two related events will be temporally near
  - need to set some timeout and be ok with dropped data
- ordering is not guaranteed
- three types of joins
  - stream - stream
    - the stream join processor keeps state with the stream events indexed so that it can output the joined events when they come in
    - it can also emit that no such event happened after a timeout occurrs
  - stream -table
    - this is also called "enriching" because it grabs extra data from a data source like a db table to add data to some event passing through
    - this adds load on the db which may not be wanted
    - can have cache to reduce load
    - can solve with change data capture, but now you're doing stream - stream

### Microbatching

- batch jobs provide exactly once semantics, but streams can't do this because there aren't any batches
- microbatching is a tradeoff
- this is where streams are implicitly divided into very small tumbling windows to provide exactly once semantics
- if output leaves stream processor (e.g. email sent) there is no way to undo that, and you lose ability to restart failed tasks
  - need atomic commit in order to get this working

## Recovering from failure

- could keep operator state on the local machine
  - this is quick and easily accessed
  - not very durable
- could use db
  - but this is slower since it needs a network call
- remote vs local storage tradeoffs can change depending on needs and development in this area
