---
title: 编译自己的Ubuntu内核
tags: [系统]
---

# 编译自己的Ubuntu内核


## 1. 环境构建

修改`/etc/apt/sources.list`文件，将`deb-src`行的注释符号去掉。应该是

```bash
sudo apt update
```

```bash
sudo apt-get build-dep linux linux-image-$(uname -r)

# 如果安装失败，可以在系统software & updates配置项下更改软件源
```

```bash
sudo apt-get install libncurses-dev gawk flex bison openssl libssl-dev dkms libelf-dev libudev-dev libpci-dev libiberty-dev autoconf zstd git libcap-dev
```



## 2. 获取源码&编译&安装



### 2.1 git（实践成功）

```bash
git://git.launchpad.net/~ubuntu-kernel/ubuntu/+source/<source package>/+git/<series>
```

以Ubuntu 20.04为例：

```bash
git clone git://git.launchpad.net/~ubuntu-kernel/ubuntu/+source/linux/+git/focal
```

```bash
# 切换到源码目录
cd focal
```

```bash
# 查看代码tag
git tag
```

```bash
# 获取指定tag
git checkout -b linux58 Ubuntu-hwe-5.15-5.15.0-43.46_20.04.1
```

```bash
chmod a+x debian/rules
chmod a+x debian/scripts/*
chmod a+x debian/scripts/misc/*
```

```bash
# 清除环境
fakeroot debian/rules clean
```

```bash
# 编译内核
fakeroot debian/rules binary-headers binary-generic
```

编译成功后，会在上层目录生成多个linux\*5.15\*.deb文件。使用如下命令安装：

```bash
# 安装内核
cd ..
sudo dpkg -i linux*5.15*.deb
```

> 注意：如果当前运行的就是5.15版本内核，那么在安装相同内核的时候会报错显示`conflict`。

```bash
# 更新grub文件
sudo update-grub
```

```bash
# 重启系统，在启动阶段选择新安装的内核
sudo reboot
```



### 2.2 apt-get方法

```bash
apt-get source linux-image-unsigned-$(uname -r)
```

- Modifying the configuration

  This step can be skipped if no configuration changes are wanted. The build process will use a configuration that is put together from various sub-config files. The simplest way to modify anything here is to run:

  ```bash
  chmod a+x debian/rules
  chmod a+x debian/scripts/*
  chmod a+x debian/scripts/misc/*
  LANG=C fakeroot debian/rules clean
  LANG=C fakeroot debian/rules editconfigs # you need to go through each (Y, Exit, Y, Exit..) or get a complaint about config later
  ```

  This takes the current configuration for each architecture/flavour supported and calls menuconfig to edit its config file. The chmod is needed because the way the source package is created, it loses the executable bits on the scripts.

  In order to make your kernel "newer" than the stock Ubuntu kernel from which you are based you should add a local version modifier. Add something like "+test1" to the end of the first version number in the `debian.master/changelog` file, before building. This will help identify your kernel when running as it also appears in `uname -a`. Note that when a new Ubuntu kernel is released that will be newer than your kernel (which needs regenerating), so care is needed when upgrading. NOTE: do not attempt to use CONFIG_LOCALVERSION as this _will_ break the build.

- Building the kernel

  Building the kernel is quite easy. Change your working directory to the root of the kernel source tree and then type the following commands:

  ```bash
  LANG=C fakeroot debian/rules clean
  # quicker build:
  LANG=C fakeroot debian/rules binary-headers binary-generic binary-perarch
  # if you need linux-tools or lowlatency kernel, run instead:
  LANG=C fakeroot debian/rules binary
  ```

  If the build is successful, a set of three .deb binary package files will be produced in the directory above the build root directory. For example after building a kernel with version "4.8.0-17.19" on an amd64 system, these three (or four) .deb packages would be produced:

  ```bash
  cd ..
  ls *.deb
      linux-headers-4.8.0-17_4.8.0-17.19_all.deb
      linux-headers-4.8.0-17-generic_4.8.0-17.19_amd64.deb
      linux-image-4.8.0-17-generic_4.8.0-17.19_amd64.deb
  ```

  on later releases you will also find a linux-extra- package which you should also install if present.

- Testing the new kernel

  Install the three-package set (on your build system, or on a different target system) with dpkg -i and then reboot:

  ```bash
  sudo dpkg -i linux*4.8.0-17.19*.deb
  sudo update-grub
  sudo reboot
  ```



## 3. 踩坑记录

- 未安装zstd

  Ubuntu官方给的必装软件中，为包含`zstd`，导致编译出错。

- 编译log太多，导致错误提示被冲掉

  编译过程中，当出现某个编译错误后，编译并不会立即停止。这就导致错误日志会被后续的信息冲掉。

  解决方案：

  - 将终端的scroll值改的比较大，例如50000。
  - 搜一搜编译日志是否有保存为文件。
  - 编译时直接通过管道符`>`把编译日志输出到文件当中，编译结束后查询。



## 4. 参考资料

[Kernel/BuildYourOwnKernel - Ubuntu Wiki](https://wiki.ubuntu.com/Kernel/BuildYourOwnKernel)

[Kernel/SourceCode - Ubuntu Wiki](https://wiki.ubuntu.com/Kernel/SourceCode)

[Recompile 20.04 HWE kernel? - Ask Ubuntu](https://askubuntu.com/questions/1311636/recompile-20-04-hwe-kernel)

[how-to-compile-and-install-kernel-on-ubuntu](https://itsubuntu.com/how-to-compile-and-install-kernel-on-ubuntu/)

[HOWTO: Compile Linux kernel (on Ubuntu, applies to any distro) - DEV Community](https://dev.to/wxyz/howto-compile-linux-kernel-on-ubuntu-applies-to-any-distro-44k7)

[Sam隨手筆記: How to compile Kernel Ubuntu 20.04 (sam0537.blogspot.com)](https://sam0537.blogspot.com/2020/07/copy-from-httpslinuxguides.html)

https://discourse.ubuntu.com/t/how-to-compile-kernel-in-ubuntu-20-04/20268/9

[Quickly building a custom Linux (Ubuntu) kernel, with modified configuration (kernel timer frequency) – Saverio Miroddi – 64K RAM SYSTEM  38911 BASIC BYTES FREE](https://saveriomiroddi.github.io/Quickly-building-a-custom-linux-ubuntu-kernel-with-modified-configuration-kernel-timer-frequency/)

[Compile and install kernel ubuntu (linuxhint.com)](https://linuxhint.com/compile-and-install-kernel-ubuntu/)

[How to Build and Install a Custom Kernel on Ubuntu - Make Tech Easier](https://www.maketecheasier.com/build-custom-kernel-ubuntu/)

[Rebuild the official Ubuntu kernel – Ubuntu 16.04 LTS | Any IT here? Help Me! (ahelpme.com)](https://ahelpme.com/linux/ubuntu/rebuild-the-official-ubuntu-kernel-ubuntu-16-04-lts/)

[Kernel/SourceCode - Ubuntu Wiki](https://wiki.ubuntu.com/Kernel/SourceCode)