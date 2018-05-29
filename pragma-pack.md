## Part 1: [Visual C++ pack](https://msdn.microsoft.com/en-us/library/2e70t5y1.aspx?f=255&MSPPError=-2147217396)

```c++
#pragma pack( [ show ] | [ push | pop ] [, identifier ] , n  )
```

***References:*** [#pragma pack 指令详解](https://www.jianshu.com/p/90a6eef329ec)

`#pragma pack`的主要作用就是改变编译器的内存对齐方式，这个指令在网络报文的处理中有着重要的作用，`#pragma pack(n)`是他最基本的用法，其作用是改变编译器的对齐方式，不使用这条指令的情况下，编译器默认采取`#pragma pack(8)`也就是8字节的默认对齐方式，n值可以取（1， 2， 4， 8， 16） 中任意一值。

1. `#pragma pack(show)`显示当前内存对齐的字节数。

2. 单纯使用`#pragma pack(push)`会将当前的对齐字节数压入栈顶，并设置这个值为新的对齐字节数，就是说不会改变这个值。而使用`#pragma pack(push, n)`会将当前的对齐字节数压入栈顶，并设置n为新的对齐字节数。再就是这个`#pragma pack(push, identifier [, n])`会在上面的操作基础上为这个对齐字节数附上一个标识符，这里注意这个标识符只能以（$、_、字母）开始，标识符中可以有（$、_、字母、数字），并且标识符不能是关键字(push， pop可以作为标识符)。

3. 单纯使用`#pragma pack(pop)`会弹出栈顶对齐字节数，并设置其为新的内存对齐字节数。使用`#pragma pack(pop, n)`情况就不同了，他会弹出栈顶并直接丢弃，设置n为其新的内存对齐字节数。`#pragma pack(pop, identifier [, n])`较为复杂，编译器执行这条执行时会从栈顶向下顺序查找匹配的identifier，找到identifier相同的这个数之后将从栈顶到identifier，包括找到identifier全部pop弹出，若没有找到则不进行任何操作。

## Part 2: [GCC pack](https://gcc.gnu.org/onlinedocs/gcc-4.4.4/gcc/Structure_002dPacking-Pragmas.html)

For compatibility with Microsoft Windows compilers, GCC supports a set of `#pragma` directives which change the maximum alignment of members of structures (other than zero-width bitfields), unions, and classes subsequently defined. The n value below always is required to be a small power of two and specifies the new alignment in bytes.

1. `#pragma pack(n)` simply sets the new alignment.

2. `#pragma pack()` sets the alignment to the one that was in effect when compilation started (see also command line option `-fpack-struct[=<n>]`).

3. `#pragma pack(push[,n])` pushes the current alignment setting on an internal stack and then optionally sets the new alignment.

4. `#pragma pack(pop)` restores the alignment setting to the one saved at the top of the internal stack (and removes that stack entry). Note that `#pragma pack([n])` does not influence this internal stack; thus it is possible to have `#pragma pack(push)` followed by multiple `#pragma pack(n)` instances and finalized by a single `#pragma pack(pop)`.
