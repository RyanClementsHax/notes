# Chapter 1: Microservices

- versioning of microservices is an antipattern
    - microservices are not libraries
    - other teams may pin a dependency on an older (perhaps unmaintained) version of the microservice
    - logging and monitoring become complicated
    - for the same reason, versioning endpoints is also an antipattern
- organizational challenges
    - inverse conway's law
    - technical sprawl
    - more ways to fail
    - competition for resources (labor, infrastructure, etc)
        - prioritizing teams by business criticality often leave out tools or innovation type teams