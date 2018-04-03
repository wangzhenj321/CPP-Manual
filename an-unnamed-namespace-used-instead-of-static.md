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

## Why is an unnamed namespace used instead of static?

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

The use of static in AAA indicates that *flag* has internal linkage. This means that *flag* is local to its translation unit (that is, effectively it is only known by its name in some source file, in this *case x.cpp*). This means that *flag* can't be used by another translation unit (by its name at least). The goal is to have less global/cross-file name pollution in your programs while at the same time achieving some level of encapsulation. Such a goal is usually considered admirable and so therefore is often considered desirable (note that the goal, not the code, is being discussed in this sentence).

Contrast this to BBB. In the case of using the unnamed namespace above, *flag* has external linkage, yet it is effectively local to the translation unit. It is effectively still local because although we did not give the namespace a name, the compiler generated a unique name for it. In effect, the compiler changes BBB into this:

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

For each translation unit, a uniquely generated identifier name for *UNIQUE* somehow gets synthesized by the compiler, with the effect that no other translation unit can see names from an unnamed namespace, hence making it local even though the name may have external linkage.
Therefore, although *flag* in CCC has external linkage, its real name is *UNIQUE::flag*, but since *UNIQUE* is only known to *x.cpp*, it's effectively local to *x.cpp* and is therefore not known to any other translation unit.

## References

1. [Static vs global](https://stackoverflow.com/questions/2271902/static-vs-global)
2. [Why is an unnamed namespace used instead of static?](http://www.comeaucomputing.com/techtalk/#nostatic)
