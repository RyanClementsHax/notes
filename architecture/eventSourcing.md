# Event Sourcing

- can squeeze a lot of business value from this as opposed to just storing state
- good for apps that
  - are heavy in workflow
  - have unknown metrics or want to allow for metrics created in the future to be evaluated on past data
  - need "time travel" questions answered
    - e.g. what was the state of the account two days ago at 5pm?
- building out event sourcing leads to consistency issues with the read side of the application that needs to be dealt with
- sometimes you need to delete all data you have for a given entity due to some regulation
  - doing this with an event log is hard since it is immutable by design
- this pattern has overhead that makes it prohibitive for some applications
