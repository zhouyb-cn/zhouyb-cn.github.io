---
title: nginx 反向代理配置
date: 2018-01-23 15:18:30
tags: nginx
---

### 条件

两台能互相通讯的服务器，一台内网地址192.168.1.100，另外一台192.168.1.101，192.168.1.100服务器上设置反向代理，当用户访问地址时，转发到192.168.1.101服务器上

### 服务器配置

```
server {
    listen 80;

    server_name www.zhouyanbo.com;

    location / {
       proxy_set_header X-Real-IP  $remote_addr;
       proxy_set_header X-Forwarded-For $remote_addr;
       proxy_set_header Host $host;
       proxy_pass http://192.168.1.101:8080;
    }
}
```

192.168.1.101服务器上正常配置站点