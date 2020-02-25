---
title: Gitbook 安装及部署指南
tags:  Gitbook Github bat 脚本
date:   2020-02-02 20:00:00 +0800
key: gitbooktutorials
---
`Gitbook` 在系统整理知识体系时，有很大的便利。

<!--more-->

本文探讨的是 `Windows10` 操作系统下 `Gitbook` 的使用方法。
# 安装 nodejs
前往 [官网](https://nodejs.org/en/) 下载安装，npm 已集成在 node 里，结束后查看是否安装成功：
~~~shell
node -v
npm -v
~~~
# 安装 Gitbook
cmd 控制台输入如下命令：
~~~bash
npm install gitbook-cli -g

#查看安装的版本
gitbook -V
~~~
如果没有安装 `git`，也要补上：
下载 [程序](https://git-scm.com/download/win)，依步骤安装。
# 安装 Gitbook editor windows 客户版
目前，官网的下载链接已经失效，推测官方已经放弃该客户端，此软件对新手比较友好，但 `不推荐作为主力工具`。

安装包可自行搜索 `百度网盘` 的分享，名字为 `GitBook.Editor.Setup.exe`，此处暂不提供下载链接。
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
> warn: no summary file in this book
> info: create README.md
> info: create SUMMARY.md
> info: initialization is finished

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
通过修改根目录下的 `book.json` 文件来实现，如果没有则新建一个，内容为：

~~~json
{
    "title": "我的一本书",
    "author" : "你的名字",
    "description" : "描述性文字",
    "direction" : "ltr",
    "language" : "zh-hans",
    "links" : {
        "sidebar" : {
            "Home" : "https://xxxxxxxx.com"
        }
    },
    "structure": {
        "readme": "README.md",
        "summary": "SUMMARY.md"
    },
    "plugins": [
        "-lunr",
        "-search",
        "search-plus",
        "anchors",
        "anchor-navigation-ex",
        "splitter",
        "advanced-emoji",
        "katex",
        "sitemap-general",
        "todo",
        "copy-code-button",
        "alerts",
        "include-csv",
        "klipse",
        "disqus",
        "tbfed-pagefooter"
    ],
    "pluginsConfig": {
        "theme-default":{
        	"showLevel": true
        },
        "anchor-navigation-ex": {
        	"showLevel":false,
            "isShowTocTitleIcon": true,
            "associatedWithSummary":false
        },
        "sitemap-general": {
            "prefix": "https://xxxxxxxxxxxx.com"
        },
        "disqus":{
        	"shortName": "YOUR-DISQUS-SHORTNAME"
        },
        "tbfed-pagefooter":{
        	"copyright":"Copyright &copy XXXXXXX.com 2020",
        	"modify_label":"该文件修订时间： ",
        	"modify_format":"YYYY-MM-DD HH:mm:ss"
        }
    },
    "styles": {
        "website": "styles/website.css",
        "ebook": "styles/ebook.css",
        "pdf": "styles/pdf.css",
        "mobi": "styles/mobi.css",
        "epub": "styles/epub.css"
    }
}
~~~

配置文件一看就明白，只简单说两点：
- "links" ，在 gitbook 目录上方添加一些其他的链接；
- "plugins" 和 "pluginsconfig"，添加插件和插件的配置文件。

至于插件如何配置以及使用规范，可 `Google` 搜索 `gitbook-plugins-$NAME`， 例如 `todo` 的使用规范见如下 [网站](https://www.npmjs.com/package/gitbook-plugin-todo)。

配置文件修改后，在 `gitbook` 的根目录下，运行指令 `gitbook install` 来自动安装插件。
# 编辑 Gitbook
## 预览 Gitbook
~~~shell
gitbook serve
~~~
## 编译 Gitbook
~~~shell
gitbook build
~~~

编译完成后，在根目录下生成静态网页文件夹 `_book` 。
# 推送到 Github Pages
## 静态网页拷贝到仓库
首先，将 `Github 仓库` 切换到 `gh-pages` 分支
~~~bash
git checkout gh-pages
~~~
然后再将 `_book` 内容拷贝到此目录：
~~~bash
xcopy D:\YOUR-GITBOOK-PATH\_book\*.* D:\YOUR-GITHUB-PATH\ /E /W /Y
# /E 复制目录和子目录，包括空的目录和子目录；
# /W 复制前提醒您按某个键；
# /Y 不用提醒，直接覆盖现有文件。
~~~
## 推送到 Github 仓库
之前用的是 `Github客户端` ，其实习惯之后发现，还是命令行方便😁：
~~~bash
#在 Github 仓库的目录下运行
git add .
git commit -m "更新说明，随便写"
git push origin gh-pages
~~~
成功后即可进入相应的 Github Pages 页面查看，大功告成。
# 批处理指令总结
`gitbook` 和 `github` 使用习惯了之后，会越来越喜欢命令行，毕竟可以使用脚本，现将常用脚本总结如下：
## 生成静态网页
文件名为 `编译gitbook.bat`
~~~batch
cd /d D:\YOUR-GITBOOK-PATH\
gitbook build
~~~
## 自动上传到 Github 仓库
文件名为 `AutoSubmitToGithub.bat`
~~~batch
rem 切换到 gh-pages 分支
cd /d D:\YOUR-Github-PATH\
git checkout gh-pages

rem 拷贝_book 文件到 gh-pages
xcopy D:\YOUR-GITBOOK-PATH\_book\*.* D:\YOUR-GITHUB-PATH\ /E /W /Y

rem 提交到 GitHub 仓库
git add .
git commit -m  "Gitbook update"
git push origin gh-pages
~~~

# 利用 Github 的 Action 功能完成自动部署
> 2020 年 02 月 22 日修改

学习的过程中，发现可以利用 `Action` 功能完成 `Gitbook` 的静态网页自动部署到 `gh-pages` 分支的操作，十分方便，实现方法见 [下一篇博文](https://blog.xresearcher.com/2020/02/22/gitbook-action.html)。
