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

### ‘FILE’ {aka ‘struct _IO_FILE’} has no member named ‘__pad’; did you mean ‘__pad5’?

Apply following patch in `studio/buf.c`

```
diff --git a/stdio/buf.c b/stdio/buf.c
index 9555e3d..a9c74d9 100644
--- a/stdio/buf.c
+++ b/stdio/buf.c
@@ -86,29 +86,31 @@ buffer_size(FILE *fp)

 #elif defined(_IONBF)

+/*
 #ifdef _LP64
 #define _flag __pad[4]
 #define _ptr __pad[1]
 #define _base __pad[2]
 #endif
+*/

 int
 is_unbuffered(FILE *fp)
 {
-       return(fp->_flag & _IONBF);
+       return(fp->_flags & _IONBF);
 }

 int
 is_linebuffered(FILE *fp)
 {
-       return(fp->_flag & _IOLBF);
+       return(fp->_flags & _IOLBF);
 }

 int
 buffer_size(FILE *fp)
 {
 #ifdef _LP64
-       return(fp->_base - fp->_ptr);
+       return(fp->_IO_buf_end - fp->_IO_buf_base);
 #else
        return(BUFSIZ); /* just a guess */
 #endif
```

### timeout.c:119:5: warning: ‘condition’ may be used uninitialized in this function

Apply following patch for `threadctl/timeout.c`

```
diff --git a/printer/print.h b/printer/print.h
index bb6a3d8..9f4a9b6 100644
--- a/printer/print.h
+++ b/printer/print.h
@@ -23,7 +23,7 @@
 #define LPNAME                 "lp"
 #endif

-#define FILENMSZ        64
+#define FILENMSZ        512
 #define FILEPERM        (S_IRUSR|S_IWUSR)

 #define USERNM_MAX      64
diff --git a/threadctl/timeout.c b/threadctl/timeout.c
index e3b0465..6608bb9 100644
--- a/threadctl/timeout.c
+++ b/threadctl/timeout.c
@@ -97,7 +97,7 @@ retry(void *arg)
 int
 main(void)
 {
-       int                             err, condition, arg;
+       int                             err, condition=0, arg=0;
        struct timespec when;

        if ((err = pthread_mutexattr_init(&attr)) != 0)
```

## Compiling and running own programs

The uncompressed file contains directories with the names of the chapters having all the programs of that capture and two others named `include` and `lib`.

The `include` directory contains the header file `apue.h`. The `lib` directory has the source code of the implementations defined in header file.

Lets assume the uncompressed file is located at: `BASE/`, In my case it is `/home/manoj/apue.3e`

Once you uncompress the source code, go in the directory and run make:

$ cd BASE
$ make

`make` will compile all the programs in all the chapters. Also, it will create the library that will contain the implementations of the functions in apue.h.

To compile an example program that you write from the book, run this GCC command (assuming your program's name is myls.c which is the first in the book):

```
gcc -o test test.c -I BASE/include/ -L BASE/lib/ -lapue
```

-I tells gcc which directory to look for the include file. -L tells it the location of the library directory, and -lapue, tells the name of the library file to look for in that directory. Such that -LXXX means to look for a file in the library directory with the name: libXXX.a or libXXX.so.

In my case, created a directory myprograms and created ls1.c in it. To compile and run

```
ubuntu:~/apue.3e$ gcc -o myls myprograms/ls1.c -I include/ -L lib/ -lapue
ubuntu:~/apue.3e$ ./myls myprograms
ls1.c
.
..
```
