---
title: 处理TIF、EPS图片
date: 2017-09-11 16:41:07
tags: image tif eps
---

工作中需要处理图片，格式包括png、jpg、svg、gif、tif、eps，等等。。。 前面几种还知道
第一次听说还有tif、eps格式的图片，看来还是见识少啊

在网上找资料发现很少有处理这两种格式的图片的，尤其是eps，最后找到[imagemagick](https://www.imagemagick.org)，看了介绍之后觉得真是功能强大啊，无所不能

具体安装及使用方法见[官网](https://www.imagemagick.org/script/download.php)

使用过程中发现用convert命令对tif图片处理时，会在目录下生成不止一个图片

``` bash
convert -resize 200 image.tif target_path
```

查资料发现tif是有图层的，一个tif图片可能会有多个layer，如果只要想生成一个图片的话，修改命令如下

``` bash
convert -resize 200 image.tif[0] target_path
```
