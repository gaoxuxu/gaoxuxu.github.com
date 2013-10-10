---
author: gavin
comments: true
date: 2013-03-14 22:49:13
layout: post
slug: android%e5%b7%a5%e5%85%b7%e4%b9%8blint%ef%bc%88%e4%b8%80%ef%bc%89
title: Android工具之lint（一）
wordpress_id: 115
categories:
- Android
tags:
- Android
- lint
---

### 
	lint简介：






	lint是Android提供的一种静态代码分析工具，用它扫描Android工程源文件可以发现一些关于源文件正确性、安全性、性能、可用性、可访问性和国际化等方面潜在的bug并且可以给出合理的优化改进建议。





### 
	lint的语法：



lint的语法比较简单：  


    
    lint [flags] <project directory>


例如，如果想要扫描myproject文件夹及其子文件夹下的Java和XML文件，可以使用下面的命令，结果会显示在控制台中：  


    
    <span>lint myproject</span> 


上面的命令会检测源文件中所有的问题，如果只是想检测某些特定的问题，比如说XML缺少命名空间前缀的问题，可以使用下面的命令：  


    
    lint --check MissingPrefix myproject


lint还可以将检测结果存储到一个指定的html文件中。比如要检查myproject文件夹的accessibility问题，并将检测结果存储为accessibility_report.html文件，可以使用下面的命令：  




	  





    
    lint --check Accessibility --HTML accessibility_report.html myproject





	  






### 
	lint可用的命令行选项：






	**分类：检测（Checking）**






	--disable <list>：不检测list中指定的问题。如果list中包含多个ID或分类，应用半角逗号将它们分开。






	--enable <list>：会检测所有lint默认支持的以及list中指定的问题，如果list中包含多个ID或分类，应用半角逗号将它们分开。






	--check <list>：检测list中指定的问题，如果list中包含多个ID或分类，应用半角逗号将它们分开。






	-w 或 --nowarn：只检测error级别的问题，会忽略warning级别的问题。






	-Wall：检测所有的warning级别的问题，包括那些默认不检查的。






	-Werror：将所有的warning级别的问题都提交为error级别。






	--config <filename>：使用配置文件来决定检测或不检测某些问题。如果工程包含了一个lint.xml文件，则这个lint.xml就被认为是默认的配置文件。






	**分类：报告（Reporting）**






	--html <filename>：生成一个HTML格式的报告。报告会输出到参数指定的文件中，内容包括lint检测到问题的代码片段、问题的详细描述并且会链接到源文件中。






	--url <filepath>=<url>：在HTML输出中，将本地路径前缀<filepath>替换为url前缀<url>。--url选项只用于使用--html选项生成HTML报告的情况下，可以使用多个由半角逗号分隔的<filepath>=<url>映射，如果不链接到文件，可以使用--url none。






	--simplehtml <filename>：生成一个简单的HTML报告。报告会保存到参数指定的输出文件中。






	--xml <filename>：生成一个XML报告，报告会保存到参数指定的输出文件中。






	--fullpath：在lint的检测结果中显示文件全名。






	--showall：不会截断长信息或交叉位置列表。






	--nolines：输出中不包含源码的代码片段。






	--exitcode：如果发现error，则设置退出代码为1。






	--quiet：不显示进度指示器。






	**分类：帮助（Help）**






	--help：列出lint工具支持的命令行参数。使用--help <topic>查阅指定主题的帮助信息，必须“suppress”。






	--list：列出lint可以检测的问题的ID和简要描述。






	--show：列出lint可以检测的问题的ID和详细描述。使用--show <ids>查看指定lint问题的描述。






	--version：显示lint的版本。





### 
	在Java和XML源文件中配置：






	如果不检查指定的Java类或方法，可以使用@SuppressLint注解。






	如果不检查XML文件中的指定位置，可以使用tools:ignore属性。

