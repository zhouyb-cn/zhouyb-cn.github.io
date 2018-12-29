---
title: 生命不息，折腾不止
date: 2018-10-10 16:37:56
tags: geek
---

最近老大提起了一个说很好玩的聊天客户端[weechat](https://weechat.org/)，说是很Geek，让我研究下给大家做一下分享，下面记录一下过程。

网上搜索了如何能够在weechat中收发微信消息，最初有点一头雾水，无从下手，只是知道weecaht是一个IRC客户端，官方首页的说明

> WeeChat, the extensible chat client.

> Fast — Light — Free software

轻量级的开源的软件，听说很多大佬在用（大佬都是在用一些看起来很高大上的东西）

按照[官方文档](https://weechat.org/files/doc/stable/weechat_user.en.html)一步一步来就可以，文档写的很清晰，只是需要花点时间理解weechat是做什么的就可以了，实际上比较简单，weecaht只是一个IRC客户端，IRC客户端也有很多，可以选择其它的客户端，weechat可扩展性比较大，装好weechat之后，又在github上找了一个控制网页微信来收发微信的库，在这里[https://github.com/MaskRay/wechatircd](https://github.com/MaskRay/wechatircd)，按照Readme操作就可以，最后创建一个wechat server

```shell
/connect wechat
```

即可加载到网页微信端的所有消息，并且可以是现在terminal中收发消息，看起来很高大上，并且可以随时查看以前的消息，都有日志记录，不方便的是对图片、音频、文件收发不太方便，显示到Terminal都是链接形式，

效果如下：

![weechat](/Users/yanbo/Desktop/屏幕快照 2018-12-29 下午4.15.58.png)

------

2018.12.28 补充：

升级了电脑系统到10.14.2，发现无法显示中文字符，输入中文也无法显示了，都变成了？ 查了资料应该是编码问题，最后升级了iTerm2到最新版本发现解决了。