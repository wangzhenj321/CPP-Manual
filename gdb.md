***References:***

1. https://blog.csdn.net/bigheadyushan/article/details/77828949
2. http://linuxtools-rst.readthedocs.io/zh_CN/latest/tool/gdb.html
3. https://sourceware.org/gdb/onlinedocs/gdb/TUI.html

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

### 4.1 程序运行参数

- `set args` 可指定运行时参数。（如：`set args 10 20 30 40 50` ） 
- `show args` 命令可以查看设置好的运行参数。 
- `run` 启动程序，可以简写为r。不指定运行参数 r。指定运行参数`r 10 20 30 40 50`

### 4.2 工作目录

- `cd` 相当于shell的cd命令。 
- `pwd` 显示当前的所在目录。

### 4.3 断点

#### 4.3.1 设置断点

- `break` 设置断点，可以简写为b
- `b 10` 设置断点，在源程序第10行
- `b func` 设置断点，在func函数入口处
- `break filename:linenum` 在源文件filename的linenum行处停住
- `break filename:function` 在源文件filename的function函数的入口处停住
- `break class::function`/`break class::function(type,type)` 在类class的function函数的入口处停住
- `break namespace::class::function` 在名称空间为namespace的类class的function函数的入口处停住
- `info b` 查询所有断点

#### 4.3.2 维护停止点

- `clear` 清除所有的已定义的停止点
- `clear function` 清除所有设置在函数上的停止点
- `clear linenum` 清除所有设置在指定行上的停止点
- `clear filename:linenum` 清除所有设置在指定文件：指定行上的停止点
- `delete [breakpoints] [range...]` 删除指定的断点，breakpoints为断点号。如果不指定断点号，则表示删除所有的断点。range表示断点号的范围（如：3-7）。其简写命令为d。
- `disable [breakpoints] [range...]` disable所指定的停止点，breakpoints为停止点号。如果什么都不指定，表示disable所有的停止点。简写命令是dis.
- `enable [breakpoints] [range...]` enable所指定的停止点，breakpoints为停止点号。
- `enable [breakpoints] once range…` enable所指定的停止点一次，当程序停止后，该停止点马上被GDB自动disable。
- `enable [breakpoints] delete range…` enable所指定的停止点一次，当程序停止后，该停止点马上被GDB自动删除。

### 4.4 调试代码

- `run` 运行程序，可简写为r
- `next` 单步跟踪，函数调用当作一条简单语句执行，可简写为n
    - `next count` An argument *count* is a repeat count, as for `step`.
- `step` 单步跟踪，函数调进入被调用函数体内，可简写为s
    - `step count` Continue running as in `step`, but do so *count* times. If a breakpoint is reached, or a signal not related to stepping occurs before count steps, stepping stops right away.
- `finish` 退出函数
- `continue` 继续运行程序，可简写为c
- `stepi`/`si`, `nexti`/`ni` 单步跟踪一条机器指令,一条程序代码有可能由数条机器指令完成，stepi和nexti可以单步执行机器指令。
- `info program` 来查看程序的是否在运行，进程号，被暂停的原因。

### 4.5 查看运行时数据

- `print` 打印变量、字符串、表达式等的值，可简写为p。print接受一个表达式，GDB会根据当前的程序运行的数据来计算这个表达式，表达式可以是当前程序运行中的const常量、变量、函数等内容。但是GDB不能使用程序中定义的宏。
- `p count` 打印count的值
- `p cou1+cou2+cou3` 打印表达式值
- `whatis var` 显示某个变量的类型

### 4.6 程序变量

在GDB中，你可以随时查看以下三种变量的值： 
- 全局变量（所有文件可见的） 
- 静态全局变量（当前文件可见的） 
- 局部变量（当前Scope可见的） 

如果你的局部变量和全局变量发生冲突（也就是重名），一般情况下是局部变量会隐藏全局变量，也就是说，如果一个全局变量和一个函数中的局部变量同名时，如果当前停止点在函数中，用print显示出的变量的值会是函数中的局部变量的值。如果此时你想查看全局变量的值时，你可以使用“::”操作符： 

```
file::variable 
function::variable
```

可以通过这种形式指定你所想查看的变量，是哪个文件中的或是哪个函数中的。例如，查看文件f2.c中的全局变量x的值：

```
p ‘f2.c’::x
```

当然，“::”操作符会和C++中的发生冲突，GDB能自动识别“::”是否C++的操作符，所以你不必担心在调试C++程序时会出现异常。 

***数组变量*** 

有时候，你需要查看一段连续的内存空间的值。比如数组的一段，或是动态分配的数据的大小。你可以使用GDB的“@”操作符，“@”的左边是第一个内存的地址的值，“@”的右边则你你想查看内存的长度。例如，你的程序中有这样的语句：

```
int *array = (int *) malloc (len * sizeof (int));
```

于是，在GDB调试过程中，你可以以如下命令显示出这个动态数组的取值：

```
p *array@len
```

@的左边是数组的首地址的值，也就是变量array所指向的内容，右边则是数据的长度，其保存在变量len中。

### 4.7 自动显示

你可以设置一些自动显示的变量，当程序停住时，或是在你单步跟踪时，这些变量会自动显示。相关的GDB命令是display。

