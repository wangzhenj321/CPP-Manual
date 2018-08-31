## Uninstall boost

***References:*** https://askubuntu.com/questions/397939/how-to-uninstall-boost-1-49-and-install-boost-1-54-in-ubuntu

You need to uninstall the `libboost1.49-dev` package... actually, it should be removed with a single `sudo apt-get autoremove`. If you are not sure do this:

```
dpkg -S /usr/include/boost/version.hpp
```

This will return the package that has this file, then you can:

```
sudo apt-get autoremove package
```

## Install newer version of boost

***References:*** https://stackoverflow.com/questions/12578499/how-to-install-boost-on-ubuntu

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
