---
layout: post
title: 在国内使用 Google Photos Backup
categories: google
---

不知曾几何时，就已经把自己的照片全部托管在 Google 了，当时还是存在于 Google+ 里的一个项目，现在独立成 Google Photos, 它的好处就不需要多说了，免费无限制的高品质图片存储，强大的检索和领先星球的在线图片处理体验，不时上去还会有一些小惊喜。

但是众所周知的原因，这么好用的一款产品在国内并不能直接访问，它属下的桌面客户端 Google Photos Bakcup 就更不用说了。在 Windows 平台下，只需要打开 Shadowsocks 代理即可。但是 Mac OS X 平台下就没那么顺利了，直接打开 App 登录会出现以下问题：

> ![Google Photos Backup - There was a problem authenticating your account](/imgs/using-google-photos-backup-in-china-001.png)
>
> There was a problem authenticating your account.

这是由于 OS X 客户端 Google Photos Backup 使用的协议是 Socks5，Shadowsocks 并不支持走这个协议，所以还需要一个 [Proxifier](https://www.proxifier.com/)，它的原理是让 SOCKS5 协议的也支持走 Shadowsocks 通道。

## 具体方法如下

1. 安装并运行 Proxifier for Mac 后，点击 **Proxies** 按钮。

    ![Proxifier for Mac](/imgs/using-google-photos-backup-in-china-002.png)

2. 在弹出的 Proxies 对话框中点击新增 **Add** 按钮，新增一个代理方式。

    ![Proxifier for Mac - Proxies](/imgs/using-google-photos-backup-in-china-003.png)

3. 服务器 Server 下填写 **127.0.0.1**，端口处 **Port** 填上自己定义的端口，默认是 1080，协议 **Protocol** 选上 **SOCKS Version 5**，其次点 OK 确认。

    ![Proxifier for Mac - Add Proxy](/imgs/using-google-photos-backup-in-china-004.png)

4. 这时候会弹出一个框询问是否全局默认走刚才添加的代理服务器，我们单击 **No**。

    ![Proxifier for Mac - Set Default Proxy](/imgs/using-google-photos-backup-in-china-005.png)

5. 最后一步就是为 Google Photos Backup 添加规则 **Rules**，让它走 Shadowsocks 代理。选择 **Rules** 选项卡，单击 **Add** 按钮。在弹出的对话框中依次填写名称 **Name** 为 **Google**, 应用程序 **Applications** 为 **\"Google Photos Backup\"**（注意，需要加双引号，因为这里有空格，需要括起来），动作 **Action** 选择 **Proxy SOCKS5 127.0.0.1:1080**，这个就是我们刚才添加的代理通道。接着单击 OK 即可。

    ![Proxifier for Mac - Add Rule](/imgs/using-google-photos-backup-in-china-006.png)

好了，恭喜你可以无障碍的正常使用它了，在使用过程中不要关闭 Proxifier 喔，使用结束后就可以关闭了。总体的规则如下，可以对照。要注意 ShadowsocksX 在 Proxifier 中是走直连，默认 Default 也是走直连。

![Proxifier for Mac - All Rules](/imgs/using-google-photos-backup-in-china-007.png)

大家甚至可以举一反三，在所有科学上网且需要走 SOCKS5 协议的客户端都可以用这个方法。
