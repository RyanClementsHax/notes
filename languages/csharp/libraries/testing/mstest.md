# MSTest

## Test lifecycle
1. Collect all test cases
1. Split collection into two based on DoNotParallel flag.
1. Group parallel collection based on ExecutionScope flag.
1. Create queue of groups
1. Create threads where each
    1. Take first from queue
    1. Check assembly initialize execution and run in case is not executed yet
    1. Check class initialize execution and run in case is not executed yet
    1. Execute test method
1. Wait for all threads
1. Run all not parallel tests
1. Run all class cleanups
1. Run assembly cleanup
- if you write your tests independent of each other, you will never need to concern yourself with this
- all non parallel tests run after parallel tests
- the class cleanup function is not called until ALL tests, both parallel and sequential, are finished
  - there is a [github issue](https://github.com/microsoft/testfx/issues/580) for this (that contains my first comment on an OSS repo :D)
  - this means that if classes need to release resources for other tests to use (like db connections), they won't get released until all tests are finished

## [CollectionAssert.AreEqual vs CollectionAssert.AreEquivalent](https://stackoverflow.com/questions/29757258/collectionassert-areequivalent-vs-assert-equals#:~:text=AreEqual%20check%20that%20they%20have,same%20items%2C%20in%20any%20order.)
- the first does a strict equal of the elements pairwise
- the latter basically does set comparision