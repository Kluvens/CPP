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

### Nested namespaces
- it is possible to define a namespace within another namespace
- prefer top-level and occasionally two-tier namespaces to multi-tier

``` cpp
// in std.h
namespace std {
  auto cout = /* definition */;
  namespace chars {
    char get_char();
  }
}

// in std.cpp
// legal syntax since C++17
namespace std::chars {
  char get_char() { return ‘c’; }
}

// main.cpp
int main() {
  std::cout << std::chars::get_char()<<“\n”;
}
```

### Inline namespaces
- it is possible to define nested namespaces as being inline
- this injects all names inside the nested namespace into the enclosing one

``` cpp
namespace std {
  inline namespace curr {
    class vector {
      /* current interface of vector */
    }
  }
  namespace cpp98 {
    class vector {
      /* old interface of vector */
    }
  }
}

std::vector v1; // default: std::curr::vector
std::curr::vector v2; // same as above
std::cpp98::vector v3; // opt-in to old version
```

### Namespace using-directives
- it is possible to inject all or some names in a namespace into the enclosing scope using a `using` directive
- this should be avoided as much as possible to prevent name collisions

``` cpp
// potentially catastrophic
// using namespace std;

int main() {
  using std::vector;
  vector<int> v = {1, 2, 3};
  
  if (v.size()) {
    using namespace std::chrono;
    auto txt = millisecond{500};
  } else {
    // this won’t work: different scope
    auto txt2 = millisecond{500};
  }
}
```

### Anonymous namespaces
- in C we had static functions that made functions local to a file
- C++ uses anonymous namespaces to achieve the same effect
- functions that you don't want in your public interface should be put into anonymous namespaces
- any names inside an anonymous namespace are injected into the enclosing scope automatically

### Object lifecycle in C++
- an object is a piece of memory of a specific type that holds some data
  - all variables are objects
  - this does not add overhead because objects are stack-allocated by default
- object lifetime starts when it comes in scope
  - memory for the object is first allocated
  - then it is formally constructed
  - every type has at least one constructor that says how to initialise it

### Object construction
- we generally use `()` to call functions, and `{}` to construct objects

``` cpp
void ex() {
  int i; // default ctor: uninitialised
  int i{}; // initialised with 0.
  int i{4}; // value-initialised to 4
  int i(4); // same as above.

  // is {1, 2} an initialiser list?
  // or a call to one of vector’s constructors?
  // this will be interpreted as an init list
  std::vector<int> v{1, 2};

  int *p; // default ctor: uninitialised.
  // p is known as a “wild” pointer
}
```

### Object destruction
- objects are destructed once they go out of scope
- an object goes out of scope when
  - local variables: the `}` marking the end of its scope is encountered
  - static or global variables: when the program ends
  - dynamically-allocated varaibles: when the programmer calls `free()` or `delete`

``` cpp
auto v = std::vector<int>{1, 2, 3};

int main() {
  int *i = new int{4};
  {
    std::string str = “hi!”;
  } // str is destructed
  
  delete i; // the int is destructed.
}
// i would NOT have been destructed
// at the end of main since it is
// dynamically allocated.
// It is our responsibility to delete it.

// v is destructed here at program end
```

## Classes

### Classes vs. Structs
- a class and a struct in C++ are almost exactly the same
- the only technical difference is that all members of a struct are public by default whereas all members of a class are private by default
- structs generally are used for simple types with few or no methods

### Member initialiser list
- the initialisation phase occurs before the body of the constructor is executed regardless of whether a member initialiser list is supplied
- this avoids initalising a data member only to have its value overwritten

``` cpp
class user {
public:
  user(std::string name, int age)
  // everything following the colon
  // is the member initialiser list
  : name_{name}, age_{age} {
    /* more complex set-up */
  }
  
  private:
    std::string name_;
    int age_;
};
```

### Delegating constructor
- a constructor may call another constructor from the member initialiser list
- since the other constructor must construct all the data members, you should not specify anything else in the list

``` cpp
class point2d {
public:
  // the default ctor delegates to the
  // 2-arg constructor, supplying
  // default values
  point2d() : point2d(0, 0) {}
  
  point2d(float x, float y)
  : x_{x}, y_{y} {}
  
private:
  float x_;
  float y_;
};
```

### In-class initialisers
- It can be easy to forget to initalise all data members, especially when there are many
- we can use in-class initialisers as a last resort fallback if a data member is not initialised with a member initisaliser list
- it is best to minimise their use, as having initialisation logic spread out can be hard to read

``` cpp
class point2d {
public:
  point2d(float x, float y)
  : x_{x}, y_{y} {}
  
private:
  // with no default constructor,
  // the in-class initialisers
  // will be used instead.
  
  float x_ = 0;
  float y_ = 0;
};
```

### explicit
- if a constructor for a class is callable with a single parameter, the compiler will create an implicit type conversion from the parameter to the class type
- you have to opt-out this implicit type conversion with the `explicit` keyword

