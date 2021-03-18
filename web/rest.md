# REST

## Problem details
- [Problem details](https://lakitna.medium.com/understanding-problem-json-adf68e5cf1f8) is a standard for returning details of an error

## Bulk operations
- you may be tempted to implement a bulk operation for an api, but there are some considerations
- rest, philosphically, operates on single resources and each endpoint returns an individual status code
  - bulking will break this
  - but yet there are situations when you need to break this for performance reasons
- considerations
  - data consistency is now different
    - sometimes you need to pull data in memory before making writes
    - that data if done before all of the writes take place can more easily become stale if some outside force is editing it
      - i.e. if you upload a spreadsheet where every row needs to meet some unique constraints, what happens when multiple users upload the same spreadsheet at the same time?
    - all of your resources in the bulk operation may be individually valid, but they may not be able to exist with each other
      - i.e. if you upload a spreadsheet of user data where two rows have the same value for the email column which is supposed to be unique in the db
  - what happens when a single resource fails?
    - how should this effect the other work?
    - how is this reported to the client?