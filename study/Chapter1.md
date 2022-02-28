# Exercises

**1.1** Verify on your system that the directories dot and dot-dot are not the same, except in the root directory.

**Answer:**
We can easily verify it by printing value of `inode` as follows:


For root directory,  `/`, `/.`, and `/..` all have the same inode:

```
ubuntu:~$ ls -ldi / /. /..
2 drwxr-xr-x 20 root root 4096 Jun 27  2021 /
2 drwxr-xr-x 20 root root 4096 Jun 27  2021 /.
2 drwxr-xr-x 20 root root 4096 Jun 27  2021 /..

```

For other directories, `..` refers to the **parent** directory
```
ubuntu:~$ ls -ldi /usr /usr/. /usr/..
9437185 drwxr-xr-x 14 root root 4096 Feb 10  2021 /usr
9437185 drwxr-xr-x 14 root root 4096 Feb 10  2021 /usr/.
      2 drwxr-xr-x 20 root root 4096 Jun 27  2021 /usr/..
```

**1.2** In the output from the Program 1.4, what happened to the processes with process IDs 852 and 853?

**Answer:**

Between two execution of program, there will be some other process (or threads) that are started either by another use, system services or may be cron job. If you run the program quick enough, you will see consecutive process IDs as follows:

```
$ ./hello;./hello
hello world from process ID 50224
hello world from process ID 50225
```

**1.3** In Section 1.7 the argument to `perror` is defined with the ISO C attribute `const`, whereas the integer argument to `strerror` isn’t defined with this attribute. Why?

**Answer:**

The `perror` function takes a pointer to the first character in a string of characters. This is passed by reference and hence the called function can modify the content of the string. The `const` enables the compiler to generate a compile-time error if the function did try to modify the value pointed to by the given pointer.

The `strerror` function takes an int, which is passed by value so the function receives a copy of the value, which can not modify the original value.

**1.4** In the error handling function `err_sys` in Appendix B, why is the value of `errno` saved when the function is called?

**Answer:**

The `perror` function procudes an error message on the standard error (based on the current value of errno) and hence the value need to be saved before calling the function.


**1.5** If the calendar time is stored as a signed 32-bit integer, in which year will it overflow?

**Answer:**

A signed 32-bit integer can store values [-2147483648, 2147483647]. As we are using signed integer, assuming only +ve values to be used. Overflow will happen after *2147483647 ticks* or *248.55 days*.

Time starts at Epoch [Januay 1, 1970 at 00:00:00 UTC]; that will be time t=0. The clock advances 1 tick/second

```
    2147483647 ticks         * (1 second  / 1 tick)
=   2147483647 seconds       * (1 minutes / 60 seconds)
=     35791394.1166 minutes  * (1 hour    / 60 minutes)
=       596523.235 hours     * (1 day     / 24 hours)
=        24855.135 days      * (1 year    / 365.25 days)
=           68 years
```

**1.6** If the process time is stored as a signed 32-bit integer, and if the system counts 100 ticks per second, after how many days will the value overflow?

**Answer:**

A signed 32-bit integer can store values [-2147483648, 2147483647]. As we are using signed integer, assuming only +ve values to be used. Overflow will happen after *2147483647 ticks* or *248.55 days*.

```
    2147483647 ticks       * (1 second  / 100 ticks)
=   21474836.47 seconds    * (1 minutes / 60 seconds)
=     357913.94 minutes    * (1 hour    / 60 minutes)
=       5965.23 hours      * (1 day     / 24 hours)
=        248.55 days
```
