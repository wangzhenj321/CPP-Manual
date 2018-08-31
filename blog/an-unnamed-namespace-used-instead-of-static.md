**Table of Contents**

[static vs global](#static-vs-global)

[internal linkage vs external linkage](#internal-linkage-vs-external-linkage)

[an unnamed namespace used instead of static](#an-unnamed-namespace-used-instead-of-static)

[scope resolution operator without a scope](#scope-resolution-operator-without-a-scope)

[References](#References)


## static vs global

```c++
#include <stdio.h>
#include <stdlib.h>

static int i;
int j;

int main ()
{
    //Some implementation
}
```

`i` has **internal linkage** so you can't use the name `i` in other source files (strictly **translation units**) to refer to the same object.

`j` has **external linkage** so you can use `j` to refer to this object if you declare it extern in another translation unit.


## internal linkage vs external linkage

When you write an implementation file (`.cpp`, `.cxx`, etc) your compiler generates a **translation unit**. This is the object file from your implementation file plus all the headers you `#include`d in it.

- **Internal linkage** refers to everything only **in scope of a translation unit**.

- **External linkage** refers to things that exist beyond a particular translation unit. In other words, **accessible through the whole program**, which is the combination of all translation units (or object files).

You can explicitly control the linkage of a symbol by using the `extern` and `static` keywords. If the linkage isn't specified then the default linkage is `extern` for non-const symbols and `static` (internal) for const symbols.

```c++
// in namespace or global scope
int i;                // extern by default
const int ci;         // static by default
extern const int eci; // explicitly extern
static int si;        // explicitly static

// the same goes for functions (but there are no const functions)
int foo();            // extern by default
static int bar();     // explicitly static 
```

Note that instead of using `static` for internal linkage it is better to use **anonymous namespaces** into which you can also put classes. The linkage for anonymous namespaces has changed between C++98 and C++11 but the main thing is that they are unreachable from other translation units.

```c++
namespace {
   int i; // external linkage but unreachable from other translation units.
   class invisible_to_others { };
}
```


## an unnamed namespace used instead of static

In C++, some uses of the `static` keyword have been deprecated. In particular, an **unnamed namespace** should be favored over some previous uses of "file scope static's". In fact in some cases an unnamed namespace must be used in order to obtain a similar effect. That is to say, this code:

```c++
// x.cpp
static bool flag = false; // AAA

void foo() { if (flag)... }
void bar() { ...flag = true... }
```

should instead often be composed this way in C++:

```c++
// x.cpp
namespace /* NOTHING HERE!! */ { // BBB
    bool flag = false; // no need for static here
}
```

The use of `static` in AAA indicates that *flag* has **internal linkage**. This means that *flag* is local to its translation unit (that is, effectively it is only known by its name in some source file, in this case *x.cpp*). This means that *flag* can't be used by another translation unit (by its name at least). The goal is to have less global/cross-file name pollution in your programs while at the same time achieving some level of encapsulation. Such a goal is usually considered admirable and so therefore is often considered desirable (note that the goal, not the code, is being discussed in this sentence).

Contrast this to BBB. In the case of using the unnamed namespace above, *flag* has **external linkage**, yet it is effectively local to the translation unit. It is effectively still local because although we did not give the namespace a name, the compiler generated a unique name for it. In effect, the compiler changes BBB into this:

```c++
// Just get UNIQUE established
namespace UNIQUE { } // CCC
// Bring UNIQUE into this translation unit
using namespace UNIQUE;
// Now define UNIQUEs members
namespace UNIQUE {
    bool flag = false; // As Before
}
```

For each translation unit, a uniquely generated identifier name for *UNIQUE* somehow gets synthesized by the compiler, with the effect that no other translation unit can see names from an unnamed namespace, hence making it local even though the name may have external linkage. Therefore, although *flag* in CCC has external linkage, its real name is *UNIQUE::flag*, but since *UNIQUE* is only known to *x.cpp*, it's effectively local to *x.cpp* and is therefore not known to any other translation unit.


## scope resolution operator without a scope

It means global scope. You might need to use this operator when you have conflicting functions or variables in the same scope and you need to use a global one. You might have something like:

```c++
void bar();    // this is a global function

class foo {
    void some_func() { ::bar(); }    // this function is calling the global bar() and not the class version
    void bar();                      // this is a class member
};
```

If you need to call the global `bar()` function from within a class member function, you should use `::bar()` to get to the global version of the function.


## References

1. [Static vs global](https://stackoverflow.com/questions/2271902/static-vs-global)

2. [What is external linkage and internal linkage?](https://stackoverflow.com/questions/1358400/what-is-external-linkage-and-internal-linkage#1358796)

3. [Why is an unnamed namespace used instead of static?](http://www.comeaucomputing.com/techtalk/#nostatic)

4. [scope resolution operator without a scope](https://stackoverflow.com/questions/75213/scope-resolution-operator-without-a-scope)
