***References:*** https://stackoverflow.com/questions/13889467/should-i-include-xxxx-h-or-cxxxx-in-c-programs

## Question

- What should I include in C++ programs, `stdio.h` or `cstdio`? and Why?
- Why two header files which provide the same functionality?
- What does the standard say regarding this?
- How should I go about including other such headers, Is there a base rule that I should follow?

## Answer

Consider the following programs:

***Sample 1:***

```c++
#include<stdio.h>

int main()
{
    printf("Hello World");
    return 0;
}
```

***Sample 2:***

```c++
#include<cstdio>

int main()
{
    printf("Hello World");
    return 0;
}
```

Both work as expected. ***So which usage is more appropriate?*** The answer is: ***Neither!*** Surprised? Read on.

The C++ Standard library provides all standard C headers for compatibility reason, while C++ as a language also provides all the equivalent headers. As a convention,

- No C++ standard library headers(apart from ones include for C compatibility) have any file extensions, and
- All C++ equivalent of C headers begin with `cxxxxx`.

The C++ Standard mentions this under *Annex D (normative) Compatibility features*:

![](img/xxxxh-vs-cxxxx/fig1.png?raw=true)

§2 mentions the important distinguishing point. This rule applied to the examples above means:

- Including ***cstdio*** imports the symbol names in the std namespace and ***possibly*** in the Global namespace.
- Including ***stdio.h*** imports the symbol names in the Global namespace and ***possibly*** in the std namespace.

Let us apply this rule to our sample codes and measure the pros and cons:

***Sample 1:*** This brings all the symbols from stdio.h in the global namespace. Advantage is that you can use the symbols without any qualification since they are imported in the global namespace. Downside is that you end up polluting the global namespace with many symbol names that you will probably never use. This might lead to symbol name collision. In C++ always consider the global namespace as a minefield and avoid it as much as possible.

***Sample 2:*** This is a very bad practice because there is no guarantee that the implementation will put the symbols in global namespace, the standard simply does not demand to do so. We are simply relying on the behavior of one particular compiler implementation. We cannot and should not assume that all compilers will do so. So strictly speaking the program is not standard approved and this usage is not portable across all implementations.

### So what is the correct usage?

The correct usage is to use `cstdio` and fully qualify the symbol names or else bring them in scope with ***using declarations***. This guarantees all symbols we use are present in `std` namespace and we are not polluting the global namespace. Example of correct usage:

***Sample 3:***

```c++
#include<cstdio>

using std::printf;

int main()
{
    printf(“Hello World”);
    return 0;
}
```

Note that the directive [`using namespace std;`, especially in a header, is not a good option](https://stackoverflow.com/questions/1452721/why-is-using-namespace-std-considered-bad-practice) and you should always use `using` declarations.

Note that we consider `stdio.h` vs. `cstdio` here just a sample use case, in practice it applies to most `cxxxx` and `xxxx.h` headers, [except a few like `<math.h>` and `<cmath>`](https://stackoverflow.com/questions/8734230/math-interface-vs-cmath-in-c/8734292#8734292).
