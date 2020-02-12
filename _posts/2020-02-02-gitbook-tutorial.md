---
title: Gitbook安装及部署指南
tags:  Gitbook Github bat脚本
date:   2020-02-02 20:00:00 +0800
key: gitbooktutorials
---
`Gitbook`在系统整理知识体系时，有很大的便利。

<!--more-->

本文探讨的是`Windows10` 操作系统下 `Gitbook` 的使用方法。
# 安装 nodejs
结束后，查看是否安装成功：
~~~shell
node -v
npm -v
~~~
# 安装 Gitbook
cmd控制台输入如下命令：
~~~bash
npm install gitbook-cli -g

#查看安装的版本
gitbook -V
~~~
如果没有安装`git`，也要补上：
下载[程序](https://git-scm.com/download/win)，依步骤安装。
# 安装 Gitbook editor windows 客户版
目前，官网的下载链接已经失效，推测官方已经放弃该客户端，但此软件挺对新手熟悉和折腾`Gitbook`相当方便，安装包可自行搜索`百度网盘`的分享，名字为`GitBook.Editor.Setup.exe`，此处暂不提供下载链接。
# 生成新的 Gitbook 文件
## 使用 window 客户端
软件没必要登录，直接新建即可，捣腾两下就会了，此处不详细介绍😝。
## 利用 gitbook 命令行
~~~bash
#新建文件夹
mkdir test\
cd .\test\

#初始化
gitbook init
>warn: no summary file in this book
>info: create README.md
>info: create SUMMARY.md
>info: initialization is finished

#文件夹下生成了两个文件
dir
>.\test 的目录
2020/02/02  23:53    <DIR>          .
2020/02/02  23:53    <DIR>          ..
2020/02/02  23:50                16 README.md
2020/02/02  23:50                40 SUMMARY.md
~~~
此时，一个空白的 `Gitbook` 已经生成，使用 `gitbook serve` 命令启动服务，浏览器中打开 `http://localhost:4000/` 进行本地预览。
# 配置 Gitbook
**待补充。**
# 推送到 Github Pages
## 生成 _book 目录
在 `test` 目录下运行 `gitbook build` 指令即可。

但为了方便，我在桌面新建一个批处理指令，这样只需双击就能完成系列操作，特别方便。

新建文件 `gitbook build.bat`，内容如下：
~~~bash
title Gitbook Build
cd /d D:\yourpath\test\
gitbook build
~~~
目录下新生成一个文件夹 `_book` 。
## 把 _book 内容 拷贝到 Github 仓库 的目录
首先，将 `Github 仓库` 切换到 `gh-pages` 分支
~~~bash
git checkout gh-pages
~~~
然后再将 `_book` 内容拷贝到此目录：
~~~bash
xcopy .\your-gitbook-path\test\_book\*.* .\your-github-path\ /E /W /Y
# /E 复制目录和子目录，包括空的目录和子目录；
# /W 复制前提醒您按某个键；
# /Y 不用提醒，直接覆盖现有文件。
~~~
## 推送到 Github 仓库
之前用的是 `Github客户端` ，其实熟练了，还是命令行方便(主要是可以一键批处理😁)：
~~~bash
#在Github仓库的目录下运行
git add .
git commit -m "更新说明，随便写"
git push origin gh-pages
~~~
成功后即可进入相应的 Github Pages 页面查看，大功告成。

#### **批处理指令，总结**