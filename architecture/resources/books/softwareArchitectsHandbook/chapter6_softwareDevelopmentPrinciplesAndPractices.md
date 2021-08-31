# Chapter 6: Software Development Principles and Practices

## Loose coupling

### Types of coupling

- high
- the following are in order of the "tightness" of the coupling from highest to lowest

#### Content coupling

- high
- when one module accesses the private data/methods of another module
- should always be avoided

#### Common coupling

- high
- when modules share the same global data
- should always be avoided
- static read only data is fine though

#### External coupling

- high
- when modules share the same part of an environment external to the software
- e.g. sharing external data format, interface, communication format, tool, or device
- sometimes this is unavoidable, but we should still try to avoid this if possible

#### Control coupling

- moderate
- when one module controls the internal logic of the other by passing information
- e.g. a control flag
- testing both modules together should be done frequently

#### Stamp coupling

- low
- data structure coupling
- when modules share a composite data structure
- not all of the values may be used in this "contract"

#### Data coupling

- low
- when modules share some data which are just primitive data values
- e.g. when a module calls a method on another module, and inputs and outputs are shared in the form of method parameters and the return value
- emphasis on *primitive*
- all inputs are used
- if some aren't used, they should be removed

#### Message coupling

- lowest
- when one module calls a method on another module and does not send any parameters

#### No coupling

- no direct communication at all
- the modules can be implemented, tested, and maintained independently

### LoD (The Law of Demeter) / principle of least knowledge

- modules should know the least amount as possible about other modules
- limit the communication between modules ideally to only things it owns and things it *needs* to know about

## Cohesion

### High cohesion
