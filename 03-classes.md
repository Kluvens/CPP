## Scopes

The scope of a variable is the part of the program where it is accessible

### New scopes
- if statements
- switch statements
- loop
- compound statements
- function bodies
- class types
- namespaces
- global scope

### Namespaces
- used to
  - group related names together
  - modularise code
- can only be created 
  - at global scope
  - at namespace scope
  - entities inside accessed with the scope operator `::`

### Object lifecycle in C++
- an object is a piece of memory of a specific type that holds some data
  - all variables are objects
  - this does not add overhead because objects are stack-allocated by default
- object lifetime starts when it comes in scope
  - memory for the object is first allocated
  - then it is formally constructed
  - every type has at least one constructor that says how to initialise it
