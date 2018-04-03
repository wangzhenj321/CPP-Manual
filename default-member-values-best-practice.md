## Question

Is it good practice when writing C++11 code to set default values for class members in the header file of the class?

Or is it better to do this in the constructor of the class?

EDIT:

I mean:

**foo.h**

```c++
#include <string>

using std::string;

class Foo{
    private:
        string greet = "hello";
    public:
        Foo();
};
```

VS

**foo.cpp** (of course with the necessary header file, but without the in-class initialization):

```c++
Foo::Foo(){
    greet = "hello";
}
```

Which one is better and why?

## Answer

If a class member is always initialized with the same initial value, then you should make the initializer inline, so as to avoid duplication. If the initial value depends on the constructor, then put it in the constructor initializer list. (And never use assignment in the way you did.)

Example:

```c++
class Foo
{
    bool done = false;   // always start like this
    int qty;
    Bar * p;

public:
    Foo()                        : qty(0),              p(nullptr)    { }
    Foo(int q, Bar * bp)         : qty(q),              p(bp)         { }
    explicit Foo(char const * s) : qty(std::strlen(s)), p(new Bar(s)) { }

    // ...
};
```

In this hypothetical example, the member `done` is always required to start as `false`, so it's best to write the initializer inline. The other two members, `qty` and `p`, can be initialized differently in each of three different constructors, so they are initialized inside the constructors' initializer lists.

## References

1. [Default member values best practice](https://stackoverflow.com/questions/11594846/default-member-values-best-practice)
