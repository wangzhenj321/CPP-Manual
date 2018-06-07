***References:***

1. https://blog.csdn.net/bigheadyushan/article/details/77828949
2. http://linuxtools-rst.readthedocs.io/zh_CN/latest/tool/gdb.html

## 1. 简介

GDB是一个由GNU开源组织发布的、UNIX/LINUX操作系统下的、基于命令行的、功能强大的程序调试工具。其功能强大，现描述如下： 

1. 启动你的程序，可以按照你的自定义的要求随心所欲的运行程序。 
2. 可让被调试的程序在你所指定的调置的断点处停住。（断点可以是条件表达式） 
3. 当程序被停住时，可以检查此时你的程序中所发生的事。 
4. 动态的改变你程序的执行环境。

## 2. 生成调试信息

一般来说GDB主要调试的是C/C++的程序。要调试C/C++的程序，首先在编译时，我们必须要把调试信息加到可执行文件中。使用编译器（cc/gcc/g++）的`-g`参数可以做到这一点。如：

```c++
gcc -g hello.c -o hello
g++ -g hello.cpp -o hello
```

如果没有`-g`，你将看不见程序的函数名、变量名，所代替的全是运行时的内存地址。当你用`-g`把调试信息加入之后，并成功编译目标代码以后，让我们来看看如何用gdb来调试他。

## 3. 启动GDB的方法

1. `gdb program`

    program 也就是你的执行文件，一般在当前目录下。

2. `gdb program core`

    用gdb同时调试一个运行程序和core文件，core是程序非法执行后core dump后产生的文件。

3. `gdb program 1234`

    如果你的程序是一个服务程序，那么你可以指定这个服务程序运行时的进程ID。gdb会自动attach上去，并调试他。program应该在PATH环境变量中搜索得到。

## 4. gdb常用交互命令

### 
