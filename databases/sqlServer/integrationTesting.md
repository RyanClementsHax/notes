# Integration Testing

- performing integration tests using a catalog (or multiple) on a single serve can be tricky
- the payoffs are great though
- if you have your own tool for handling migrations that you can use to reset databases from your test code, use those
  - before each test run, simply delete the database, then remigrate
  - this may be a problem if migrations take forever though
- if you can write all of your database access code within a transaction, you can run each test within a transaction, never commit it, and roll it back after the test finishes running
  - this allows you to run tests concurrently
  - not all data access layer projects are written in such a way that allows this
- if that isn't an option, using snapshots is probably the best way to go
  - manually deleting data gets hairy when you have lots of relationships between tables
  - snapshots seem to take a minimum of 1.5s to perform each however, and then grow linearly with the amount of pages you dirty
  - this assumes that you can easily create a migrated db to use for testing

## Using snapshots

- here are steps to implement this in test code (this is one way of accomplishing this)
- do the following before your tests that require the catalog runs

1. grab the database name you want to test off of (can come from config or hard coded string) and the name any snapshot that has already been created for it (could exist from previous test runs)
2. get the server restart time (needed later)
3. optionally, test the connection to the database by running a simple query
4. optionally, you can add a check to make sure that the database is fully migrated to help prevent the accident that the database wasn't set up right before test runs leading to poor developer experience
5. handle snapshot restoring
   - if you don't care about the time it takes to restore a snapshot on test startup, always restore the snapshot if one already exists, else create one
   - if you do care about the time it takes to restore a snapshot on test startup, we can make a few optimizations to only restore a snapshot if we need to
     - you will need to grab two more things about the snapshot to do this
       - when it was created
       - whether it is in sync with the catalog (see my sql server notes on how to do this)
     1. if there was a preexisting snapshot
        - 1a. and it is out of sync with it is catalog, restore that one
        - 1b. if the server restart time is greater than the snapshot creation date, recreate the snapshot
          - see my sql server notes on why we need to do this
          - tldr: the best metric we use to figure out whether a snapshot is in sync gets wiped on restart and there really isn't a good way around it
     2. if there was no preexisting snapshot, create one

- do the following in your test code
  - make sure the tests run sequentially to avoid race conditions
  - after each test, restore the snapshot
  - optionally, expose a method for your tests to call when they want to manually reset a database (if you have a use case that is)

### Optimizations

- if you have hundreds of tests, waiting almost 2 seconds for a snapshot to restore after each test might be out of the question
- there are a few things you can do to make things faster

1. use multiple catalogs and run your tests concurrently
   - this assumes you have a way to easily create multiple migrated catalogs on the same server
   - this does mean that your set up code has to be more complex to grab all of the catalogs on the same server and do the snapshot restoring logic on each of them
     - you can do this process in parallel for each catalog to save time
     - you could also do this with multiple servers, but that's more overhead than its worth
     - you also need to handle the case that one of the catalogs isn't migrated or in a bad state some how
       - it is my recommendation that you fail the whole test run as there is something wrong with your process on creating the catalogs making the catalogs being used for testing unreliable
   - you need to implement a way for your tests to reserve catalogs
     - e.g. using a concurrent queue for tests to dequeue db connections from when they start execution and to enqueue when they are done with them
   - you also need to be cognizant of making sure that database resources are always released even on test failures else test execution may deadlock
2. group tests that can be run in any order together and only restore after groups finish executing
   - e.g. it may be the case that all of your read operations can be grouped together and won't need a snapshot restore
   - e.g. it also may be the case that your users data access tests could run in any order (e.g. they don't make any data changes that would fail other tests if run in the incorrect order) but not with orders data access tests so you could group users tests and orders tests separately and thus only have to do two restores instead of a restore for each one of those tests
3. order tests and selectively restore your snapshots
   - test ordering is considered an anti pattern, but rules need to be bent or broken to allow for optimizations
   - not all test frameworks have good ways of doing this
4. using a combination of the above
   - in a project I was part of, we implemented the multiple catalog options with grouping
   - we got great parallel execution, only restored when we need to, and as a result sped up tests 10x
