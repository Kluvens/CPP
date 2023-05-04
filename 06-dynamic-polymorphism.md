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

### Abstract base classes (ABC)
- might want to deal with a base class, but the base class by itself does not make sense
- might want some default behavior and data, but derived classes should fill out the rest of the behaviour
- if a class has at least one pure virtual method, the class is abstract and cannot be constructed

### The Deadly diamond of Death
- C++ supports multiple inheritance
- in deep hierarchies, it is possible for a derived class's ancestors to inherit from the same class
- C++ solves this via virtual inheritance

### Virtual inheritance
- if a derived class inherits virtually from a base class, it is guaranteed to only have one copy of any shared ancestors
- unqualified calls to member functions with name collisions go through overload resolution as if those member functions were declared virtual
- without virtual inheritance, the call would be ambiguous

``` cpp
struct B { int n; };
class X : public virtual B {};
class Y : virtual public B {};
class Z : public B {};
  // every A has one X, one Y, one Z, and two B's:
  // - one that is the base of Z
  // - and one that is shared by X and Y
struct A : X, Y, Z {
  A() {
    // modifies the virtual B subobject's member
    X::n = 1;
    // modifies the same virtual B subobject's member
    Y::n = 2;
    // modifies the non-virtual B subobject's member
    Z::n = 3;
    // prints 223
    std::cout << X::n << Y::n << Z::n << '\n';
  }
};

struct M { void f(); };
struct B1: virtual M { void f(); };
struct B2: virtual M {};

struct C : B1, B2 {
  void foo() {
  X::f(); // OK, calls X::f (qualified lookup)
  f(); // OK, calls B1::f (unqualified lookup as if virtual)
  }
};
```