```
display expr
display/fmt expr
display/fmt addr
```

expr是一个表达式，fmt表示显示的格式，addr表示内存地址，当你用display设定好了一个或多个表达式后，只要你的程序被停下来，GDB会自动显示你所设置的这些表达式的值。

```
info display
```

查看display设置的自动显示的信息。

```
undisplay dnums…
delete display dnums…
```

删除自动显示，dnums意为所设置好了的自动显式的编号。如果要同时删除几个，编号可以用空格分隔，如果要删除一个范围内的编号，可以用减号表示（如：2-5）

```
disable display dnums…
enable display dnums…
```

disable和enalbe不删除自动显示的设置，而只是让其失效和恢复。

### 4.8 显示源代码

GDB 可以打印出所调试程序的源代码，当然，在程序编译时一定要加上`–g`的参数，把源程序信息编译到执行文件中。不然就看不到源程序了。当程序停下来以后， GDB会报告程序停在了那个文件的第几行上。你可以用list命令来打印程序的源代码。默认打印10行，还是来看一看查看源代码的GDB命令吧。

```
list linenum   // Print lines centered around line number linenum in the current source file.
list function  // 显示函数名为function的函数的源程序
list   // 显示当前行后面的源程序
list - // 显示当前行前面的源程序
```

一般是打印当前行的上5行和下5行，如果显示函数是是上2行下8行，默认是10行，当然，你也可以定制显示的范围，使用下面命令可以设置一次显示源程序的行数。

```
set listsize count  // 设置一次显示源代码的行数。(unless the list argument explicitly specifies some other number)
show listsize   // 查看当前listsize的设置
```

### 4.9 查看栈信息

当程序被停住了，你需要做的第一件事就是查看程序是在哪里停住的。当你的程序调用了一个函数，函数的地址，函数参数，函数内的局部变量都会被压入“栈”（Stack）中。你可以用GDB命令来查看当前的栈中的信息。下面是一些查看函数调用栈信息的GDB命令： breacktrace,简称bt 

打印当前的函数调用栈的所有信息。如：

```
(gdb) bt
#0 func (n=250) at tst.c:6
#1 0x08048524 in main (argc=1, argv=0xbffff674) at tst.c:30
#2 0x400409ed in __libc_start_main () from /lib/libc.so.6
```

从上可以看出函数的调用栈信息：__libc_start_main –> main() –> func()

```
// n是一个正整数，表示只打印栈顶上n层的栈信息
backtrace n
bt n

// -n表一个负整数，表示只打印栈底下n层的栈信息
backtrace -n
bt -n
```

## 5. GDB Text User Interface

The GDB Text User Interface (TUI) is a terminal interface which uses the curses library to show the source file, the assembly output, the program registers and GDB commands in separate text windows. The TUI mode is supported only on platforms where a suitable version of the curses library is available.

The TUI mode is enabled by default when you invoke GDB as ‘gdb -tui’. You can also switch in and out of TUI mode while GDB runs by using various TUI commands and key bindings, such as tui enable or C-x C-a.

### 5.1 TUI Overview

In TUI mode, GDB can display several text windows:

- *command*

    This window is the GDB command window with the GDB prompt and the GDB output. The GDB input is still managed using readline.

- *source*

    The source window shows the source file of the program. The current line and active breakpoints are displayed in this window.

- *assembly*

    The assembly window shows the disassembly output of the program.

- *register*

    This window shows the processor registers. Registers are highlighted when their values change.

The source and assembly windows show the current program position by highlighting the current line and marking it with a ‘>’ marker. Breakpoints are indicated with two markers. The first marker indicates the breakpoint type:

- *B* Breakpoint which was hit at least once.
- *b* Breakpoint which was never hit.

The second marker indicates whether the breakpoint is enabled or not:

- *+* Breakpoint is enabled.
- *-* Breakpoint is disabled.

The source, assembly and register windows are updated when the current thread changes, when the frame changes, or when the program counter changes.

These windows are not all visible at the same time. The command window is always visible. The others can be arranged in several layouts:

- source only,
- assembly only,
- source and assembly,
- source and registers, or
- assembly and registers.

### 5.2 TUI Key Bindings

`Ctrl + x + a` Enter or leave the TUI mode

### 5.3 TUI-specific Commands

- `layout name` Changes which TUI windows are displayed. In each layout the command window is always displayed, the name parameter controls which additional windows are displayed, and can be any of the following:
    - `next`  Display the next layout.
    - `prev`  Display the previous layout.
    - `src`   Display the source and command windows.
    - `asm`   Display the assembly and command windows.
    - `split` Display the source, assembly, and command windows.
    - `regs`  When in src layout display the register, source, and command windows. When in asm or split layout display the register, assembler, and command windows.

- `focus name` Changes which TUI window is currently active for scrolling. The name parameter can be any of the following:
    - `next` Make the next window active for scrolling.
    - `prev` Make the previous window active for scrolling.
    - `src`  Make the source window active for scrolling.
    - `asm`  Make the assembly window active for scrolling.
    - `regs` Make the register window active for scrolling.
    - `cmd`  Make the command window active for scrolling.

- `refresh` Refresh the screen. This is similar to typing C-L.

- `update` Update the source window and the current execution point.