``` cpp
class point2d {
public:
  // single arg parameter
  explicit point2d(float n)
  : x_{n}, y_{n} {}
  
  // this ctor is also callable with a single arg
  explicit point2d(float *f, int n = 2)
  : x_{f[0]}, y_{n >= 2 ? f[1] : f[0]} {}
  
private:
  float x_;
  float y_;
};

point2d ex() {
  // error: single arg constructor is explicit
  return 3.0f
  
  float arr[2] = {0.0f};
  // error: 2-arg constructor also is explicit
  return arr;
}
```

### Destructor
- called when the object goes out of scope

``` cpp
// from Java: a boxed integer
class integer {
public:
  integer(int i) : ptr_{new int{i}} {}
  
  // never forget to free a pointer
  // ever again!
  ~integer() {
    delete ptr_;
  }
  
private:
  int *ptr_;
}
```

### special member functions
There are six special member functions
- the default constructor
- the copy constructor
- the move constructor
- the destructor
- the copy-assignment operator
- the move-assignment operator

### synthesised special members
- the compiler is able to synthesise definitions if the user does not provide one
- it is possible to opt-into synthesis with `default`
- likewise, it is possible to opt-out of synthesis with `delete`

``` cpp
class point2d {
public:
  // even one user-supplied ctor stops
  // the compiler-generated default.
  point2d(float a, float b);
  point2d() = default; // opt back into it
  
  // now this class is no longer copyable
  point2d(const point2d &other) = delete;
  
  // ensure compiler-synthesised dtor.
  ~point2d() = default;
  
private:
  float x_;
  float y_;
};
```

### Non-static members
- a class can have member functions
- a class can also have data members
- the size of a class only depends on the types and declaration order of its members
  - due to alignment requirements, the compiler may insert padding into your class

``` cpp
class foo {
public:
  void speak();
private:
  int i;
  void *ptr;
  bool b;
};

class foo2 {
public:
  void speak();
 private:
  bool b;
  int i;
  void *ptr;
};

static_assert(sizeof(foo) == 24); // why?
static_assert(sizeof(foo2) == 16); // why?
```

### Incomplete types
- an incomplete type is a type that has been declared but not defined
- you can only from pointers and references to incomplete types
- because of this restriction, a class cannot have data members of its own type
- since a class is considered declared once its name has been seen, it can have pointer or reference members to its own type

``` cpp
struct node {
  int data;
  
  // node is incomplete.
  // This is invalid.
  // This would also make no sense.
  // What is sizeof(Node)??
  node next;
  
  // this, however, is fine.
  node *next;
  
  // this is fine, too.
  node &next;
};
```

### The this pointer
- member functions have an extra implicit parameter, named `this`
- this is a pointer to the object on behalf of which the function is called
- a member function does not explicitly define it, but may explicitly use it
- the compiler treats an unqualified reference to a class member as being made through the `this` pointer

### const Objects
- member functions are by default only callable by non-const objects
- you can define a const member function which is callable by both const and non-const objects
- a const member function may only modify mutable members

``` cpp
class point2i {
public:
  point2d(int a, int b) : x_{a}, y_{b} {}
  
  const int& x() const { return x_; }
  int &x() { return x_; }
  
  const int& y() const { return y_; }
  int &y() { return y_; }
private:
  int x_;
  int y_;
}

const auto p = point2i{1, 2};

p.x(); // OK! const-qualified method called
p.y() = 4; // error: calls a non-const method
```

### Static members
- static members belong to the class, as opposed to any particular object
- static methods are callable without any instance
- static methods are never const-qualified
- static data members' lifetime ends when the program ends

``` cpp
class point2d {
public:
  static point2d make_point(float a, float b) {
    return point2d(a, b);
}

  ~point2d() {
    n_live_ -= 1;
  }
private:
  point2d(float a, float b) : x_{a}, y_{b} {
    n_live_ += 1;
  }
  
  inline static int n_live_ = 0; // since C++17

  float x_;
  float y_;
};
```

### Friends
- a class may declare another function or class as a `friend`
- friends are able to access the private members of the class
- friends are always public

``` cpp
class point2d {
public:
  /* Other implementation details... */
  // declare distance as a friend of point2d
  friend float dist(point2d &, point2d &);

private:
  float x_;
  float y_;
}

float dist(point2d &l, point2d &r) {
  // because dist is a friend, it can access
  // the private members of point2d
  return std::sqrt(l.x_ * r.x_ + l.y_ * r.y_);
};
```

### Hidden friends
- it is possible to declare and define a friend inline in a class
- this hidden friend has different look up rules than usual
- mostly used with operator overloading

``` cpp
namespace hf {
  class point2d {
  public:
    // other implementation details...
    friend float dist(point2d &l, point2d &r) {
      return std::sqrt(l.x_ * r.x_ + l.y_ * r.y_);
    }
  private:
    float x_, y_;
  };
  
  float dist(point2d &, point2d &);
}

void ex() {
  hf::point2d p = {1, 2}, q = {3, 4};
  
  // OK: hidden friend found through ADL
  dist(p, q);
  
  // OK: found through 2nd declaration outside class
  hf::dist(p, q);
}
```
