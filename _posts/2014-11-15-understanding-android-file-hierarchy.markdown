---
author: gavin
comments: true
date: 2014-11-16 14:28:09
layout: post
title: Android文件层次结构
categories:
- Java
- Android
---

前几天阅读Android 5.0的源码，发现自己以前没有了解过Android文件层次，这篇文章简单记录了我了解的一些信息。

Android文件层次不过是Linux文件层次的变体。在Linux／Android／Unix（为简单起见，下午一律称为Linux）中，文件层次是一棵单独的树，树的顶部也就是根部是“`/`”，在“`/`”之下是文件和目录。Linux文件层次缺乏Windows中驱动的概念，它将文件系统挂载在一个目录来创建一棵单独的树。对基于媒介的文件系统来说，文件系统相当于一些媒介的分区，这使得文件系统无论存在于本地还是远程设备都一样。所有事物都是从根部开始的一棵单独整体树的一部分。

所有文件或目录的路径都以根部“`/`”开始。例如，我的文件管理器显示的内部闪存的路径是`/mnt/sdcard`，它也被简称为“`sdcard`”。如果你的Android设备有外部SD卡，你还会看到`/mnt/extSdCard`。当然可能实际的名字会不一样，但概念都是一样的。例如，CyanogenMod将外部闪存挂载到了`/mnt/sdcard`，而内部闪存则挂载到了`/mnt/emmc`。这里需要记住的一个重点是，每个目录都代表了一个文件系统的**挂载点**。对普通用户来说，Android文件仅仅用来展示了你访问的文件系统，这些文件系统只是整体文件层次的一部分。

要看到完整的Android文件层次的话，需要“root”权限。这种情况下，“root”指的是有系统管理员特权的特殊用户账户。如果你“root”了一个Android设备，你就会提升为root账户。没有自动授予root权限的其中一个理由是，极小的错误会导致大麻烦。然而，它为有Linux命令和Linux系统管理经验的人揭开了Android底层结构的面纱。

##Android文件系统

Android使用的是Linux内核，所有文件和目录都被称之为虚拟文件系统（VFS）。每个文件系统都是VFS的实现。每个文件系统有一个单独的内核模块，这个内核模块注册了它由VFS支持的操作。通过抽象和实现分离，增加一个新文件系统就变为写一个内核模块的问题。这些模块要么是内核的一部分，要么动态按需加载。Android内核自带从AIX（IBM的Unix变体）的Journal文件系统（JFS）到Amiga文件系统的文件系统的大集合的一个子集。这里边所有的花样对用户都是隐藏的，当内核挂载一个文件系统时它会处理所有操作。

内核配置文件确定什么文件系统模块要被编译，以及它们是要在内核中构建，还是动态加载。因此，Android内核只包含了与它运行有关的文件系统模块。实际上，我没听说过有编译了所有文件系统模块的独立Linux分支，因为有些文件系统是依赖于硬件结构的。不同的Android设备支持的文件系统有所不同，下面是比较常见的闪存文件系统：

* ***exFAT***：The extended File Allocation Table，微软专有的闪存文件系统。由于许可证的限制，它不是标准Linux内核的一部分。不过，一些制造商提供了Android对该文件系统的支持。
* ***F2FS***：The Flash-Friendly File System，三星于2012年推出的开源Linux文件系统。
* ***JFFS2***：The Journal Flash System version 2，Ice Cream Sandwich以来AOSP（Android Open Source Project）默认的闪存文件系统。JFFS2是原始JFFS的替代品。
* ***YAFFS2***：Yet Another Flash File System 2，2.6.32版本内核的默认AOSP闪存文件系统。新的内核版本不支持YAFFS2，在kernel.org的最新内核版本的源码树中已经找不到它了。不过，有些手机生产厂商可能会继续支持YAFFS2。

除了闪存文件系统，Android设备通常支持以下的media-based文件系统：

* ***EXT2/EXT3/EXT4***：EXTended文件系统是标准的Linux文件系统，EXT4是当前版本。从2010年以后，EXT4经常被用来代替YAFFS2或者JFFS2作为Android设备的内部闪存文件系统。
* ***MSDOS***：MMSDOS驱动器支持FAT12、FAT16和FAT32文件系统。
* ***VFAT***：VFAT实际上不是文件系统，它是FAT12、FAT16和FAT32文件系统的一个扩展。因此，你总是看到VFAT内核模块和MSDOS模块结合在一起。外部SD卡通常使用VFAT格式。

上面是media-based文件系统。VFS也支持伪文件系统，也就是非media based文件系统。Linux内核支持一些伪文件系统，下面是对Android设备比较重要的一部分：

