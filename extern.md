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

## Part 2 `extern "c"`

最早的标准C编译器编译引用出来的变量和函数就是在名字前面加个下滑杠，比如`void foo(int a,int b);`编译器引用的函数名是`_foo`，后来的C编译器都是遵循这个标准。可是后来C++出现了，他的重载特性使得不同的编译器对它进行了不同的处理，比如`void foo(int a,int b);`就可能被编译器引用出来后变为`_foo_int_int`之类的，把函参也放进函数名中，从而实现了重载。为了保证不同厂家生产的模块之间的兼容性，并且可以调用原先开发好的C模块，就引出了一个`extern "C"`的方法，通过这种定义来告诉编译器：请按照C的方法来对我这个函数进行编译，保持我的名称。这样不同厂家的C和C++中的变量，函数，类就得到了一致化的处理，兼容性也就解决了。

***References:*** https://stackoverflow.com/questions/1041866/what-is-the-effect-of-extern-c-in-c

extern "C" makes a function-name in C++ have 'C' linkage (compiler does not mangle the name) so that client C code can link to (i.e use) your function using a 'C' compatible header file that contains just the declaration of your function. Your function definition is contained in a binary format (that was compiled by your C++ compiler) that the client 'C' linker will then link to using the 'C' name.

Since C++ has overloading of function names and C does not, the C++ compiler cannot just use the function name as a unique id to link to, so it mangles the name by adding information about the arguments. A C compiler does not need to mangle the name since you can not overload function names in C. When you state that a function has extern "C" linkage in C++, the C++ compiler does not add argument/parameter type information to the name used for linkage.

Just so you know, you can specify "C" linkage to each individual declaration/definition explicitly or use a block to group a sequence of declarations/definitions to have a certain linkage:

```c++
extern "C" void foo(int);
extern "C"
{
   void g(char);
   int i;
}
```

If you care about the technicalities, they are listed in section 7.5 of the C++03 standard, here is a brief summary (with emphasis on extern "C"):

- extern "C" is a linkage-specification
- Every compiler is required to provide "C" linkage
- a linkage specification shall occur only in namespace scope
- Only function names and variable names with external linkage have a language linkage
- two function types with distinct language linkages are distinct types even if otherwise identical
- linkage specs nest, inner one determines the final linkage
- extern "C" is ignored for class members
- at most one function with a particular name can have "C" linkage (regardless of namespace)
- 'static' inside 'extern "C"' is valid; an entity so declared has internal linkage, and so does not have a language linkage
- Linkage from C++ to objects defined in other languages and to objects defined in C++ from other languages is implementation-defined and language-dependent. Only where the object layout strategies of two language implementations are similar enough can such linkage be achieved
