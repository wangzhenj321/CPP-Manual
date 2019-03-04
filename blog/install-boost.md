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

If your code compiled with Visual Studio needs Boost, you can get [prebuilt Boost libraries](http://boost.teeks99.com/). However, if your code needs to link with Boost library files, then the Visual Studio version used for your code and used to build Boost has to match. If you cannot find a prebuilt Boost library matching your Visual Studio then you might have to build Boost by yourself. I had this problem since my code was using C++11 features that required Visual Studio 2015, but the prebuilt Boost libraries were only available for Visual Studio 2012.

To build Boost for Visual Studio, I followed these steps:

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
    
    - `toolset`: Use this to specify the Visual C++ compiler to use. For Visual Studio 2015, this is msvc-14.0. For other versions of Visual Studio see this post.
    
    - `architecture`: This is the processor architecture. Keep this x86 for both 32-bit and 64-bit builds!
    
    - `address-model`: Use this to specify whether you want 32 or 64 bit library to be built.
    
    - `-j`: Use this to specify how many cores to use for parallel compilation.

> **This compilation took about 30 minutes and after it is done, the Boost header files and built libraries are dumped in `C:\Boost`.**

## References

1. [How to build Boost using Visual Studio](https://codeyarns.com/2014/06/06/how-to-build-boost-using-visual-studio/)

2. [Prepare to Use a Boost Library Binary](https://www.boost.org/doc/libs/1_55_0/more/getting_started/windows.html#prepare-to-use-a-boost-library-binary)

3. [BUILDING BOOST 1.64, 1.65, 1.66 WITH VISUAL STUDIO 2017](https://studiofreya.com/2017/04/23/building-boost-1-64-with-visual-studio-2017/)

4. [如何在Visual Studio中使用Boost C++ Library](http://programjustforfun.blogspot.com/2016/10/visual-studioboost-c-library.html)
