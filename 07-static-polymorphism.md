## Static polymorphism

### Function templates
- a blueprint for the compiler to synthesis particular instances of a function varying by type

``` cpp
#include <iostream>
template <typename T>
auto add_or_concat(const T &a, const T &b) -> T {
  return a + b;
}

int main() {
  // prints 3
  std::cout << add_or_concat(1, 2) << std::endl;
  
  // prints "hello world"
  std::string h = "hello", w = "world";
  std::cout << add_or_concat(h, w) << std::endl;
  
  // code is only generated for int and std::string
  // no other type was used, so no other version
  // was instantiated.
  return 0;
}
```
