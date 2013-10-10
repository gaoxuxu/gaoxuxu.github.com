---
author: gavin
comments: true
date: 2013-04-19 23:10:58
layout: post
slug: ubuntu%e4%b8%8bgolang%e7%9a%84emacs%e9%85%8d%e7%bd%ae
title: Ubuntu下golang的emacs配置
wordpress_id: 155
categories:
- Go
tags:
- emacs
- Go
---


	参考了[《Go Web编程》](https://github.com/astaxie/build-web-application-with-golang/blob/master/ebook/01.4.md) 






	这里说一下我的步骤：






	1.配置emacs高亮显示 




    
        cp $GOROOT/misc/emacs/* ~/.emacs.d/


2.安装gocode



	  







	  





    
        go get -u github.com/nsf/gocode


3.配置gocode  


    
        ~ cd $GOROOT/src/pkg/github.com/nsf/gocode/emacs
        ~ cp go-autocomplete.el ~/.emacs.d/
        ~ gocode set propose-builtins true
        propose-builtins true
        ~ gocode set lib-path "/home/gavin/go/pkg/linux_amd64" // 这个是自己的路径
        lib-path "/home/gavin/go/pkg/linux_amd64"
        ~ gocode set
        propose-builtins true
        lib-path "/home/gavin/go/pkg/linux_amd64"





	4.安装[auto-complete](http://cx4a.org/software/auto-complete/) 






	下载并解压






	  





    
        cd ~/.emacs.d
        mkdir auto-complete


然后cd到auto-complete解压到的文件夹



	  







	  





    
        make install DIR=$HOME/.emacs.d/auto-complete


安装成功后配置.emacs文件



	  







	  





    
        (add-to-list 'load-path "/home/gavin/.emacs.d/auto-complete")
        (require 'auto-complete-config)
        (add-to-list 'ac-dictionary-directories "/home/gavin/.emacs.d/auto-complete/ac-dict")
        (ac-config-default)


5.配置.emacs文件



	  







	  





    
        ;; golang mode
        (add-to-list 'load-path "/home/gavin/.emacs.d")
        (require 'go-mode-load)
        (require 'go-autocomplete)





	  







	  


