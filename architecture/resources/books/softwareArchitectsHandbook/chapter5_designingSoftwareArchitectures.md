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
  - purpose of the specific architecture design
- primary functional requirements
  - business critical functions
- quality attribute scenarios
  - how the architecture should respond to particular stimuli
  - these need to be testable and not vague
- constraints
  - decisions imposed on the software architecture that typically cannot be changed

## Software architecture patterns

- reference architecture
- externally developed software
  - commercial
  - open source

## Design rationale

- the design decisions for what was decided on and what WASN'T decided on should be documented for many reasons including
  - evaluation
  - verification
  - knowledge transfer
  - communication
  - maintenance
  - documentation
  - reuse
    - also can include variation points to let implementers modify the architecture to fit their own needs

## Using a systematic approach to architecture design

- general model
  1. architectural analysis
  2. architectural synthesis
  3. architectural evaluation
- selecting a process
  - what are the activities and artifacts of the design process?
  - are there any activities/artifacts that you think are not needed?
  - are there any activities/artifacts that you feel are lacking?
  - what are the techniques and tools of the design process?
- ADD (Attribute-Driven Design)
  - iterative
  - close attention to quality attributes early in process
  - doesn't cover other parts like documentation
  - 8 steps
    1. review inputs
    - design objectives
    - primary functional requirements
    - quality attribute scenarios
    - constraints
    - architectural concerns
    1. establish iteration goal by selecting inputs
    1. choose one or more elements of the system to refine
    1. choose one or more design concepts that satisfy the inputs
    1. instantiate architectural elements, allocate responsibilities and define interfaces
    1. sketch views and record design decisions
    1. perform analysis of current design and review iteration goal and achievement of design purpose
    1. iterating if necessary
- Microsoft's technique for architecture and design
  - also iterative
  - 5 steps
    1. identifying architecture objectives
    2. identifying key scenarios
    3. creating application overview
       - what an architecture looks like when complete
       - intended to connect architecture design with real world decisions
       - consists of determining your application type, identifying deployment constraints, identifying architecture design styles, and determining relevant technologies
    4. identifying key issues
    5. defining candidate solutions
  - each iteration reviews a scenario
  - seems more user/client focused
- ACDM (Architecture-Centric Design Method)
  - also iterative
  - lightweight method with a product focus
  - seeks to ensure software arch maintains a balance between business and technical concerns
  - attempts to make the software arch the intersection between requirements and the solution
  - 7 steps
    1. discover arch drivers
    2. establish project scope
    3. creating notional arch
    4. arch review
    5. production go/no-go
    6. experiment planning
    7. experiment and refine arch
- ADM (Architecture Development Method)
  - a core part of TOGAF (The Open Group Architecture Framework)
  - complex state diagram for steps
  - focuses on evaluating the current architecture, validating the target architecture, then creating a migration plan to get from current to target

## Tracking architecture design process

- use your current system of work (e.g. scrum, kanban)
- it is ideal to use an agile framework since it pairs well with the iterative nature of these architecture design frameworks
- prioritizing work (DIVE)
  - Dependencies
  - Insure against risks
  - business Value
  - Estimated Effort
- don't expect your arch backlog to be static
  - it evolves as the arch evolves
