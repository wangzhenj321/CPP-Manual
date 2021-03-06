**Table of Contents**

[**For Ubuntu 16.04**](#for-ubuntu-1604)

- [Uninstall boost](#uninstall-boost)

- [Install newer version of boost](#install-newer-version-of-boost)

- [References](#references)

[**For Windows 10**](#for-windows-10)

- [Prerequisities](#prerequisities)

    - [Version list of Microsoft Visual C++](#version-list-of-microsoft-visual-c)
    
        - [Unknown compiler version while compiling Boost with MSVC](#unknown-compiler-version-while-compiling-boost-with-msvc)
    
    - [Developer Command Prompt for Visual Studio](#developer-command-prompt-for-visual-studio)
    
    - [Header Only VS Build](#header-only-vs-build)
    
    - [Library Naming](#library-naming)

- [Basic build steps](#basic-build-steps)

- [References](#references-1)

# For Ubuntu 16.04

## Uninstall boost

You need to uninstall the `libboost1.49-dev` package... actually, it should be removed with a single `sudo apt-get autoremove`. If you are not sure do this:

```
dpkg -S /usr/include/boost/version.hpp
```

This will return the package that has this file, then you can:

```
sudo apt-get autoremove package
```

## Install newer version of boost

> ***Remarks*** The installation process will take a long time.

Get the version of Boost that you require. This is for 1.55 but feel free to change or manually download yourself:

```
wget -O boost_1_55_0.tar.gz https://sourceforge.net/projects/boost/files/boost/1.55.0/boost_1_55_0.tar.gz/download
tar xzvf boost_1_55_0.tar.gz
cd boost_1_55_0/
```

Get the required libraries, main ones are `icu` for `boost::regex` support:

```
sudo apt-get update
sudo apt-get install build-essential g++ python-dev autotools-dev libicu-dev build-essential libbz2-dev libboost-all-dev
```

Boost's bootstrap setup:

```
./bootstrap.sh --prefix=/usr/
```

Then build it with:

```
./b2
```

and eventually install it:

```
sudo ./b2 install 
```

## References

1. [How to uninstall BOOST 1.49 and install BOOST 1.54 in ubuntu](https://askubuntu.com/questions/397939/how-to-uninstall-boost-1-49-and-install-boost-1-54-in-ubuntu)

2. [How to install Boost on Ubuntu](https://stackoverflow.com/questions/12578499/how-to-install-boost-on-ubuntu)

# For Windows 10

If your code compiled with Visual Studio needs Boost, you can get [prebuilt Boost libraries](http://boost.teeks99.com/). However, if your code needs to link with Boost library files, then **the Visual Studio version used for your code and used to build Boost has to match**. If you cannot find a prebuilt Boost library matching your Visual Studio then you might have to build Boost by yourself. I had this problem since my code was using C++11 features that required Visual Studio 2015, but the prebuilt Boost libraries were only available for Visual Studio 2012.

## Prerequisities

### Version list of Microsoft Visual C++

Refer to [Microsoft Visual C++](https://en.wikipedia.org/wiki/Microsoft_Visual_C%2B%2B).

```
MSC    1.0   _MSC_VER == 100
MSC    2.0   _MSC_VER == 200
MSC    3.0   _MSC_VER == 300
MSC    4.0   _MSC_VER == 400
MSC    5.0   _MSC_VER == 500
MSC    6.0   _MSC_VER == 600
MSC    7.0   _MSC_VER == 700
MSVC++ 1.0   _MSC_VER == 800
MSVC++ 2.0   _MSC_VER == 900
MSVC++ 4.0   _MSC_VER == 1000 (Developer Studio 4.0)
MSVC++ 4.2   _MSC_VER == 1020 (Developer Studio 4.2)
MSVC++ 5.0   _MSC_VER == 1100 (Visual Studio 97 version 5.0)
MSVC++ 6.0   _MSC_VER == 1200 (Visual Studio 6.0 version 6.0)
MSVC++ 7.0   _MSC_VER == 1300 (Visual Studio .NET 2002 version 7.0)
MSVC++ 7.1   _MSC_VER == 1310 (Visual Studio .NET 2003 version 7.1)
MSVC++ 8.0   _MSC_VER == 1400 (Visual Studio 2005 version 8.0)
MSVC++ 9.0   _MSC_VER == 1500 (Visual Studio 2008 version 9.0)
MSVC++ 10.0  _MSC_VER == 1600 (Visual Studio 2010 version 10.0)
MSVC++ 11.0  _MSC_VER == 1700 (Visual Studio 2012 version 11.0)
MSVC++ 12.0  _MSC_VER == 1800 (Visual Studio 2013 version 12.0)
MSVC++ 14.0  _MSC_VER == 1900 (Visual Studio 2015 version 14.0)
MSVC++ 14.1  _MSC_VER == 1910 (Visual Studio 2017 version 15.0)
MSVC++ 14.11 _MSC_VER == 1911 (Visual Studio 2017 version 15.3)
MSVC++ 14.12 _MSC_VER == 1912 (Visual Studio 2017 version 15.5)
MSVC++ 14.13 _MSC_VER == 1913 (Visual Studio 2017 version 15.6)
MSVC++ 14.14 _MSC_VER == 1914 (Visual Studio 2017 version 15.7)
MSVC++ 14.15 _MSC_VER == 1915 (Visual Studio 2017 version 15.8)
MSVC++ 14.16 _MSC_VER == 1916 (Visual Studio 2017 version 15.9)
```

![](../img/install-boost/vs_version_info.png?raw=true)

#### Unknown compiler version while compiling Boost with MSVC

Refer to [Unknown compiler version while compiling Boost with MSVC 14.0 (VS 2015)](https://stackoverflow.com/questions/30760889/unknown-compiler-version-while-compiling-boost-with-msvc-14-0-vs-2015).

Latest (at the time of posting this answer) **Boost 1.58 does** support **MSVC 14.0 Preview** which was the latest MS compiler at the time of Boost 1.58 release. Now, the latest version of Visual Studio is **2015 RC** which isn't covered in the boost 1.58 config file.

To stop Boost 1.58 complaining about unknown compiler version edit `boost/config/compiler/visualc.hpp` and replace:

```
// last known and checked version is 19.00.22129 (VC14 Preview):
#if (_MSC_VER > 1800 && _MSC_FULL_VER > 190022310)
```

with:

```
// last known and checked version is 19.00.22816 (VC++ 2015 RC):
#if (_MSC_VER > 1800 && _MSC_FULL_VER > 190022816)
```

### Developer Command Prompt for Visual Studio

The Developer Command Prompt for Visual Studio enables you to use .NET Framework tools more easily. It is a command prompt that automatically sets specific environment variables. You may have multiple command prompts, depending on the version of Visual Studio and any additional SDKs you've installed. For example, 64-bit versions of Visual Studio provide both 32-bit and 64-bit command prompts. 

**In Windows 10**

1. In the search box on the taskbar, start typing the name of the tool, such as `dev` or `developer command prompt`. This brings up a list of installed apps that match your search pattern. If you're looking for a different command prompt, try entering a different search term such as `prompt`.

2. Choose the **Developer Command Prompt for Visual Studio** (or the command prompt you want to use).

![](../img/install-boost/developer_command_prompt_for_visual_studio.png?raw=true)

### Header Only VS Build

Refer to [如何在Visual Studio中使用Boost C++ Library](http://programjustforfun.blogspot.com/2016/10/visual-studioboost-c-library.html).

1. 不需要Build: Header Only

    大部分Lib只需要include Header就行了, Library code都寫在header. 對於不用build的Lib，只要include header就行了
    
    ![](../img/install-boost/include_boost_header_in_vs.png?raw=true)

2. 需要Build: 

    一些需要先build, 例如: 跟系統有關的
    
    ![](../img/install-boost/include_boost_lib_in_vs.png?raw=true)

### Library Naming

Refer to [Library Naming](https://www.boost.org/doc/libs/1_55_0/more/getting_started/windows.html#library-naming).

![](../img/install-boost/boost_library_naming.png?raw=true)

In order to choose the right binary for your build configuration you need to know how Boost binaries are named. Each library filename is composed of a common sequence of elements that describe how it was built. For example, `libboost_regex-vc71-mt-d-1_34.lib` can be broken down into the following elements:

- **lib**

    Prefix: except on Microsoft Windows, every Boost library name begins with this string. On Windows, only ordinary static libraries use the lib prefix; import libraries and DLLs do not.
    
- **boost_regex**

    Library name: all boost library filenames begin with boost_ (e.g. boost_filesystem = filesystem library).

- **vc71**

    Toolset tag: identifies the toolset and version used to build the binary (e.g. vc141 = msvc-14.1 = Visual Studio 2017).

- **mt**

    Threading tag: indicates that the library was built with multithreading support enabled. Libraries built without multithreading support can be identified by the absence of `-mt`.

- **d**

    ABI tag: encodes details that affect the library's interoperability with other compiled code. For each such feature, a single letter is added to the tag:
    
    | **Key** |	**Use this library when:** | **Boost.Build option** |
    |---|---|---|
    | s | linking statically to the C++ standard library and compiler runtime support libraries. | runtime-link=static |
    | g | using debug versions of the standard and runtime support libraries. | runtime-debugging=on |
    | y | using a special debug build of Python. | python-debugging=on |
    | d | building a debug version of your code. | variant=debug |
    | p | using the STLPort standard library rather than the default one supplied with your compiler. | stdlib=stlport |
    
    For example, if you build a debug version of your code for use with debug versions of the static runtime library and the STLPort standard library in “native iostreams” mode, the tag would be: `-sgdpn`. If none of the above apply, the ABI tag is ommitted.

- **1_34**

    Version tag: the full Boost release number, with periods replaced by underscores. For example, version 1.31.1 would be tagged as "-1_31_1" (e.g. 1_64 = Version 1.64).

- **.lib**

    Extension: determined according to the operating system's usual convention. On most unix-style platforms the extensions are **.a** and **.so** for static libraries (archives) and shared libraries, respectively. On Windows, **.dll** indicates a shared library and **.lib** indicates a static or import library. Where supported by toolsets on unix variants, a full version extension is added (e.g. ".so.1.34") and a symbolic link to the library file, named without the trailing version number, will also be created.

## Basic build steps

Refer to [How to build Boost using Visual Studio](https://codeyarns.com/2014/06/06/how-to-build-boost-using-visual-studio/).

To build Boost for Visual Studio, I followed these steps:

> **This compilation took about 30 minutes and after it is done, the Boost header files and built libraries are dumped in `C:\Boost`.**

1. Download the source of Boost from [here](https://www.boost.org/users/download/). Unzip it anywhere.

2. Open the **Developer Command Prompt for Visual Studio**. Change to the directory where Boost is unzipped.

3. Build the **Boost.Build tool** (`b2.exe`) using this command:

    ```
    bootstrap.bat
    ```

4. Build Boost using this compile invocation:

    ```
    b2 toolset=msvc-14.0 --build-type=complete --abbreviate-paths architecture=x86 address-model=64 install -j4
    ```
    
    **Options**
    
    - `toolset`: Use this to specify the Visual C++ compiler to use. For Visual Studio 2015, this is msvc-14.0. For other versions of Visual Studio see this post.
    
        ```
        Visual Studio 2012 -> msvc-11.0
        Visual Studio 2013 -> msvc-12.0
        Visual Studio 2015 -> msvc-14.0
        Visual Studio 2017 -> msvc-14.1
        ```
    
    - `architecture`: This is the processor architecture. Keep this x86 for both 32-bit and 64-bit builds!
    
    - `address-model`: Use this to specify whether you want 32 or 64 bit library to be built.
    
    - `-j`: Use this to specify how many cores to use for parallel compilation.
    
    In particular, to limit the amount of time spent building, you may be interested in:
    
    - reviewing the list of library names with `--show-libraries`
    
    - limiting which libraries get built with the `--with-`*library-name* or `--without-`*library-name* options
    
    - choosing a specific build variant by adding `release` or `debug` to the command line.

## References

1. [Prepare to Use a Boost Library Binary](https://www.boost.org/doc/libs/1_55_0/more/getting_started/windows.html#prepare-to-use-a-boost-library-binary)

2. [BUILDING BOOST 1.64, 1.65, 1.66 WITH VISUAL STUDIO 2017](https://studiofreya.com/2017/04/23/building-boost-1-64-with-visual-studio-2017/)
