# Chapter 5: Designing Software Architectures


## Software architecture design
- serves as technical guidance and typically occurs iteratively until the initial architecture is at a point where the development team can begin their work
- making decisions
  - you need to get good at pro/con when making decisions
  - try to make decisions that resolve issues so the solution can be implemented
  - perfect is the enemy of good

## Terms
- structure
  - groupings of and relations between elements
- element
  - generic term that can be used to represent any of the following: system, subsystem, module, component
- system
  - represents the entire software project including all of its subsystems
  - highest level of abstraction
- subsystem
  - logical groupings of elements that make up a larger system
  - can be created in many ways like partitioning a system by functionality
  - could, but not always, represent standalone software applications
- module
  - logical groupings of elements
  - contained within a subsystem and consists of other modules and/or components
  - typically focused on a single logical area of responsibility
  - teams assigned to a subsystem will be responsible for the modules that make up that subsystem
- component
  - execution units that represent some well-defined functionality
  - smallest level of abstraction

## Importance of design
- a proper design determines whether the requirements and quality attributes can be satisfied
- key decisions are made
  - it is easier to change major parts of architecture in the design phase than after implementing and depending on the inferior design
- avoiding design decisions can incur tech debt
  - sometimes its ok to take on tech debt
- a design communicates architecture to others
  - also improves cost and effort estimates
- provides guidance to devs
  - can also be good for training/onboarding devs
- influences non-technical parts of project
  - purchasing of other tools and licenses
  - hiring of team members
  - the organization of the dev env
  - deployments

## Approaches
- top down
  - start off at the system and decompose from there
  - advantages
    - lends itself well to the division of work
    - as further decomposition takes place, tasks can be created for individual team members
    - useful for large projects as it can help manage them
  - disadvantages
    - domain should be well understood
    - BDUF (Big Design Up Front) or BUFD (Big Up Front Design), otherwise construed as too much design up front without leaving room for flexibility or regarding unknowns
    - doesn't lend itself well to sharing between teams
      - can be mitigated with colloaboration tools or up front abstraction on the part of the architect
    - be careful not to become an ivory tower architect
  - good for projects that
    - are large
    - are complex
    - need enterprise software designed for the organization
    - the team is large or there are multiple teams
    - the domain is well understood
- bottom up
  - begins with components
  - combine components like lego bricks into larger modules until all requirements met
  - no up front design
  - "emergent design" or "emergent architecture" because the desgin "emerges" from this process
  - advantages
    - does not require the domain to be well understood
    - more simplicity
    - works well with agile development
    - NDUF (No Design Up Front)
    - no wasted design with BDUF
    - recognizing opportunities for reuse becomes easier
  - disadvantages
    - assumes change is cheap
    - can lead to lower levels of maintainability with no up front design
    - more difficult to estimate the entire project
    - entire scope of work may not be known when using this approach
    - design flaws may not be detected until later
  - good for projects that
    - are small
    - are not complex
    - don't need enterprise software to be designed
    - the team is small or there is only one team
    - the domain isn't well understood
- choosing the right one
  - either extreme isn't a good idea
  - typically a mix of both is healthy

## Greenfield systems
- usually don't have a lot of architectures to referrence if this is a new domain
- regardless, you will likely (and should) spend time creating prototypes to do proof of concepts and to explore the domain further

## Brownfield systems
- once you understand the architecture already built and systems already being used, you can think of it as a greenfield project that's been through some iterations

## Architectural drivers
- these are considerations that need to be made for the software system that are architecturally significant
- they drive the design of the arch
- design objectives
- primary functional requirements
- quality attribute scenarios
- constraints