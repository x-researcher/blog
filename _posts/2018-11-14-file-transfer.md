---
title: 远程 VPS 与本地电脑之间如何传输文件
tags:  sz rz xftp winscp rsync
date:   2018-11-14 00:00:00 +0800
key: filetransfer
---
介绍常用的传输方式（上传，下载，同步，迁移）。

<!--more-->

---

主要分三大类，基于 `ZMODEM` 协议的文件传输系统，基于 `SFTP`、`FTP`、`SCP` 等协议的文件传输系统，和基于 `Rsync` 程序的同步系统。

## 1.命令行操作
基于 `ZMODEM` 协议，是通过命令行下的指令调出下载和上传窗口，以达到文件传输的目的。
采用 `sz` 与 `lz` 命令

首先安装程序
~~~bash
yum install lrzsz -y
~~~
常用命令
~~~bash
# 下载文件 file1 file2 file3
sz file1 file2 file3
# 上传并覆盖
rz -y
~~~

**缺陷：** `不能上传文件夹`，但是可以先把文件夹打包，再作为文件下载。
{:.error}

## 2.UI 客户端操作
基于 `SFTP`、`FTP`、`SCP` 等协议开发的客户端，通过 `SSH` 协议连接主机后，可以轻松进行下载、上传、编辑、删除、移动等操作，获得如 `Windows` 操作系统般的体验。
### 2.1.WinScp
[WinScp](https://winscp.net/eng/download.php) 完全免费，并且操作简单易懂，可参考 [官方文档](https://winscp.net/eng/docs/lang:chs)，不再赘述。
### 2.2.Xftp
与 `winscp` 类型。可与 `xshell` 搭配使用，极大地提高工作效率。

`Xftp` 属于 `Xmanager Power Suit` 的一部分，属于商业付费软件。

~~[Xmanager power suit 6 最新版注册](http://mannix.top/2018/11/24/Xmanager-power-suit-6-%E6%9C%80%E6%96%B0%E7%89%88%E6%B3%A8%E5%86%8C/)~~

~~[Xmanager-keygen](https://github.com/DoubleLabyrinth/Xmanager-keygen)~~

尽量不使用盗版，现在的主力工具为 `winSCP` + `PuTTy`。
{:.info}

## 3.Rsync 同步系统
上述两部分已能满足大多数情况下的需求，如果你想把 `VPS` 当成网盘，备份与同步自己的文件，或者 `VPS` 之间迁移数据，就需要 `Rsync` 软件了。

目前我并未用到此功能，可参考互联网上的教程学习。

[How To Use Rsync to Sync Local and Remote Directories on a VPS](https://www.digitalocean.com/community/tutorials/how-to-use-rsync-to-sync-local-and-remote-directories-on-a-vps)

[如何使用 Rsync 同步本地和 VPS 远程目录](https://www.howtoing.com/how-to-use-rsync-to-sync-local-and-remote-directories-on-a-vps)

[Linux 下同步工具 inotify+rsync 使用详解](https://segmentfault.com/a/1190000002427568)

