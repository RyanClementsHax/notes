# Chapter 4: Software Quality Attributes

- quality attributes may conflict
  - a secure application typically suffers in usability
  - a scalable application typically suffers in speed or consistency

## Internal vs external quality
- internal
  - defined by code
  - will often have an impact on external quality
- external
  - defined by what users see
  - *ilities*

## Testing quality attributes
- manually testing the software
- creating benchmarks and using tools
- performing code reviews and calculating code metrics
- executing automatred tests

## Maintainability
- allows for easier reverse engineering
- the biggest cost of software tends to be in supporting it; thus, you can drive costs down by desiging a system to be maintainable
- types
  - corrective
    - fixing defects
  - perfective
    - implementing new or updated requirements
  - adaptive
    - adapting software to changes in environment
    - ex: OS upgrade
  - preventative
- modifiability
  - the easier it is for someone to modify the code, the more maintainable it is
- extensibility and flexibility
- try to make modifications in small scopes
- desiging for maintainability
  - reduce complexity (complexity leads to more bugs)
  - reducing dize
  - increasing cohesion (similitude within env or code)
  - reducing coupling
- measuring maintainability
  - lines of code (LOC)
    - physical LOC just counts lines of code
    - logica LOC counts the lines of code that matter (i.e. no comments)
  - cyclomatic complexity
    - `CC = E - N + 2P`
  - depth of inheritance (DIT) tree

## Usability
- directly correlates with user satisfaction
- user's perception of the quality of the software increases with usability
- can be one of the cheapest ways to increase the quality of a software system
- increases productivity of users
- you will have problems if your users don't want to use your application
- when software systems exhibit this, it allows users to complete their tasks efficiently
- the speed in which a user performs their actions reflects a systems usability
- needs to be considered as early as the requirements phase

## Learnability
- straight forward
- very imporant for new users
- includes how easy it is for experienced users to learn new functionality
- providing useful feedback goes a long way
  - appropriate validation and helpful error messages
  - friendly and informative messages to user
  - tooltips
  - feedback for long-running processes
- usability testing should begin when there is a working version of the software available
- help/documentation systems go a long way in this area
  - FAQs

## Accessibility
- can include
  - making software usable with just a keyboard
  - support for assistive tech like screen readers
  - non-text content has text alternative
  - there are ways to help users navigate software
  - enough time to read and use web page or screen
  - using colors well
  - logical tab order for controls and input fields

## Availability
- how many 9's
- achieving more 9's will result in diminishing returns and prevents your teams from focusing on other work
- most users won't be able to tell the difference between 5 9's and 4 9's
  - other factors will drown this out like network speed
- multiple ways of calculating
- detecting
  - ping/echo
    - the watcher pings the service to see it is up and after timeout considers it unavailable
  - heartbeat
    - the service sends the watcher a message every so often and after timeout considers it unavaiable
  - timestamp
    - detecting errors by analyzing timestamps of events
  - voting
    - detecting faults by detecting outliers in voting
  - sanity test
    - simple input and output comparison
  - condition monitoring
  - self tests
- recovering
  - exception handling
  - retry
  - varying leves of redundancy
  - rollback
  - graceful degredation
  - ignore faulty behavior
  - preventing faults
  - removal from service
  - transactions
  - increasing competence sets
    - essentially modifying the system to handle the edge cases
  - exception prevention

## Other *ilities*
- portability
  - also includes i18n and localization
    - changes in text should not change code
    - unicode should be used for this
- adaptability
- installability
  - also how a system handles update/upgrade process
- replaceability
- interoperability
  - includes locating system
  - uses common standards
  - needs testing
- testability
  - controllability
  - observability
  - isolability
  - automatability
  - test documentation
    - having documentation is a sign of higher organizational maturity
    - avoids tribal knowledge
    - too much can become a risk