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