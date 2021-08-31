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

## ADRs (Architectural Decision Records)

- super useful for recording big decisions for projects
- could be anything from language choice to architecture choice
- the point is to write them down so you can refer to them later
- very useful if your project will exist long enough to see employees turn over
- [here](https://adr.github.io/madr/#the-template) is one good template (there are many)
