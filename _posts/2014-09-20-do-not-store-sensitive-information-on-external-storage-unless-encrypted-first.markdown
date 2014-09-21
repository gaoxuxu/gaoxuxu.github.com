---
author: gavin
comments: true
date: 2014-09-20 14:11:30
layout: post
title: Android代码红线:不要将敏感信息明文保存在外部存储设备（SD卡）上
categories:
- Java
- Android
---

##不要将敏感信息明文保存在外部存储设备（SD卡）上

Android提供了几种数据存储方式，其中有一种就是**外部存储设备（`/sdcard`,`/mnt/sdcard`）**。“外部存储设备”的例子包括设备中的微型或者标准尺寸的SD卡，装载到PC的Android设备存储，以及`Android/obb`文件夹。

在Android 4.1之前，外部存储设备中的文件是全球可读的（world-readable）。在Android 1之前，外部存储设备中的文件是全球可写的（world-writable）。从Android 1到Android 4.3，一个app要向其他app保存在外部存储设备上的文件写入，只需要一个权限`WRITE_EXTERNAL_STORAGE`。从Android 4.4开始，采用了基于文件夹结构创建的文件组和文件行为，它允许一个app利用权限来管理／读／写基于它包名的文件夹结构中的文件。从Android 4.4开始，用户（包括apps）从Android设备管理的其它app主要的外部存储空间脱离了开来。

上面所说的缺乏限制的后果就是，保存在外部存储的文件可以被设备上安装其它的app读或者修改（对于允许读／写的Android版本）；如果存储到可以拆卸的外部存储设备的话，那么谁都可以访问这些文件，比如PC（还有一种情况就是，设备内部的外部存储设备被卸载并且装载到其它地方）。

Android API指南[Storage Options](http://developer.android.com/guide/topics/data/data-storage.html)的陈述如下： 

> Caution: External storage can become unavailable if the user mounts the external storage on a computer or removes the media, and there's no security enforced upon files you save to the external storage. All applications can read and write files placed on the external storage and the user can remove them.

开发者如果没有对敏感信息进行编码，就不能将其保存在外部存储设备上，因为保存在外部存储上的文件不能保证其可用性、完整性和机密性。

###不符合该规则的代码示例

下面的代码在外部存储中创建了一个文件，并将敏感信息写入了该文件：

    private String filename = "myfile";
    
    private String string = "sensitive data such as credit card number";
    FileOutputStream fos = null;
    
    try {
      File file = new File(getExternalFilesDir(TARGET_TYPE), filename);
      fos = new FileOutputStream(file, false);
      fos.write(string.getBytes());
    } catch (FileNotFoundException e) {
      // handle FileNotFoundException
    } catch (IOException e) {
      // handle IOException
    } finally {
      if (fos != null) {
        try {
          fos.close();
        } catch (IOException e) {
        
        }
      }
    }

####验证

一般来说，一个应用保存的文件位置如下：

    /sdcard/Android/data/com.company.app/files/save/appdata/save_appdata

###解决方案1（将文件保存在内部存储）

下面的代码使用了`openFileOutput()`方法在应用的`data`文件夹创建了文件`myfile`，并将其权限设置为`MODE_PRIVATE`，因此其他app无法访问这个文件：

    private String filename = "myfile";
    
    private String string = "sensitive data such as credit card number";
    FileOutputStream fos = null;
    
    try {
      fos = openFileOutput(filename, Context.MODE_PRIVATE);
      fos.write(string.getBytes());
      fos.close();
    } catch (FileNotFoundException e) {
      // handle FileNotFoundException
    } catch (IOException e) {
      // handle IOException
    } finally {
      if (fos != null) {
        try {
          fos.close();
        } catch (IOException e) {
        
        }
      }
    }

###解决方案2

在将数据保存在外部存储（比如SD卡）中之前，请先将数据安全地编码。

翻译自[CERT](https://www.securecoding.cert.org/confluence/display/java/DRD00-J.+Do+not+store+sensitive+information+on+external+storage+%28SD+card%29+unless+encrypted+first)