* ***cgroup***：cgroup（control group）伪文件系统提供了访问和定义各种内核参数的方法。cgroup是伪文件系统，有许多不同的进程控制组。如果你的Android设备支持进程控制组，你可以在`/proc/cgroups`文件中找到这些组的列表。Android使用cgroup来acct（用户账户控制）和cpuctl（CPU控制）。
* ***rootfs***：这个文件系统服务于根文件系统（“`/`”）挂载点。
* ***procfs***：procfs文件系统通常挂载在`/proc`目录，它们反映了一些内核数据结构。在这些文件上的操作实际上读的是内核中的数据。这些文件夹反映了每个正在运行的任务的进程ID（实际上是线程组leader的进程ID）。`/proc/filesystems`文件中创建了当前已经注册的文件系统的列表。NODEV下面的就是伪文件系统，它们没有相关的设备。`/proc/sys`目录包含了一些可调的内核参数。
* ***sysfs***：内核的设备模型是一个面向对象数据结构，反映了内核通过sysfs文件系统所知的设备，它一般挂载在`/sys`目录。当内核发现一个新的设备，它就在`/sys/devices`目录构建一个对象。内核使用socket通信将新设备的信息告诉udevd守护进程，udevd守护进程在`/dev`目录下构建一条记录。`/sys/fs`目录包含media-based文件系统的内核对象结构。`/sys/module`目录包含每个已加载内核模块的对象。
* ***tmpfs***：tmpfs文件系统经常挂载在`/dev`目录。由于它是伪文件系统，因此`/dev`目录下的所有数据在重启设备后都会丢失。

上面看到的一些文件系统，只是冰山一角。对没有root的设备，这些文件系统和目录都是隐藏起来的，你没有权限去访问它们。我的三星Galaxy S3在设置按钮的开发者选项中提供了USB调试功能，打开这个选项后就可以用adb（Android Debug Bridge）连接到三星Galaxy S3.然而它只提供用户级别的权限，它提供了对绝大多数目录的访问和绝大多数Linux命令。这个选项应该在其他三星Android设备也有。你也可以用[Terminal Emulator](https://play.google.com/store/apps/details?id=jackpal.androidterm)试试。它在我的三星Galaxy S3和运行CyanogenMod 10（Jelly Bean）的B&N Nook Color上都是可以工作的。

##Android文件层次

由上所述，Android文件层次是Linux文件层次的一个修改过的版本。不同Linux版本和不同生产厂商的结构有细微的差别。然而，差别非常细小。下面对Jelly Bean的AOSP目录结构的最顶层做一个简单的总结：

* ***acct***：这个目录是acct cgroup（control group）的挂载点。
* ***cache***：`/dev/block/mtdblock2`分区的挂载点（分区名称可能有所不同）。缓存的大小被限制为这个分区的大小。
* ***d***：`/sys/kernel/debug`的一个符号链接。
* ***data***：`/dev/block/mtdblock1`分区的挂载点。
* ***default.prop***：这个文件定义了各种默认属性。
* ***dev***：tmpfs文件系统的挂载点，定义了应用程序可用的设备。`/dev/cpuctl`目录是cpuctl控制组的挂载点，使用cgroup伪文件系统。
* ***etc***：`/system/etc`的一个符号链接。
* ***init***：处理`init.rc`文件的一个二进制程序。`init.rc`文件引入其他`init.*.rc`文件。Android启动时，内核会在启动进程的最后运行init程序。`init.rc`文件值得一读，它告诉我们一些Android设备配置的事情。由于Android不支持`/etc/sysctl.conf`，所以`/proc/sys/kernel`参数的更新就成为了`init.rc`文件的一部分。除非你对Linux内核的内部工作机制有一个好的理解，最好不要修改这些参数。对`/dev/cpuctl`的参数也是一样的道理。
* ***mnt***：除了挂载内部和外部SD卡，这个目录还是其他文件系统的挂载点。`/mnt/asec`目录是一个tmpfs文件系统的挂载点，它是Android安全机制的一部分。`/mnt/obb`目录是一个tmpfs文件系统的挂载点，它用来存储应用程序文件超出50MB后的扩展文件。`/mnt/secure`目录是Android安全机制的另外一个组件。你也可以看到一个或多个USB设备的挂载点。
* ***proc***：procfs文件系统的挂载点，用来提供对内核数据结构的访问。
* ***root***：root账户的home目录。
* ***sbin***：比标准Linux发行版中的`/sbin`目录小很多，但它确实包含了几个重要守护进程的二进制文件。
* ***sdcard***：`/mnt/sdcard`的一个符号链接。
* ***sys***：sysfs伪文件系统的挂载点，sysfs伪文件系统是内核的设备对象结构的反映。这个目录下有很多信息，但它需要理解内核设备模型。简而言之，这些目录表示内核对象，而文件是这些对象的属性。
* ***system***：这个目录是`/dev/block/mtdblock0`的挂载点，这个目录下就是你通常在标准Linux发行版中root目录下看到的目录。这些目录包括`bin`、`etc`、`lib`、`usr`和`xbin`。
* ***ueventd.goldfish.rc ueventd.rc***：这些文件定义了`/dev`目录的配置规则。
* ***vendor***：`/system/vendor`的一个符号链接。

上面只是Android文件结构的一个简单描述。要理解桌面之下Android的工作，你需要了解Linux的基本知识和Linux命令行。对于内核参数，你需要知道Linux内核的工作知识。在以后的文章中我将详细探讨Android文件层次。