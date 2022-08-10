---
title: STM32MP1开发板使用步骤
tags: [开发板]
---



## 1. 资料下载

[STM32MP157-Mini开发板 — 正点原子资料下载中心 1.0.0 文档 (openedv.com)](http://www.openedv.com/docs/boards/arm-linux/zdyz-minimp157.html)

[公开仓库列表 - 正点原子Linux团队 (coding.net)](https://alientek-linux.coding.net/public/)



## 2. 交叉编译工具链

位于：开发板光盘 -> 5、开发工具 -> 1、交叉编译器 -> gcc-arm-9.2-2019.12-x86_64-arm-none-linux-gnueabihf.tar.xz

> 坑：在coding仓库下载的交叉编译工具链不能用，还是得用百度网盘。

- 创建路径，用于存放交叉编译工具链

    ```bash
    sudo mkdir /usr/local/arm
    ```

- 拷贝交叉编译工具链压缩包到刚创建的路径

    ```bash
    sudo cp gcc-arm-9.2-2019.12-x86_64-arm-none-linux-gnueabihf.tar.xz /usr/local/arm/ -f
    ```

- 解压

    ```bash
    cd /usr/local/arm && sudo tar -vxf gcc-arm-9.2-2019.12-x86_64-arm-none-linux-gnueabihf.tar.xz
    ```

- 通过 `~/.bashrc` 文件修改系统 PATH 变量

    ```bash
    sudo vi ~/.bashrc
    ```

    ```bash
    export PATH=$PATH:/usr/local/arm/gcc-arm-9.2-2019.12-x86_64-arm-none-linux-gnueabihf/bin
    ```

- 使用交叉编译工具链之前，安装必要的库

  ```bash
  sudo apt-get update
  ```

  ```bash
  sudo apt-get install -y lsb-core lib32stdc++6
  ```

- 验证安装结果

  ```bash
  arm-none-linux-gnueabihf-gcc -v
  ```




## 3. 安装 ST 官网软件



### 3.1 Java 环境安装

- 查看当前 ubuntu 默认 JAVA 运行环境

  ```bash
  java -version
  ```

  如果当前是 openJDK 运行环境，那么需要删除。

  ```bash
  sudo apt-get remove openjdk*
  ```

- 安装 Oracle JAVA 运行环境

  路径：开发板光盘 -> 3、软件 -> Java 安装包 -> jre-8u271-linux-x64.tar.gz

  ```bash
  sudo mkdir /usr/local/java
  ```

  ```bash
  sudo tar vzxf jre-8u271-linux-x64.tar.gz -C /usr/local/java
  ```

- 修改`~/.bashrc`，文件末尾添加如下内容：

  ```bash
  export CLASSPATH=.:/usr/local/java/jre1.8.0_271/lib 
  export PATH=$PATH:/usr/local/java/jre1.8.0_271/bin
  ```

- 重启电脑，查看是否安装成功

  ```bash
  java -version
  ```



### 3.2 STM32CubeMX 安装

- 创建CubeMX目录，并将 开发板光盘 -> 05、开发工具 -> 02、ST官方开发工具 -> en.stm32cubemx_v6-0-1.zip 拷贝到该目录

  ```bash
  mkdir CubeMX && cd CubeMX
  ```

- 解压

  ```bash
  unzip en.stm32cubemx_v6-0-1.zip
  ```

- 更改文件属性

  ```bash
  chmod 777 SetupSTM32CubeMX-6.0.1.linux
  ```

  ```bash
  chmod 777 SetupSTM32CubeMX-6.0.1.exe
  ```

- 安装

  ```bash
  ./SetupSTM32CubeMX-6.0.1.linux
  ```

  > 坑：
  >
  > 1. 默认会把软件安装到 ~/STM32CubeMX/ 目录下。
  > 2. 由于是ssh到服务器上安装的，所以没有启动 UI 安装方式，使用的是终端安装方式
  > 3. 安装完成后，~/STM32CubeMX/ 目录下的可执行文件 STM32CubeMX 无法双击执行，只能在终端下执行 ./STM32CubeMX 执行。



### 3.3 STM32CubeIDE 安装

- 创建CubeIDE目录，并将 开发板光盘 -> 05、开发工具 -> 02、ST官方开发工具 -> en.st-stm32cubeide_1.4.0_7511_20200720_0928_amd64_sh.zip 拷贝到该目录

  ```bash
  mkdir CubeIDE && cd CubeIDE
  ```

- 解压

  ```bash
  unzip en.st-stm32cubeide_1.4.0_7511_20200720_0928_amd64_sh.zip
  ```

- 更改属性，并执行安装

  ```bash
  chmod 777 st-stm32cubeide_1.4.0_7511_20200720_0928_amd64.sh
  ```

  ```bash
  ./st-stm32cubeide_1.4.0_7511_20200720_0928_amd64.sh
  ```

  > 默认会把软件安装到 ~/st/stm32cubeide_1.4.0/ 目录下。



### 3.4 STM32CubeProgrammer 安装

- 创建CubeProgrammer 目录，并将 开发板光盘 -> 05、开发工具 -> 02、ST官方开发工具 -> en.stm32cubeprog_v2-5-0.zip 拷贝到该目录

  ```bash
  mkdir CubeProgrammer && cd CubeProgrammer
  ```

- 解压

  ```bash
  unzip en.stm32cubeprog_v2-5-0.zip
  ```

- 执行安装

  ```bash
  ./SetupSTM32CubeProgrammer-2.5.0.linux
  ```




> 坑：这几个软件的桌面图标不知道为啥有时候会不好用，还是直接从命令行启动比较稳定。



## 4. 源码编译



### 4.1 stm32wrapper4dbg 工具安装

TF-A 和 u-boot 编译时需要用到 stm32wrapper4dbg ，这个工具的源码位于 开发板光盘 -> 5、开发工具 -> stm32wrapper4dbg-master.zip，在 ubuntu 系统下解压编译即可。

```bash
unzip stm32wrapper4dbg-master.zip
```

```bash
cd stm32wrapper4dbg-master && make
```

```bash
sudo cp stm32wrapper4dbg /usr/bin
```

```bash
# 验证结果
stm32wrapper4dbg -s
```

> 从 code 代码库中下载的压缩包不能用，还是得用百度网盘的。



### 4.2 安装环境工具

```bash
sudo apt-get install device-tree-compiler bison flex lzop libssl-dev u-boot-tools -y
```



### 4.3 正点原子源码

目录：01_Source_Code/01、正点原子Linux出厂系统源码

- tf-a-stm32mp-2.2.r1-gaa9f87c-v1.5.tar.bz2
- u-boot-stm32mp-2020.01-g9c0df34f-v1.5.tar.bz2
- linux-5.4.31-g8c3068500-v1.8.tar.bz2



#### 4.3.1 TF-A

- 解压

  ```bash
  tar -xvf tf-a-*.tar.bz2
  ```

- 修改 Makefile.sdk，将 `CROSS_COMPILE` 改为 `arm-none-linux-gnueabihf-`

- 编译

  ```bash
  cd tf-a-stm32mp.2.2r1/ && make -f ../Makefile.sdk all
  ```

- 编译完成后，会在 Makefile.sdk 文件同级目录下生成 build 目录

- 正点原子使用的 tf-a-stm32mp157d-atk-trusted.stm32 文件位于 build/trust/



#### 4.3.2 uboot

修改 uboot 的 Makefile 文件，添加 ARCH 和 CROSS_COMPILE 配置：

```bash
ARCH = arm
CROSS_COMPILE = arm-none-linux-gnueabihf-
```

```bash
make distclean
```

```bash
make stm32mp157d_atk_defconfig
```

```bash
make V=1 DEVICE_TREE=stm32mp157d-atk all
```



#### 4.3.3 linux

在 linux 内核源码目录下，编写编译脚本 run_build.sh，简化执行：

```bash
#!/bin/sh 
make ARCH=arm CROSS_COMPILE=arm-none-linux-gnueabihf- distclean 
make ARCH=arm CROSS_COMPILE=arm-none-linux-gnueabihf- stm32mp1_atk_defconfig 
# make ARCH=arm CROSS_COMPILE=arm-none-linux-gnueabihf- menuconfig 
make ARCH=arm CROSS_COMPILE=arm-none-linux-gnueabihf- uImage dtbs LOADADDR=0XC2000040 -j16
```

为脚本添加可执行属性：

```bash
chmod 777 run_build.sh
```

执行脚本，编译内核：

```bash
./run_build.sh
```





## 5. Uboot 网络加载



### 5.1 Uboot 网络配置

- 配置 uboot 开发板 IP

  ```bash
  setenv ipaddr 192.168.100.187
  ```

- 配置开发板 MAC 地址

  ```bash
  setenv ethaddr b8:ae:1d:01:01:00
  ```

- 配置 Gateway

  ```bash
  setenv gatewayip 192.168.100.1
  ```

- 配置掩码

  ```bash
  setenv netmask 255.255.255.0
  ```

- 配置服务器 IP

  ```bash
  setenv serverip 192.168.100.185
  ```

- 保存配置

  ```bash
  saveenv
  ```

- 测试网络

  ```bash
  ping 192.168.100.185
  ```

  

### 5.2 主机网络服务搭建



#### 5.2.1 TFTP 服务

- 安装服务

  ```bash
  sudo apt-get install -y tftp-hpa tftpd-hpa xinetd
  ```

- 创建 TFTP 文件夹

  ```bash
  mkdir ~/tftpboot && chmod 777 ~/tftpboot
  ```

- 创建配置文件 /etc/xinetd.d/tftp （如果没有 /etc/xinetd.d 目录就自己创建）

  ```bash
  server tftp
  {
  	socket_type  = dgram
  	protocol     = udp
  	wait         = yes
  	user         = root
  	server       = /usr/sbin/in.tftpd
  	server_args  = -s /home/bryan/tftpboot/
  	disable      = no
  	per_source   = 11
  	cps          = 100 2
  	flags        = IPv4
  }
  ```

- 启动 TFTP 服务

  ```bash
  sudo service tftpd-hpa start
  ```

- 打开 /etc/default/tftpd-hpa 文件，将其修改为如下所示内容：

  ```bash
  # /etc/default/tftpd-hpa
  
  TFTP_USERNAME="tftp"
  TFTP_DIRECTORY="/home/bryan/tftpboot"
  TFTP_ADDRESS=":69"
  TFTP_OPTIONS="-l -c -s"
  ```

- 重启 TFTP 服务

  ```bash
  sudo service tftpd-hpa restart
  ```



#### 5.2.2 NFS 服务

- 安装服务

  ```bash
  sudo apt-get install nfs-kernel-server rpcbind
  ```

- 创建目录

  ```bash
  mkdir ~/nfs
  ```

- 更改 /etc/exports 文件，在文件末尾添加：

  ```bash
  /home/bryan/nfs *(rw,sync,no_root_squash)
  ```

- 重启服务

  ```bash
  sudo /etc/init.d/nfs-kernel-server restart
  ```

- 本机验证

  ```bahs
  sudo mount -t nfs -o nolock,vers=3 localhost:/home/bryan/nfs /mnt
  ```
  

- 修改 NFS 版本（大坑）

  Ubuntu 的 NFS 默认只支持 3 和 4 版本，uboot 默认使用的是版本 2，所以直接需要修改 Ubuntu 的 NFS 配置，否则会出现如下错误，导致无法挂载：

  ```bash
  [  112.489406] VFS: Unable to mount root fs via NFS, trying floppy.
  ```

  修改方法很简单，只需要在 Ubuntu 系统 /etc/default/nfs-kernel-server 文件的最后添加：

  ```bash
  RPCNFSDOPTS="--nfs-version 2,3,4 --debug --syslog"
  ```

  保存后重启 NFS 服务：

  ```bash
  sudo /etc/init.d/nfs-kernel-server restart
  ```

  

### 5.3 文件准备

- 将内核文件（uImage）、设备树文件（stm32mp157d-atk.dtb）放到 ~/tftpboot 目录下。
- 将文件系统（akt-image-qt5.12.9-rootfs.tar.bz2）解压到 ~/nfs 目录下。



### 5.4 Uboot 启动变量配置

- bootcmd

  ```bash
  setenv bootcmd 'tftp c2000000 uImage;tftp c4000000 stm32mp157d-atk.dtb;bootm c2000000 - c4000000'
  ```

- bootargs

  ```bash
  setenv bootargs 'console=ttySTM0,115200 root=/dev/nfs nfsroot=192.168.100.185:/home/bryan/nfs/rootfs,proto=tcp rw ip=192.168.100.187:192.168.100.185:192.168.100.1:255.255.255.0::eth0:off'
  ```

- 保存

  ```bash
  saveenv
  ```

- 启动

  ```bash
  boot
  ```

  
