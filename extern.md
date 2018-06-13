## Part 1

***References:*** https://stackoverflow.com/questions/10422034/when-to-use-extern-in-c

### Answer 1

This comes in useful when you have global variables. You declare the existence of global variables in a header, so that each source file that includes the header knows about it, but you only need to “define” it once in one of your source files.

To clarify, using `extern int x;` tells the compiler that an object of type `int` called `x` exists somewhere. It's not the compilers job to know where it exists, it just needs to know the type and name so it knows how to use it. Once all of the source files have been compiled, the linker will resolve all of the references of `x` to the one definition that it finds in one of the compiled source files. For it to work, the definition of the `x` variable needs to have what's called “external linkage”, which basically means that it needs to be declared outside of a function (at what's usually called “the file scope”) and without the `static` keyword.

**header:**

```c++
#ifndef HEADER_H
#define HEADER_H

// any source file that includes this will be able to use "global_x"
extern int global_x;

void print_global_x();

#endif
```

**source 1:**

```c++
#include "header.h"

// it needs to be defined somewhere
int global_x;

int main()
{
    //set global_x here:
    global_x = 5;

    print_global_x();
}
```

**source 2:**

```c++
#include <iostream>
#include "header.h"

void print_global_x()
{
    //print global_x here:
    std::cout << global_x << std::endl;
}
```

---

**Q:** So, if i declare a global variable in an header file without the extern keyword, the source files that include the header does not see it?

**A:** No, something worse happens. Each source file that includes the header will have its own variable, so each source file will compile independently but the linker will complain because two source files will have the same global identifiers.

---

### Answer 2

It is useful when you share a variable between a few modules. You define it in one module, and use extern in the others.

For example:

in file1.cpp:

```c++
int global_int = 1;
```

in file2.cpp:

```c++
extern int global_int;
//in some function
cout << "global_int = " << global_int;
```

## Part 2
