## Exceptions
- Exceptions are for exceptional circumstances
- exception handling
  - run-time mechanism
  - code downstream detects anomalies and throws an appropriate exception
  - upstream code catches the exception, handles it, and potentially rethrows it

### Conceptual structure
- exceptions are just data that is thrown up the stack when an error is detected
- code that can throw is placed in a `try` block
- code to handle a thrwon exception is placed in a `catch` block
- stack unwinds until an appropriate catch block is found

### Catching the right way
- throw by value, catch by const reference
- references are preferred because
  - more efficient, less copying
  - no slicing problem

### Rethrowing
- when an exception is caught, by default that catch block will be the only part of the code with access to the exception
- to give other catch blocks upstream access to the exception, can `throw` again

``` cpp
try {
  try {
    std::cout << “oops” << std::endl;
    throw 6771;
  } catch (const int &x) {
    std::cout << “exception detected.”
              << “rethrowing\n”;
    throw x;
  }
} catch (const int &i) {
  std::cout << “i: ” << i << std::endl;
}
//changing exception type
```

### Multiple catch blocks
- each catch block argument is matched against the current exception until an appropriate type is found
- catch(...) should almost always be last

``` cpp
#include <iostream>
#include <vector>

int main() {
  auto items = std::vector<int>{};
  try {
    items.resize(items.max_size() + 1);
  } catch (const std::bad_alloc &e) {
    std::cout << "Out of bounds.\n";
  } catch (const std::exception&) {
    std::cout << "General exception.\n";
  } catch (...) {
    std::cout << "Even more general.\n";
  }
}
```

### noexcept: asking if the exception can not throw
- specifies whether a function could potentially throw
- querying at compile time never to emit an exception
- it doesn't actually prevent a function from throwing an exception
- if a noexcept function throws, std::terminate is called
- compiler can sometimes make optimisations if it knows a function is noexcept

### Stack unwinding
- stack unwinding is the automatic process of poping stack frames until an appropriate handler for an exception is found
- once a found catch block successfully exits without rethrowing, stack unwinding is complete
- if it catches by value, its formal parameter is initialized by copying initialized object. If it catches by reference,
the parameter is initialized to refer to the exception object, then unwinding
- if another exception is thrown during stack unwinding before a catch block is found, std::terminate is called

### Exception performance
- exceptions are for exceptional circumstances
  - they should not be used for control flow if a better alternative exsits
- performance-critical code largely does not use exceptions
