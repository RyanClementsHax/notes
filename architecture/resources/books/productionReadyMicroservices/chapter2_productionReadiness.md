# Chapter 2: Production Readiness

- production readiness standards
  - stability
    - a stable development cycle
    - a stable deployment process
    - stable introduction and deprecation process
  - reliability
    - a reliable deployment process
    - planning, mitigating, and protecting against the failures of dependencies
    - reliable routing and discovery
  - scalability
    - well defined quantitative and wualitative growth scales
    - identification of resource bottlenecks and requirements
    - careful, accurate capacity planning
    - scalable handling of traffic
    - the scaling of dependencies
    - scalable data storage
  - fault tolerance and catastrophe preparedness
    - potential catastrophes and failure scenarios are identified and planned for
    - single points of failure are identified and resolved
    - failure detection and remediation strategies are in place
    - it is tested for resiliency through code testing, load testing, and chaos testing
    - traffic is managed carefully in preparation for failure
    - indicents and outages are handledf appropriately and productively
  - performance
    - appropriate service level agreements (SLAs) for availability
    - proper task handling and processing
    - efficient utilization of resources
  - monitoring
    - proper logging and tracing throughout the stack
    - well designed dashboards that are easy to understand and accurately reflect the health of t5he service
    - effective, actionable alerting accompanied by runbooks
    - implementing and maintaining an on-call rotation
  - documentation
    - thorough, updated, and centralized documentation containing all of the relevant and essential information about the microservice
    - organizational understanding at the developer, team, and ecosystem levels
- implementing these standards needs by-in from all levels of the organization
