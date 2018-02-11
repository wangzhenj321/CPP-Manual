## Question

The stdafx.h is automatically generated when I start a project in VS2010. I need to make cross-platform C++ library, so I don't/can't use this header file.

What is this for? Is it OK that I just remove this header file?

## Answer

All C++ compilers have one serious performance problem to deal with. Compiling C++ code is a long, slow process.

Compiling headers included on top of C++ files is a very long, slow process. Compiling the huge header structures that form part of Windows API and other large API libraries is a very, very long, slow process. To have to do it over, and over, and over for every single Cpp source file is a death knell.

This is not unique to Windows but an old problem faced by all compilers that have to compile against a large API like Windows.

The Microsoft compiler can ameliorate this problem with a simple trick called precompiled headers. The trick is pretty slick: although every CPP file can potentially and legally give a sligthly different meaning to the chain of header files included on top of each Cpp file (by things like having different macros #define'd in advance of the includes, or by including the headers in different order), that is most often not the case. Most of the time, we have dozens or hundreds of included files, but they all are intended to have the same meaning for all the Cpp files being compiled in your application.

The compiler can make huge time savings if it doesn't have to start to compile every Cpp file plus its dozens of includes literally from scratch every time.

The trick consists of designating a special header file as the starting point of all compilation chains, the so called 'precompiled header' file, which is commonly a file named stdafx.h simply for historical reasons.

Simply list all your big huge headers for your APIs in your stdafx.h file, in the appropriate order, and then start each of your CPP files at the very top with an `#include "stdafx.h"`, before any meaningful content (just about the only thing allowed before is comments).

Under those conditions, instead of starting from scratch, the compiler starts compiling from the already saved results of compiling everything in `stdafx.h`.

I don't believe that this trick is unique to Microsoft compilers, nor do I think it was an original development.

For Microsoft compilers, the setting that controls the use of precompiled headers is controlled by a command line argument to the compiler: `/Yu "stdafx.h"`. As you can imagine, the use of the stdafx.h file name is simply a convention; you can change the name if you so wish.

In Visual Studio 2010, this setting is controlled from the GUI via Right-clicking on a CPP Project, selecting 'Properties' and navigating to "Configuration Properties\C/C++\Precompiled Headers". For other versions of Visual Studio, the location in the GUI will be different.

Note that if you disable precompiled headers (or run your project through a tool that doesn't support them), it doesn't make your program illegal; it simply means that your tool will compile everything from scratch every time.

If you are creating a library with no Windows dependencies, you can easily comment out or remove #includes from the stdafx.h file. There is no need to remove the file per se, but clearly you may do so as well, by disabling the precompile header setting above.

## References

1. [What's the use for “stdafx.h” in Visual Studio?](https://stackoverflow.com/questions/4726155/whats-the-use-for-stdafx-h-in-visual-studio)

2. [stdafx.h和targetver.h的作用](http://blog.csdn.net/google0802/article/details/9034085)