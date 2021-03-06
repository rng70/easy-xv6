# xv6-rng70

---

This is modified version of official xv6-public version. This version of xv6 was modified for 

*   easy functionality
    *   [adding system calls easily](#modularize-syscalls)
*   [bug fixes](#bug-fix)
    *   make qemu "error: writing 1 byte into a region of size 0β
    *   qemu hangs on "Booting from hard disk..."

# Installation 

---

This version is slightly modified and tested on

*   OS: Garuda Linux
*   Kernel: 5.15.12-zen1-1-zen
*   Shell: fish-3.3.1
*   gcc/g++: 11.1.0

*   ***Installation procedure***

    *   install qemu through pacman

        *   `sudo pacman -S qemu`

    *   clone this repository 

        *   `git clone https://github.com/rng70/xv6-public.git`

    *   go to the cloned repo and build and run

        *   cd xv6-public && make qemu

            **in arch linux `make qemu` work exactly `make qemu-nox` where xv6 run at the current terminal instead of new terminal (at least for fish shell). if itβs not then run `make qemu-nox` to run xv6 at the same terminal**

After successful build and run the terminal will look like this if `qemu-nox` works

![image](./images/qemu-nox.png)

otherwise a new qemu terminal will be opened

![images](./images/qemu.png)



# Bug-Fix

1.   ***Case 1: make qemu "error: writing 1 byte into a region of size 0"***

β	two lines added before and after **line 1461: *lastaddr = 99; in usertests.c**

```c
#pragma GCC diagnostic ignored "-Wstringop-overflow"
*lastaddr = 99;
#pragma GCC diagnostic pop
```



2.   ***Case 2: qemu hangs on "Booting from hard disk..."***

β	this problem occurs specially on arch linux though it is quite common for other distros also. See [#155](https://github.com/mit-pdos/xv6-public/pull/155). to solve this problem Makefile was slightly modified.

# Explore xv6 

---

##### Modularize SysCalls

system calls was modularized from here [#162](https://github.com/mit-pdos/xv6-public/pull/162)

adding a system call is not easy as it is needed to be added to 5 different files

*   in **Makefile** _(two places)_
*   in **syscall.h**
*   in **syscall.c** _(two places)_
*   in **usys.S**
*   in **user.h**

in this version of xv6 adding system call is little bit easy. just need to add two line of code in `syscall.h` and `systemcallnames.h`



##### Modify userlevel indicator

Lets say we want to replace the `$` sign with username of the system which can be achieved by editing  `sh.c` file. 

To achieve required result change source code inside `getcmd` method.

first of all, add these two line of code at the beginning of `sh.c`

```c
#define ANSI_COLOR_RESET  "\x1b[0m"
#define ANSI_COLOR_RED  "\x1b[31m"
```

ANSI_COLOR_CODE list:

```c
#define ANSI_COLOR_BLACK   "\x1b[30m"
#define ANSI_COLOR_RED     "\x1b[31m"
#define ANSI_COLOR_GREEN   "\x1b[32m"
#define ANSI_COLOR_YELLOW  "\x1b[33m"
#define ANSI_COLOR_BLUE    "\x1b[34m"
#define ANSI_COLOR_MAGENTA "\x1b[35m"
#define ANSI_COLOR_CYAN    "\x1b[36m"
#define ANSI_COLOR_WHITE   "\x1b[37m"
#define ANSI_COLOR_DEFAULT "\x1b[39m"
#define ANSI_COLOR_RESET   "\x1b[0m"
```

now editing `getcmd` method in `sh.c` like the following

```c
printf(2, ANSI_COLOR_RED "rng70@al\nΞ» " ANSI_COLOR_RESET);
```

now open terminal and run `make qemu` and see the userlevel indicator changed

![image](./images/userlevel.png)



## official statement [here](./README)
