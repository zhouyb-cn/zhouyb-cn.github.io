---
title: php 502
date: 2018-01-25 13:33:20
tags: php 502
---

**记录一次bug查找经历**

### 起因

偶然间打开一个以前的项目，浏览器显示 502 Bad Gateway，最开始以为php-fpm没有启动，后来发现不是这个原因

### 排查过程

1. nginx、php-fpm都服务正常
2. 配置无误，访问root目录下的html文件都能正常访问，php文件都是502，定位到php-fpm出问题
3. 查看nginx错误日志，报错信息(我电脑php-fpm监听的9011端口)
```
[error] 16914#16914: *1314340 recv() failed (104: Connection reset by peer) while reading response header from upstream, client: ****, server: ****, request: "GET **** HTTP/1.1", upstream: "fastcgi://127.0.0.1:9011", host: "zhouyanbo.com"
```
4. 检查php-fpm.log文件(一般位置在/var/log/php-fpm/error.log)，没有错误信息，修改/etc/php-fpm.d/www.conf配置
```
catch_workers_output = yes
```
5. 重新请求查看php-fpm.log日志，报错
```
[pool www] child 31776 said into stderr: "ERROR: Connection disallowed: IP address '127.0.0.1' has been dropped."
```
6. 最后修改/etc/php-fpm.d/www.conf配置，原来是any改为127.0.0.1
```
listen.allowed_clients = 127.0.0.1
```
7. 重启能正常访问

### 疑问

按道理说listen.allowed_clients = any也是能正常访问的，any包括了所有，但是莫名其妙会出现502的问题，暂时修改为127.0.0.1能正常访问了，以后有时间再研究下，先做下记录