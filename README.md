# vsd-hdp

# Day 0 

## Tool 1: Yosys
**Objectives**
    - Install Ubuntu 20.04 and install yosys, iverilog and gtkwave.
    - Check and verify yosys is succesfully installed.

**Installation**

*Steps followed to install Yosys*


    $ git clone https://github.com/YosysHQ/yosys.git
    $ cd yosys
    $ sudo apt install make (If make is not installed please install it) 
    $ sudo apt-get install build-essential clang bison flex \
    libreadline-dev gawk tcl-dev libffi-dev git \
    graphviz xdot pkg-config python3 libboost-system-dev \
    libboost-python-dev libboost-filesystem-dev zlib1g-dev
    $ make config-gcc
    $ make 
    $ sudo make install


Progrees Image of Yosys:

![yosys](https://github.com/Sourabhk20/vsd-hdp/assets/148907305/8fbce3e6-98d4-4d10-a633-9fb0bc75f395)







