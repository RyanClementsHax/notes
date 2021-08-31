# Data Lakes

- avoid copying data
  - this leads to a lot of stale data problems
  - it is also hard to know who is consuming your data
  - making changes is difficult
  - it is better to provide views on top of data (there are many companies built around this)
- these take the burden of transformation off of the data producers
  - this is particularly valuable when the data has many consumers
  - e.g. disparate teams that need the data for different reasons
