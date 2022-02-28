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
