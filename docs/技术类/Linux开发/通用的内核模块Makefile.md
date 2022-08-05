---
title: 通用内核模块Makefile
tags: [驱动]
---

# 通用内核模块 Makefile

在 Linux 驱动开发过程中，经常会需要写一些内核模块。今天整理了一下我常用的 Makefile 框架，相对比较简单，稍微改改就可以使用。

**ARM 交叉编译版本：**

```bash
# 指定模块名
MODULE_NAME := hello_module
 
# 指定源码文件（*.c）-- 单个源文件的形式
$(MODULE_NAME)-objs := hello.o
 
# 指定源码文件（*.c）-- 多个源文件的形式
# $(MODULE_NAME)-objs := hello1.o hello2.o hello3.o
 
# 指定模块编译
obj-m := $(MODULE_NAME).o
 
# 指定内核源码目录，需根据实际修改
KERNELDIR ?= /home/linux/Desktop/mp157/kernel/
 
# 执行 make all、make 时，实际都是执行 make modules
all default: modules
 
# 执行 make install 时，实际是执行 make modules_install
install: modules_install
 
# 实际干活的指令（ARM交叉编译版本）
modules modules_install help clean:
	$(MAKE) ARCH=arm CROSS_COMPILE=arm-none-linux-gnueabihf- -C $(KERNELDIR) M=$(shell pwd) $@
```

**X86 版本：**

```bash
# 指定模块名
MODULE_NAME := hello_module
 
# 指定源码文件（*.c）-- 单个源文件的形式
$(MODULE_NAME)-objs := hello.o
 
# 指定源码文件（*.c）-- 多个源文件的形式
# $(MODULE_NAME)-objs := hello1.o hello2.o hello3.o
 
# 指定模块编译
obj-m := $(MODULE_NAME).o
 
# 指定内核源码目录，需根据实际修改
KERNELDIR ?= /lib/modules/$(shell uname -r)/build
 
# 执行 make all、make 时，实际都是执行 make modules
all default: modules
 
# 执行 make install 时，实际是执行 make modules_install
install: modules_install
 
# 实际干活的指令（X86版本）
modules modules_install help clean:
	$(MAKE) -C $(KERNELDIR) M=$(shell pwd) $@
```

这个 Makefile 模板支持如下指令：

- make
- make all
- make modules
- make install
- make modules_install
- make modules_install INSTALL_MOD_PATH=xxxx
- make help
- make clean



**已知问题：**

模块名和源码名不能相同。例如，源码名为 hello.c，那么 MODULE_NAME 就不能设定为 hello，否则会报错，错误提示如下：

```bash
linux@ubuntu:~/Desktop/modules/hello_x86$ make
make -C /lib/modules/5.4.0-92-generic/build M=/home/linux/Desktop/modules/hello_x86 modules
make[1]: Entering directory '/usr/src/linux-headers-5.4.0-92-generic'
make[2]: Circular /home/linux/Desktop/modules/hello_x86/hello.o <- /home/linux/Desktop/modules/hello_x86/hello.o dependency dropped.
  LD [M]  /home/linux/Desktop/modules/hello_x86/hello.o
ld: no input files
scripts/Makefile.build:443: recipe for target '/home/linux/Desktop/modules/hello_x86/hello.o' failed
make[2]: *** [/home/linux/Desktop/modules/hello_x86/hello.o] Error 1
Makefile:1762: recipe for target '/home/linux/Desktop/modules/hello_x86' failed
make[1]: *** [/home/linux/Desktop/modules/hello_x86] Error 2
make[1]: Leaving directory '/usr/src/linux-headers-5.4.0-92-generic'
Makefile:24: recipe for target 'modules' failed
make: *** [modules] Error 2
```

