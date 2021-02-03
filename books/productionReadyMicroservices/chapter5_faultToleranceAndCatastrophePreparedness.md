# Fault Tolerance and Catastrophe Preparedness

- no microservice is immune to production fires
- identify and architect away single points of failure
- identify failure scenarios
  - internal
    - ex: host, logic failures
  - external
    - ex: queue, datacenter, other microservice failures
- test for these failures
  - code testing
  - load testing
  - chaos testing
  - it is crucial that this failure testing also happens in production
  - any new failures that occur need to be added to this test suite
- failure detection and mitigation need to be departmentally standardized
