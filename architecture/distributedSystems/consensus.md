# Consensus

- quorums are used for this
- you can't trust any one individual node's perception of the situation
- can solve with a central service, but has problems if a single node pauses and assumes that no time passed thinking that the current state of affairs is where it was before the pause
  - monotonic tokens are helpful to remediate this, but this assumes that every actor tells the truth and requires server side checks
  - meta data services like zookeeper are helpful for this
  - ex:
    - a file service wants to restrict one client to a file at a given time
    - a lock service could be called to coordinate access
    - but what if a client acquires the lock, pauses long enough for the lock to timeout because of a GC pause or high load, in that time another service acquires the lock, and the original service resumes thinking it still has the lock
    - if a monotonic token was used, the file service would verify the token isn't less than the most recently used token
- you can't always trust that everyone is telling the truth
  - this is called a "byzantine" system if one or more actors are malicious
  - systems that tolerate this are often expensive to make and maintain
  - some sectors need this like aerospace where high levels of radiation manipulates bits
  - P2P applications is another example
  - validation mitigates weak lying caused by corruption problems
  - sanitizing your inputs is always good practice
