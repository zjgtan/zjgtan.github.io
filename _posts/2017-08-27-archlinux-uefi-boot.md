---
layout: post
title: "以UEFI模式安装&启动Archlinux"
tags: archlinux linux UEFI 机器学习
date: 2017-08-27 20:00:00 +0800
---
前两天给一台机器装[Archlinux](https://www.archlinux.org)，打算用UEFI来引导启动，不过在做的过程中碰到了一些坑，写篇文章来记录一下每个可能掉坑的环节。
## 安装之前
### 硬盘分区
硬盘分区要使用`gdisk`程序，并按照以下步骤操作：

1. 创建一个空的GPT分区表。
2. 创建一个分区，分区类型码需要手工输入`EF00`，标记为ESP分区(ESP = EFI System Partition)。
3. 使用`mkfs.fat -F32`命令将ESP分区格式化成FAT32文件系统。

如果使用`parted`来分区的话，安装Boot Loader的时候会提示指定的分区不是ESP分区。
### 分区挂载
ESP分区必须挂载在ROOT分区的`/boot`位置。
## 安装过程中
务必使用`genfstab -U`来生成`/etc/fstab`，生成`UUID=XXX-YYY-ZZZ`格式的分区名称，下面要用到。
## 安装之后
### 安装Boot Loader
在安装并配置完基本系统之后，安装systemd-boot来负责引导启动：

    # bootclt --path=/boot install

### 配置Boot Loader
systemd-boot在引导的时候，会从*ESP*/loader/loader.conf中读取配置，并使用*ESP*/loader/entries/目录下的配置文件进行引导。

上面的`bootctl`命令在安装的时候并不会生成默认的配置文件，所以需要自己写：

*ESP*/loader/loader.conf：

    default arch # 这个选项将默认的启动entry指定为entries/arch.conf

*ESP*/loader/entries/arch.conf：

    title   Arch Linux
    linux   /vmlinuz-linux
    initrd  /initramfs-linux.img
    options root=UUID=XXX-YYY-ZZZ rootfstype=ext4 add_efi_memmap

其中的options里面的`root=UUID=XXX-YYY-ZZZ`部分，指定了挂载到根目录的分区的UUID，需要打开`/etc/fstab`查看得知。

以上就是Archlinux使用UEFI引导启动，在安装过程中所有需要注意的点了。
