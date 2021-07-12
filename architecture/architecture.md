# Arthitecture

- good principles (as taught by aws)
  - perform operations as code
  - annotate documentation
  - make requent, small, reversible changes
  - refine operations procedures frequently
  - anticipate failure
  - learn from all operational failures
  - see "well architected framework" for more details
- if you can't draw out your architecture, you have problems
- to generate an architecture with properties you want, you start with the properties, then create contstrains in the system that allow for those properties
- great architecture comes out of some smart person having a good idea so your org needs to foster ideation and experimentation
- architecture affects company culture
  - when done poorly, it creates structures and processes that breed blame
  - this requires a mature organization though

## Tenancy
- [differences between multi and single tenancy](https://www.netsolutions.com/insights/5-reasons-why-you-should-choose-multi-tenant-architecture-for-your-saas-application/)

## Dependencies
- the more a service is depended upon, the lesser its ability it has to change

## Polling
- long polling
  - where you keep a request open for a certain amount of time just in case the new data arrives within that time
  - this can reduce the load introduced with normal polling

## Scaling

- your 99.9 percentile of requests are probably the most valuable requests to optimize for because those users are more likely to provide more of your company's income
  - also the few requests that take super long will cause head of line blocking for others causing their experience to worsen too
  - this is the case with amazon where some accounts were so big that they took a long time to process
  - creating a poor experience for these accounts will likely lead to losing that account that provides so much revanue
- response times need to be measured ON THE CLIENT because that is what the user experiences
  - this avoids the problem where the problem can't be seen because it isn't being picked up by the server monitoring tools (e.g. queuing delays unseen)

### Twitter's tweet problem
- twitter wants to deliver tweets to users within 5 seconds of the tweet being tweeted
- their first strategy was for a client to grab the new tweets of all the people they are following that they haven't seen yet, sort them by date, then display it
  - this didn't scale well because each user would cause a butt ton of reads every time they queried for more tweets
- their second strategy was for a post to write to users's "inboxes" that they read from
  - this led to more manageable read patterns
  - there are accounts that have millions of followers though
  - this caused a write amplification for every post they would make that would put massive strain on their servers
- their third strategy was a mix of the two
  - for most accounts, they would use the second strategy because it scaled well for the majority of their users which had relatively small amounts of followers
  - for their few massive accounts with millions of followers, they reverted to the first strategy to prevent the massive write amplification
  - this is the strategy they use as of writing this

## ADRs (Architectural Decision Records)
- super useful for recording big decisions for projects
- could be anything from language choice to architecture choice
- the point is to write them down so you can refer to them later
- very useful if your project will exist long enough to see employees turn over
- [here](https://adr.github.io/madr/#the-template) is one good template (there are many)