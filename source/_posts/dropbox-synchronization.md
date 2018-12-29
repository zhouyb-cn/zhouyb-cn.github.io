---
title: Dropbox 同步问题
date: 2018-5-20 15:26:12
tags: dropbox tool
---

最初的时候下载了 [Dropbox](https://dropbox.com/) 做为我重要资料的网盘备份，可是最大的问题就是Dropbox的联网问题，众所周知此工具是被限制的，只能想一些办法FQ才可以使用，后来无奈从电脑上删除了，最近几天在整理[1password](http://1password.com)备份的时候，觉得icloud有点问题（原先使用icloud做备份），而且今年[icloud](https://www.icloud.com)中国由云上贵州运营了，也曝出了一些不好的新闻，不知真假，虽然我也在用icloud的免费5G的空间，反正也没有放什么隐私的材料照片，也就无所谓了，密码这个东西还是需要注意些，比较了一下Dropbox相对安全一些，决定重新捡起Dropbox。

此站点我是用的[Linode](https://welcome.linode.com)，每个月$5对我来说足够了（没什么浏览量），我在上面部署了ss服务，本地Mac安装ss客户端，添加服务器，选择自动代理模式，由于服务器在日本，延迟200多ms还算可以，基本的Google查询还是可以接受的。

下载Dropbox，安装在本地，安装好之后发现即使开启了ss还是一直处在连接中的状态，无法连接到服务器，于是在网上找到了方法，首先找到ss的自动模式的PAC文件 gfwlist.js，打来看到其中有一行

```javascript
var proxy = "SOCKS5 127.0.0.1:1080; SOCKS 127.0.0.1:1080; DIRECT;";
```

根据每个人的不同配置跟我的不一样，仅供参考，接着打开Dropbox的偏好设置，找到网络 --> 代理服务器，点击更改设置，选择手动，代理服务器类型：SOCKET5，服务器端口号写上就可以了，点击更新，过几秒发现Dropbox可以连接了

1password在偏好设置中选择同步方式为Dropbox，我只是在Mac跟iphone之间同步，iphone上开启代理即可，经试验，同步还是很快的，可以开心的使用了。
