# Distributed Systems

## Immutable architecture
- immutablity gives you hashing comparision bonuses
- autogenerated db id's cannot be used to ID an object in a distrib system
  - it is preferred to go for naturally occuring IDs or public keys
  - this helps with the location independence property distrib systems should have
- progressive web apps might connect to different db when coming back online which will lead to consistency issues

## Databases
- db sharding is hard because of the overhead needed to determine where someone's data lies on top of allowing your database to perform efficient join or aggregation type queries
- it is best to use a db that supports sharding inherently and to relax your consistency requirements

## Policy enforcement
- doing this in a distributed env is difficult but necessary for many situations like regulated industries (finance, healthcare, etc)
- great for config validation
- [OPA](https://www.openpolicyagent.org/docs/latest/ecosystem/) (Open Policy Agent)

## Anti-fragile architecture
- [ref](https://www.se-radio.net/2020/01/episode-396-barry-oreilly-on-antifragile-architecture/)
  - the show notes have lots of good resources in them
- the quality of a system is tied to its ability to withstand complexity not how well it adheres to a spec
- complexity exists outside of code
- how we can deal with complexity
  - making it someone else's problem (not great)
  - try to identify patterns even if they aren't there (we can easily fool ourselves into thinking it is even possible to understand systems that are complex which is dishonest to ourselves)
  - think of processes as flows, not user requirements (helps break down architecture and modularize it)
- flows
  - this is the basic unit of work in an info system
  - start with stressors like when a competitor drops price, not with features
  - build a path to handle stressors but build in the typical "switch" to deal with unexpected events if you can
  - discussing what happens if fire breathing lizards storm the country helps guide the conversation not on focusing on probability but stressors
    - if you can make your system resilient to this problem, you inadvertantly solve dozens of others you never predicted like social unrest, war, hurricanes, datacenter outages
  - look for dependencies between flows to determine relatedness
  - group components and flows that are impacted by the same stressors
- really good properties every antifragile system has
  1. weakly linked
  2. modularity
  3. redundant
  4. diversity
  - whenever we change something about an existing system, we always change something related to one of these areas
- as an architect, you need to understand the business
  - what happens when a competitor drops their price? (answering this can reveal a lot about what is important to a business and how it works)
- resilient systems are birthed by stress, not by design
  - requirements inherently make a system fragile
- its ok not to know everything
  - complex systems are exactly that and no experienced group of people can predict anything no matter how experienced they are
- everyone has a different viewpoint on complexity and what is considered complex

## Messaging
- giving consumers leases for messages they consume allows for the message not to be lost and to quickly be consumed by some other consumer if that consumer goes down

## Misc
- relaxing constraints on the business requirements can lead to opportunities like amazon overselling items, but handing out gift cards when they can't fulfill everything