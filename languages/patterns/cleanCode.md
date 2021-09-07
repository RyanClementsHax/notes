# Clean Code

- every line of code in a function should be one level of abstraction below the abstraction level of the function name
- provide enough abstraction in code that you write so that the reader can "bail out early" if they don't care about certain details of implementation
- clean code reads like good prose
- make sure your code is least surprising
- people are measured to only be able to hold 7 things in short term memory at a time
  - as a result, make sure any code you write can be read with only keeping 7 things or less in your head
- design patterns
  - although complex at times, design patterns are so ubiquitous that using them communicates intent, function, and internals very well

## Side effects

- they tend to come in pairs
  - acquire lock, release lock
  - open file, close file
  - allocate memory, deallocate memory
- forcing the user to deal with remembering to do this is fraught with problems
- if your language allows it, pass in a lambda that processes the resource to a function that handles the side effect twins for you

## Comments

- comments all too easily fade into the background and rust
- comments don't make up for bad code and are not type checked
- never talk about other code in a comment (can't guarantee that code will exist)
