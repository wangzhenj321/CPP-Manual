#### Table of Contents

[Part 1: default member values](#part-1-default-member-values)

[Part 2: use member initialization list](#part-2-use-member-initialization-list)

[Part 3: new C++11 initialization forms](#part-3-new-c11-initialization-forms)


# Part 1: default member values

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

1. If a class member is always initialized with the same initial value, then you should make the ***initializer inline*** (= ***class member initializer***), so as to avoid duplication.

2. If the initial value depends on the constructor, then put it in the ***constructor initializer list*** (= ***member initialization list***). (And never use assignment in the way you did.)

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


# Part 2: use member initialization list

## Question

I'm partial to using member initialization lists with my constructors... but I've long since forgotten the reasons behind this...

Do you use member initialization lists in your constructors? If so, why? If not, why not?

## Answer

For **POD** class members, it makes no difference, it's just a matter of style. ***For class members which are classes, then it avoids an unnecessary call to a default constructor.*** Consider:

```c++
class A
{
public:
    A() { x = 0; }
    A(int x_) { x = x_; }
    int x;
};

class B
{
public:
    B()
    {
        a.x = 3;
    }
private:
    A a;
};
```

In this case, the constructor for `B` will call the default constructor for `A`, and then initialize `a.x` to 3. A better way would be for `B`'s constructor to directly call `A`'s constructor in the initializer list:

```c++
B()
  : a(3)
{
}
```

This would only call `A`'s `A(int)` constructor and not its default constructor. In this example, the difference is negligible, but imagine if you will that `A`'s default constructor did more, such as allocating memory or opening files. You wouldn't want to do that unnecessarily.

***Furthermore, if a class doesn't have a default constructor, or you have a const member variable, you must use an initializer list:***

```c++
class A
{
public:
    A(int x_) { x = x_; }
    int x;
}

class B
{
public:
    B() : a(3), y(2)  // 'a' and 'y' MUST be initialized in an initializer list;
    {                 // it is an error not to do so
    }
private:
    A a;
    const int y;
};
```

> For members of fundamental types, it makes no difference which of the ways above the constructor is defined, because they are not initialized by default, ***but for member objects (those whose type is a class), if they are not initialized after the colon, they are default-constructed.***

## References

1. [Why should I prefer to use member initialization list?](https://stackoverflow.com/questions/926752/why-should-i-prefer-to-use-member-initialization-list)


# Part 3: new C++11 initialization forms



