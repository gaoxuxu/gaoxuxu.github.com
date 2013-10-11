---
author: gavin
comments: false
date: 2013-08-23 16:12:20
layout: post
slug: cocos2d-x%e5%ad%a6%e4%b9%a0%ef%bc%88%e4%b8%80%ef%bc%89
title: cocos2d-x学习（一）
wordpress_id: 239
categories:
- Cocos2d-x
---

Ubuntu下使用eclipse搭建cocos2d-x开发环境

## 安装必要软件
Ubuntu上使用cocos2d-x开发游戏，需要安装一下一些软件：

  * git：使用git下载cocos2d-x源码。
  * eclipse+CDT：eclipse上使用CDT插件可以开发C/C++程序，不过感觉没visual studio好用，虽然Visual studio我没用过几次。
  * Python：创建项目时需要。
  * 如果要开发Android平台的游戏，还需要JDK、ADT和ant。

## 下载源码
使用git下载cocos2d-x源码：
    
    git clone https://github.com/cocos2d/cocos2d-x.git

目前最新源码是6.18发布的2.1.4版本，既然我是学习的目的，所以打算用最新代码：
   
    git checkout develop

## cocos2d-x项目目录结构
进入`$COCOS2DX_ROOT/samples/Cpp/HelloCpp/`，可以看到一个cocos2d-x项目的目录结构如下：  
+--HelloCpp  
+------Classes  
+------proj.android  
+------proj.emscripten  
+------proj.ios  
+------proj.linux  
+------proj.mac  
+------proj.nacl  
+------proj.qt5  
+------proj.tizen  
+------proj.win32  
+------Resources  

`Classes`文件夹放的是cocos2d-x项目的源代码，`Resources`文件夹放的是开发时用到的资源文件，`proj.*`文件夹则是各个平台下的项目。

## 将项目导入到eclipse
前面做完后，在创建自己的项目之前，有必要知道如何将cocos2d-x项目导入到eclipse：  

好吧，这个很简单，以HelloCpp为例。eclipse左侧空白处右键，`import-->C/C++-->Existing Code as Makefile Project-->选择$COCOS2DX_ROOT/samples/Cpp/HelloCpp/`目录，点击finish即可。

## 创建自己的项目
   
    cd $COCOS2DX_ROOT/tools/project-creator
    ./create_project.py -project PROJECT_NAME -package PACKAGE_NAME -language PROGRAMING_LANGUAGE

要是记不住用法，可以直接运行`create_project.py`，会有如下的提示：
   
    Usage: create_project.py -project PROJECT_NAME -package PACKAGE_NAME -language PROGRAMING_LANGUAGE
    Options:
      -project   PROJECT_NAME          Project name, for example: MyGame
      -package   PACKAGE_NAME          Package name, for example: com.MyCompany.MyAwesomeGame
      -language  PROGRAMING_LANGUAGE   Major programing lanauge you want to used, should be [cpp | lua | javascript]
    
    Sample 1: ./create_project.py -project MyGame -package com.MyCompany.AwesomeGame
    Sample 2: ./create_project.py -project MyGame -package com.MyCompany.AwesomeGame -language javascript

### 创建Android平台上的项目
在前面的版本，包括最新的2.1.4版本中，创建Android项目可以用`$COCOS2DX_ROOT/create-android-project.sh`。不过这种方式在未来会弃用，各平台新建项目统一使用上面所说的方法。  

