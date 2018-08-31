#### Table of Contents

[Part 1: default member values best practice](#part-1-default-member-values-best-practice)

[Part 2: use member initialization list](#part-2-use-member-initialization-list)

[Part 3: new C++11 initialization forms](#part-3-new-c11-initialization-forms)

[Part 4: class member variables without initialization](#part-4-class-member-variables-without-initialization)


# Part 1: default member values best practice

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

## Brace-Initialization

C++11 attempts to overcome the problems of C++03 initialization by introducing a universal initialization notation that applies to every type—whether a POD variable, a class object with a user-defined constructor, a POD array, a dynamically allocated array, or even a Standard Library container. The universal initializer is called a ***brace-init***. It looks like this:

```c++
//C++11 brace-init
int a{0};
string s{"hello"};
string s2{s}; //copy construction
vector <string> vs{"alpha", "beta", "gamma"};
map<string, string> stars
 { {"Superman", "+1 (212) 545-7890"},
  {"Batman", "+1 (212) 545-0987"}};
double *pd= new double [3] {0.5, 1.2, 12.99};
class C
{
int x[4];
public:
C(): x{0,1,2,3} {}
};
```

Notice that unlike the traditional aggregate initializer of C and C++03, which uses braces after an equal sign (={}), the C++11 ***brace-init*** consists of a pair of braces (without the equal sign) in which the initializer(s) will be enclosed. ***An empty pair of braces indicates default initialization. Default initialization of POD types usually means initialization to binary zeros, whereas for non-POD types default initialization means default construction***:

```c++
//C++11: default initialization using {}
int n{}; //zero initialization: n is initialized to 0
int *p{}; //initialized to nullptr
double d{}; //initialized to 0.0
char s[12]{}; //all 12 chars are initialized to '\0'
string s{}; //same as: string s;
char *p=new char [5]{}; // all five chars are initialized to '\0'
```

## Class Member Initialization

C++11 pulls another rabbit out of its hat with ***class member initializers***. Perhaps an example will best illustrate these:

```c++
class C
{
int x=7; //class member initializer
public:
C();
};
```

The data member x is automatically initialized to 7 in every instance of class C. In former dialects of C++, you would use the more cumbersome ***mem-init*** notation for the same purpose:

```c++
class C
{
int x;
public:
C() : x(7) {}
};
```

C++11 class member initializers are mostly a matter of convenience. They provide an overt and simplified form of initializing data members. But class member initializers also let you perform a few tricks that have hitherto been impossible. For example, you can use a class member initializer to initialize a member array:

```c++
class C
{
int y[5] {1,2,3,4};
public:
C();
};
```

Notice that a class member initializer can consist of any valid initialization expression, whether that's the traditional equal sign, a pair of parentheses, or the new brace-init:

```c++
class C
{
string s("abc");
double d=0;
char * p {nullptr};
int y[5] {1,2,3,4};
public:
C();
};
```

***Regardless of the initialization form used, the compiler conceptually transforms every class member initializer into a corresponding mem-init.*** Thus, class C above is semantically equivalent to the following class:

```c++
class C2
{
string s;
double d;
char * p;
int y[5];
public:
C() : s("abc"), d(0.0), p(nullptr), y{1,2,3,4} {}
};
```

***Bear in mind that if the same data member has both a class member initializer and a mem-init in the constructor, the latter takes precedence.*** In fact, you can take advantage of this behavior by specifying a default value for a member in the form of a class member initializer that will be used if the constructor doesn't have an explicit mem-init for that member. Otherwise, the constructor's mem-init will take effect, overriding the class member initializer. This technique is useful in classes that have multiple constructors:

```c++
class C
{
int x=7; //class member initializer
C(); //x is initialized to 7 when the default ctor is invoked
C(int y) : x(y) {} //overrides the class member initializer
};
C c; //c.x = 7
C c2(5); //c.x = 5
```

## References

1. [Get to Know the New C++11 Initialization Forms](http://www.informit.com/articles/article.aspx?p=1852519)


# Part 4: class member variables without initialization

## Avoiding uninitialized members

***If class members are neither mentioned in a constructor’s member initializer list nor have a *brace-or-equal-initializer*, then they get default-initialized. That means, that for class types the default constructor is called, but for any other types like enums or built in types like int, double, pointers, no initialization happens at all. No initialization means your member variables possibly contain garbage values.***

```c++
struct Trivial { 
  int k; 
private: 
  int l; 
};

struct Problematic {
 vector<int> vi;
 int u;
 Trivial t;

 Problematic() = default;
};
```

A default constructed object of type `Problematic` is, in fact, problematic, because neither its member `u` nor the members of `t` will be initialized with any meaningful value. Only `vi` has a nontrivial default constructor and therefore will be initialized correctly to represent an empty vector.

## References

1. [Modern C++ Features – Default Initializers for Member Variables](https://arne-mertz.de/2015/08/new-c-features-default-initializers-for-member-variables/)
