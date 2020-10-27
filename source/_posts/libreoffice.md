---
title: 使用libreoffice转换文档成pdf
date: 2018-07-18 17:03:07
tags: linux
---

### 需求

将doc、docx、xls、xlsx、ppt、pptx格式的转换成pdf文档

### 实施

系统是centos 7，这里使用libreoffice，在转换过程中发现，只要是文件中包含中文字符，转换之后文件中成了乱码，不可读

### 解决

由于centos 7系统中缺少对中文的支持，需要安装中文字库，参考这里[centos安装中文字库](https://blog.csdn.net/wlwlwlwl015/article/details/51482065)，
安装完成后重启libreoffice即可

附：[转换脚本](https://github.com/lugnsk/pyodconverter)