如果是第一次在Linux上开发Android应用，手机连接到电脑，可能Eclipse不识别。这种情况可以用下面的办法解决（[参阅谷歌官方文档](http://developer.android.com/tools/device.html#setting-up)）：

  * 在终端运行`lsusb`，结果中找到自己的手机，比如：
`Bus 002 Device 002: ID 04e8:685e Samsung Electronics Co., Ltd`
格式很清楚，最后一部分的信息是手机生产商。我们需要的是ID后面的四位，这里是"04e8"
  * 终端运行`sudo gedit /etc/udev/rules.d/51-android.rules`
  * 文件打开后，输入`SUBSYSTEM=="usb", ATTR{idVendor}=="04e8", MODE="0666"`，保存并关闭。
  * 终端运行`chmod a+r /etc/udev/rules.d/51-android.rules`
  * 重新连接手机或者重启adb服务。

### 完整的供应商ID列表
<table>
<tr>
<th>Company</th>
<th>USB Vendor ID</th>
</tr>
<tr>
<td>Acer</td>
<td>0502</td>
</tr>
<tr>
<td>ASUS</td>
<td><code>0b05</code></td>
</tr>
<tr>
<td>Dell</td>
<td><code>413c</code></td>
</tr>
<tr>
<td>Foxconn</td>
<td><code>0489</code></td>
</tr>
<tr>
<td>Fujitsu</td>
<td><code>04c5</code></td>
</tr>
<tr>
<td>Fujitsu Toshiba</td>
<td><code>04c5</code></td>
</tr>
<tr>
<td>Garmin-Asus</td>
<td><code>091e</code></td>
</tr>
<tr>
<td>Google</td>
<td><code>18d1</code></td>
</tr>
<tr>
<td>Haier</td>
<td><code>201E</code></td>
</tr>
<tr>
<td>Hisense</td>
<td><code>109b</code></td>
</tr>
<tr>
<td>HTC</td>
<td><code>0bb4</code></td>
</tr>
<tr>
<td>Huawei</td>
<td><code>12d1</code></td>
</tr>
<tr>
<td>K-Touch</td>
<td><code>24e3</code></td>
</tr>
<tr>
<td>KT Tech</td>
<td><code>2116</code></td>
</tr>
<tr>
<td>Kyocera</td>
<td><code>0482</code></td>
</tr>
<tr>
<td>Lenovo</td>
<td><code>17ef</code></td>
</tr>
<tr>
<td>LG</td>
<td><code>1004</code></td>
</tr>
<tr>
<td>Motorola</td>
<td><code>22b8</code></td>
</tr>
<tr>
<td>MTK</td>
<td><code>0e8d</code></td>
</tr>
<tr>
<td>NEC</td>
<td><code>0409</code></td>
</tr>
<tr>
<td>Nook</td>
<td><code>2080</code></td>
</tr>
<tr>
<td>Nvidia</td>
<td><code>0955</code></td>
</tr>
<tr>
<td>OTGV</td>
<td><code>2257</code></td>
</tr>
<tr>
<td>Pantech</td>
<td><code>10a9</code></td>
</tr>
<tr>
<td>Pegatron</td>
<td><code>1d4d</code></td>
</tr>
<tr>
<td>Philips</td>
<td><code>0471</code></td>
</tr>
<tr>
<td>PMC-Sierra</td>
<td><code>04da</code></td>
</tr>
<tr>
<td>Qualcomm</td>
<td><code>05c6</code></td>
</tr>
<tr>
<td>SK Telesys</td>
<td><code>1f53</code></td>
</tr>
<tr>
<td>Samsung</td>
<td><code>04e8</code></td>
</tr>
<tr>
<td>Sharp</td>
<td><code>04dd</code></td>
</tr>
<tr>
<td>Sony</td>
<td><code>054c</code></td>
</tr>
<tr>
<td>Sony Ericsson</td>
<td><code>0fce</code></td>
</tr>
<tr>
<td>Teleepoch</td>
<td><code>2340</code></td>
</tr>
<tr>
<td>Toshiba</td>
<td><code>0930</code></td>
</tr>
<tr>
<td>ZTE</td>
<td><code>19d2</code></td>
</tr>
</table>
### 最后的大招
`51-android.rules`文件中可以写这么一行：  

`SUBSYSTEM=="usb", ENV{DEVTYPE}=="usb_device", MODE="0666"`
