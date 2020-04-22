---
title: 利用 Github 的 Action 功能，自动实现 Gitbook 的部署
tags:  Gitbook Github Action
date:   2020-02-22 17:30:00 +0800
key: gitbookaction
---
Gitbook 的结构文件保存在 master 分支里，然后利用 Github Action 自动生成 gh-pages 分支。
<!--more-->
# 0. 为什么要这样做？
[上篇文章](https://blog.xresearcher.com/2020/02/02/gitbook-tutorial.html) 里记录了将 `gitbook` 部署到 `GitHub Pages` 的过程，但是每次都要经过两个过程，生成 `_book` 文件夹和将文件拷贝到 `gh-pages` 分支再推送，比较麻烦，那能否把 `gitbook` 的结构文件推送到 `master` 分支，然后自动生成其 `gh-pages` 分支呢？

当然是可以的！我们需要用到 `GitHub` 推出了 [Action](https://github.com/features/actions) 功能，利用此功能可以自动完成静态网页部署到 `gh-pages` 分支的操作，可谓是一劳永逸啊。
# 1. 操作教程
笔者使用的是 [ZanderZhao](https://github.com/ZanderZhao/gitbook-action) 开发的 [Gitbook Action](https://github.com/marketplace/actions/gitbook-action) 文件。

~~因原作者更新，旧教程已无参考价值，故将内容删除，具体的使用细节，请访问以上官方链接。~~