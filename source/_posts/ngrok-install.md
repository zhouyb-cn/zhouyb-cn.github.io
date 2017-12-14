---
title: ngrok服务器搭建
date: 2017-12-14 17:29:55
tags: ngrok centos7 mac
---

> 记录ngrok的安装过程

### 准备
1. 一台linode服务器，系统 Centos 7 amd64
2. 一个公网域名 zhouyanbo.com

#### 一、安装软件

##### 1. 安装gcc
```bash
yum install gcc
```

##### 2. 安装go环境

下载最新的go版本
```bash
wget https://redirector.gvt1.com/edgedl/go/go1.9.2.linux-amd64.tar.gz
```
解压
```bash
tar -C /usr/local/ -zxvf go1.9.2.linux-amd64.tar.gz
```
<!-- more -->

添加环境变量
```
vim /etc/profile
```
在文件中写入以下两行
```
export GOROOT=/usr/local/go
export PATH=$PATH:$GOROOT/bin
```
保存退出，执行下面命令使生效
```
source /etc/profile
```

命令行中输入go version 输出版本号即为安装成功

#### 二、在服务器上搭建ngrok服务

##### 1. 下载ngrok源码

```
cd /usr/local/src
git clone https://github.com/inconshreveable/ngrok.git
```
##### 2. 生成证书

```
cd ngrok

export NGROK_DOMAIN="zhouyanbo.com"

openssl genrsa -out rootCA.key 2048
openssl req -x509 -new -nodes -key rootCA.key -subj "/CN=$NGROK_DOMAIN" -days 5000 -out rootCA.pem
openssl genrsa -out device.key 2048
openssl req -new -key device.key -subj "/CN=$NGROK_DOMAIN" -out device.csr
openssl x509 -req -in device.csr -CA rootCA.pem -CAkey rootCA.key -CAcreateserial -out device.crt -days 5000
```
替换原有证书

```
cp rootCA.pem assets/client/tls/ngrokroot.crt
cp device.crt assets/server/tls/snakeoil.crt
cp device.key assets/server/tls/snakeoil.key
```
##### 3. 服务端编译生成ngrokd

```
GOOS=linux GOARCH=amd64 make release-server
```
编译成功后在当前目录的bin目录下可找到ngrokd文件

##### 4. 编译生成ngrok客户端

```
GOOS=darwin GOARCH=amd64 make release-client
```
完成后会在 /usr/local/src/ngrok/bin/darwin_amd64/ 下发现 ngrok 文件, 用rsync命令拷贝文件到Mac上

##### 5. mac上编写ngrok配置文件

```
server_addr: "zhouyanbo.com:8083"
trust_host_root_certs: false
tunnels:
  http:
    subdomain: "ngrok"
    proto:
      http: "8090"
      
  https:
    subdomain: "ngrok"
    proto:
      https: "8091"
```

##### 6. 服务端开启服务，客户端连接

服务端
```
./bin/ngrokd -tlsKey="assets/server/tls/snakeoil.key" -tlsCrt="assets/server/tls/snakeoil.crt" -domain="$NGROK_DOMAIN"  -httpAddr=":80" -httpsAddr=":8082" -tunnelAddr=":8083"
```

客户端
```
./ngrok -config ngrok.cfg start http https
```

连接成功后会显示如下

```
Tunnel Status                 online
Version                       1.7/1.7
Forwarding                    https://ngrok.zhouyanbo.com:8082 -> 127.0.0.1:8091
Forwarding                    http://ngrok.zhouyanbo.com -> 127.0.0.1:8090
Web Interface                 127.0.0.1:4040
# Conn                        0
Avg Conn Time                 0.00ms
```

接下来在本地搭建项目，开启8090端口，就可以通过ngrok.zhouyanbo.com来访问了，以上步骤参考[这里](https://ubock.com/article/31-2#clist)

#### 三、问题

由于zhouyanbo.com放有我的个人bolg网站，使用80端口，搭建ngrok服务是为了微信公众号服务端在本地调试功能，只能使用80端口，两个办法解决

1. 再买个域名
2. 调试微信公众号的时候关闭blog站点

