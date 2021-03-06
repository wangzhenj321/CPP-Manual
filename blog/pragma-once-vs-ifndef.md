# Part 1

```c++
#ifndef HEADERFILE_H
#define HEADERFILE_H
...
#endif
```

Those are called [#include guards](https://en.wikipedia.org/wiki/Include_guard).

Once the header is included, it checks if a unique value (in this case `HEADERFILE_H`) is defined. Then if it's not defined, it defines it and continues to the rest of the page.

When the code is included again, the first `ifndef` fails, resulting in a blank file.

**That prevents double declaration of any identifiers such as types, enums and static variables.**

## References

1. [Why are #ifndef and #define used in C++ header files?](https://stackoverflow.com/questions/1653958/why-are-ifndef-and-define-used-in-c-header-files) 


# Part 2

为了避免同一个文件被`include`多次，C/C++中有两种方式，一种是`#ifndef`方式，一种是`#pragma once`方式。在能够支持这两种方式的编译器上，二者并没有太大的区别，但是两者仍然还是有一些细微的区别。

**方式一：**

```c++
#ifndef __SOMEFILE_H__
#define __SOMEFILE_H__
... ... // 声明、定义语句
#endif
```

**方式二：**

```c++
#pragma once
... ... // 声明、定义语句
```

`#ifndef`的方式受C/C++语言标准支持。它不光可以保证同一个文件不会被包含多次，也能保证内容完全相同的两个文件（或者代码片段）不会被不小心同时包含。当然，缺点就是如果不同头文件中的宏名不小心“撞车”，可能就会导致你看到头文件明明存在，编译器却硬说找不到声明的状况——这种情况有时非常让人抓狂。由于编译器每次都需要打开头文件才能判定是否有重复定义，因此在编译大型项目时，`ifndef`会使得编译时间相对较长，因此一些编译器逐渐开始支持`#pragma once`的方式。

`#pragma once`一般由编译器提供保证：同一个文件不会被包含多次。注意这里所说的“同一个文件”是指物理上的一个文件，而不是指内容相同的两个文件。你无法对一个头文件中的一段代码作`pragma once`声明，而只能针对文件。其好处是，你不必再费劲想个宏名了，当然也就不会出现宏名碰撞引发的奇怪问题。大型项目的编译速度也因此提高了一些。对应的缺点就是如果某个头文件有多份拷贝，本方法不能保证他们不被重复包含。当然，相比宏名碰撞引发的“找不到声明”的问题，这种重复包含很容易被发现并修正。

`#pragma once`方式产生于`#ifndef`之后，因此很多人可能甚至没有听说过。目前看来`#ifndef`更受到推崇。因为`#ifndef`受C/C++语言标准的支持，不受编译器的任何限制；而`#pragma once`方式却不受一些较老版本的编译器支持，一些支持了的编译器又打算去掉它，所以它的兼容性可能不够好。一般而言，当程序员听到这样的话，都会选择`#ifndef`方式，为了努力使得自己的代码“存活”时间更久，通常宁愿降低一些编译性能，这是程序员的个性，当然这是题外话啦。

还看到一种用法是把两者放在一起的：

```c++
#pragma once
#ifndef __SOMEFILE_H__
#define __SOMEFILE_H__
... ... // 声明、定义语句
#endif
```

看起来似乎是想兼有两者的优点。不过只要使用了`#ifndef`就会有宏名冲突的危险，也无法避免不支持`#pragma once`的编译器报错，所以混用两种方法似乎不能带来更多的好处，倒是会让一些不熟悉的人感到困惑。选择哪种方式，应该在了解两种方式的情况下，视具体情况而定。只要有一个合理的约定来避开缺点，我认为哪种方式都是可以接受的。而这个已经不是标准或者编译器的责任了，应当由程序员自己或者小范围内的开发规范来搞定。

## References

1. [#pragma once 与 #ifndef 解析](http://www.cnblogs.com/hokyhu/archive/2009/03/30/1425604.html)
