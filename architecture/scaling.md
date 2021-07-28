# Scaling

- your 99.9 percentile of requests are probably the most valuable requests to optimize for because those users are more likely to provide more of your company's income
  - also the few requests that take super long will cause head of line blocking for others causing their experience to worsen too
  - this is the case with amazon where some accounts were so big that they took a long time to process
  - creating a poor experience for these accounts will likely lead to losing that account that provides so much revanue
- response times need to be measured ON THE CLIENT because that is what the user experiences
  - this avoids the problem where the problem can't be seen because it isn't being picked up by the server monitoring tools (e.g. queuing delays unseen)

## Mapping requests to servers
- sometimes you want traffic to be routed to the same server even in a microservice environment to create a consistency guarantee or to easily handle streaming
- consistent hashing is really good for mapping requests to servers
  - e.g. a stream id instead of IP
    - using an IP will run you into problems when NATs are used
  - this allows you to handle failures well too

## Circuit breakers
- this is when services cut connections to other services when they experience failures, then send a small amount of traffic to test if the service is available, then open the connection again if it is
- another benefit aside from fault tolerance is that it allows a service to release resources like memory, threads, and sockets pessimistically instead of waiting for long timeouts everytime a request is made to a service it knows its down

## Thundering herd
- this is when a large amount of traffic happens in one instant due to traffic patterns or failures in the system
- e.g. slack experiences a thundering herd when slack clients connect in the morning due to people going to work
- e.g. if cache layers fail, then the database will experience a thundering herd of clients that are looking to fetch data from them given they can't get it from the cache any more

## Twitter's tweet problem
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