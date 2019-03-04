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



## References

1. [How to build Boost using Visual Studio](https://codeyarns.com/2014/06/06/how-to-build-boost-using-visual-studio/)

2. [Prepare to Use a Boost Library Binary](https://www.boost.org/doc/libs/1_55_0/more/getting_started/windows.html#prepare-to-use-a-boost-library-binary)

3. [BUILDING BOOST 1.64, 1.65, 1.66 WITH VISUAL STUDIO 2017](https://studiofreya.com/2017/04/23/building-boost-1-64-with-visual-studio-2017/)

4. [如何在Visual Studio中使用Boost C++ Library](http://programjustforfun.blogspot.com/2016/10/visual-studioboost-c-library.html)
