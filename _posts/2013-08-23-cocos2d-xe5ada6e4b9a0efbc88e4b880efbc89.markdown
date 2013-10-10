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




    
    <code>git clone https://github.com/cocos2d/cocos2d-x.git</code>





目前最新源码是6.18发布的2.1.4版本，既然我是学习的目的，所以打算用最新代码：




    
    <code>git checkout develop</code>





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




    
    <code>cd $COCOS2DX_ROOT/tools/project-creator
    ./create_project.py -project PROJECT_NAME -package PACKAGE_NAME -language PROGRAMING_LANGUAGE</code>





要是记不住用法，可以直接运行`create_project.py`，会有如下的提示：




    
    <code>Usage: create_project.py -project PROJECT_NAME -package PACKAGE_NAME -language PROGRAMING_LANGUAGE
    Options:
      -project   PROJECT_NAME          Project name, for example: MyGame
      -package   PACKAGE_NAME          Package name, for example: com.MyCompany.MyAwesomeGame
      -language  PROGRAMING_LANGUAGE   Major programing lanauge you want to used, should be [cpp | lua | javascript]
    
    Sample 1: ./create_project.py -project MyGame -package com.MyCompany.AwesomeGame
    Sample 2: ./create_project.py -project MyGame -package com.MyCompany.AwesomeGame -language javascript</code>





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






  


    CompanyUSB Vendor ID
  


    
Acer

    
0502

  
  


    
ASUS

    
`0b05`

  
  


    
Dell

    
`413c`

  
  


    
Foxconn

    
`0489`

  
  


    
Fujitsu

    
`04c5`

  
  


    
Fujitsu Toshiba

    
`04c5`

  
  


    
Garmin-Asus

    
`091e`

  
  


    
Google

    
`18d1`

  
  


    
Haier

    
`201E`

  
  


    
Hisense

    
`109b`

  
  


    
HTC

    
`0bb4`

  
  


    
Huawei

    
`12d1`

  
  


    
K-Touch

    
`24e3`

  
  


    
KT Tech

    
`2116`

  
  


    
Kyocera

    
`0482`

  
  


    
Lenovo

    
`17ef`

  
  


    
LG

    
`1004`

  
  


    
Motorola

    
`22b8`

  
  


    
MTK

    
`0e8d`

  
  


    
NEC

    
`0409`

  
  


    
Nook

    
`2080`

  
  


    
Nvidia

    
`0955`

  
  


    
OTGV

    
`2257`

  
  


    
Pantech

    
`10a9`

  
  


    
Pegatron

    
`1d4d`

  
  


    
Philips

    
`0471`

  
  


    
PMC-Sierra

    
`04da`

  
  


    
Qualcomm

    
`05c6`

  
  


    
SK Telesys

    
`1f53`

  
  


    
Samsung

    
`04e8`

  
  


    
Sharp

    
`04dd`

  
  


    
Sony

    
`054c`

  
  


    
Sony Ericsson

    
`0fce`

  
  


    
Teleepoch

    
`2340`

  
  


    
Toshiba

    
`0930`

  
  


    
ZTE

    
`19d2`

  




### 最后的大招





`51-android.rules`文件中可以写这么一行：  

`SUBSYSTEM=="usb", ENV{DEVTYPE}=="usb_device", MODE="0666"`



