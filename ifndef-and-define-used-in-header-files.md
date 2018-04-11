## Question

I have been seeing code like this usually in the start of header files:

```c++
#ifndef HEADERFILE_H
#define HEADERFILE_H
```

And at the end of the file is

```c++
#endif
```

What is the purpose of this?

## Answer

Those are called [#include guards](https://en.wikipedia.org/wiki/Include_guard).

Once the header is included, it checks if a unique value (in this case `HEADERFILE_H`) is defined. Then if it's not defined, it defines it and continues to the rest of the page.

When the code is included again, the first `ifndef` fails, resulting in a blank file.

That prevents double declaration of any identifiers such as types, enums and static variables.

## References

1. [Why are #ifndef and #define used in C++ header files?](https://stackoverflow.com/questions/1653958/why-are-ifndef-and-define-used-in-c-header-files)
