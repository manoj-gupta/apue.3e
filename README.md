# apue.3e

This is the repo of examples provided by Advanced Programming in the UNIX® Environment available for download at [link](http://www.apuebook.com/code3e.html).

# Steps for Compilation
Some modifications are done to compile it on Ubuntu 20.04

1. tar -zxvf src.3e.tar.gz
2. cd apue.3e
3. make

## Fixes required

### cannot find -lbsd
Install the package using 

```
sudo apt-get install libbsd-dev
```

### devrdev.c:(.text+0xc5): undefined reference to `minor’

In `devrdev.c` include `sys/sysmacros.h` headerfile

```
#include <sys/sysmacros.h>
```
