---
title: install centos 7 failed
date: 2017-08-11 10:49:37
tags: centos-7 error install
---

### 起因

最近公司新购几台服务器，需要先装好系统再拿到机房，前面两台430服务器安装系统都很顺利，在安装第三台730XD的时候报错failed to start switch root

### 解决

经过检查，是由于找不到U盘加载不到镜像导致这个错误，730服务器上三个USB插孔（前面一个后面两个），不知道前后有什么区别

### 方案

* 在启动界面，按e进入编辑模式，
* 修改启动参数 vmlinuz initrd=initrd.img linux dd quiet
* 重启会列出所有磁盘，包括U盘盘符，一般U盘会是/dev/sdb*，或者/dev/sda*，但是我发现这次U盘是/dev/sdc4
* 接着重启，重新进入编辑模式，修改启动参数 vmlinuz initrd=initrd.img inst.stage2=hd:/dev/sdc4 quiet 根据实际情况修改盘符
* ctrl + x 执行就能顺利看到安装界面
