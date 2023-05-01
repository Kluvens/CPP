## Dynamic Polymorphism

### Inheritance in C++
- C++ supports multiple inheritance for all class-types
- inheritance kind depends on the inheritance access specifier
- implementation vs. interface inheritance exists
- base class or derived class relationship expressed through inheritance

``` cpp
#include <iostream>
#include <string>

struct hello {
  std::string msg1 = "hello!";
};

struct world {
  std::string msg2 = "world!";
};

class child : public hello, public world {
public:
  auto greeting() const -> void {
    std::cout <<
    msg1 + " " + msg2 <<
    std::endl;
  }
};

int main() {
  // prints "hello world!"
  child{}.greeting();
}
```

### Inheritance: interface vs implementation
- interface inheritance is when only the interface of methods are intended to be inheritend
- implementation inheritance is when the implementation are intended to be inherited
- implemnetation inheritance uses private inheritance
- anything other than private is interface inheritance

``` cpp
#include <iostream>>
struct base {
  auto greeting() const -> void {
    std::cout << msg << std::endl;
  }
  
  std::string msg = "hi!";
};

struct interface : public base {
  // no need to remake greeting()
};

struct implementation : private base {
    // need to re-create the greeting method
    auto greeting() const -> void {
    std::cout << msg + " world!\n";
  }
};

int main() {
  interface{}.greeting(); // prints "hi!"
  implementation{}.greeting(); // prints "hi, world!"
}
```

### Inheritance: construction
- each base class is contructed first, then this's data members, then the of the constructor run

### Inheritance: destruction
- first, this's desctor runs, then the destructors of this's data members run in the reverse order of declaration. then the destructor of this's base classes run in the reverse order of inheritance

### virtual keyword
- use the virtual keyword in the base class if you want to modify the methods in derived classes

### the override keyword
- tells the compiler this method overrides a virtual function in a base class

### final keyword
- specifies to the compiler that a method cannot be overridden in a derived class

### pure virtual methods
- a pure virtual function specifies a function that a class must override

### rules for virtual methods
- virtual member functions cannot be static
- virtual member functions cannot be friends
- virtual member functions only exhibit dynamic binding when used through a pointer or reference
- the prototype of virtual functions must be the same in the base as well as derived class
- they are always declared in the base class and overridden in the derived class
- a class must have a virtual destructor but it cannot have a virtual constructor
