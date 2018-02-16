# Hints for Ettercap on MacOS
## Introduction

Due to the BSD origins of Mac OS X, most Linux based programs and libraries can be ported to Mac OS X.
Basically the challenge is the distribution of pre-compiled software packages to ease the pain of manually resolving dependencies and manual compilation of the same.
I recommend using Homebrew for doing this job.

## Installing Homebrew

Homebrew can be easily installed by running the installation command on the official Homebrew [[website|https://brew.sh]].


## Installing pre-compiled ettercap package

To just install ettercap just type:

    brew install ettercap

Ettercap is provided w/o GTK support and w/o any debug symbols.

## Building ettercap from GitHub source

To be able to customize ettercap you need to fetch the sources from GitHub and compile on Mac OS X. Typical use cases are to enable the GTK interface, test the latest code base or reproduce issues in debug mode.
Building ettercap from source requires some dependencies to be met before being able to compile.

The following example shows how to compile ettercap in Debug mode with IPv6 support enabled.

    brew update
    brew install check curl cmake ghostscript gtk+ gtk+3 libidn libnet luajit rtmpdump geoip
    git clone https://github.com/Ettercap/ettercap
    cd ettercap
    mkdir build
    cd build
    cmake -DCMAKE_BUILD_TYPE=Debug -ENABLE_IPV6=On -DOPENSSL_ROOT_DIR=`brew --prefix openssl` ..
    make
    sudo make install
 
The definition of OpenSSL root directory is necessary since Homebrew doesn't install OpenSSL at a expected location for CMake.

The whole procedure has been demonstrated in this [video](https://www.youtube.com/watch?v=f2gBA0mD42w) on YouTube.