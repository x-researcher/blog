---
title: Linux服务器端安装字体
tags:  Jupyter系列 Linux fonts
date:   2020-02-01 03:00:00 +0800
key: serverlinuxfonts
---
`服务器部署JupyterLab系列文章：（三）`{:.error}

<!--more-->
## 创建字体文件夹

* 在`/usr/share/fonts`下新建字体文件夹，如`msfonts`


* 将windows系统盘下fonts中的times.ttf、MSYHBD.TTF 、MSYH.TTF 、simhei.ttf 、simsun.ttc 拷贝到`/usr/share/fonts/msfonts/`

## 修改字体目录的权限：
~~~bash
chmod -R 755 /usr/share/fonts/msfonts
~~~

## 修改`/etc/fonts/fonts.conf`

在`<dir>/usr/share/fonts</dir>`后增加`<dir>/usr/share/fonts/msfonts</dir>`，如下
~~~xml
Xml代码 
<!-- Font directory list -->  
    <dir>/usr/share/fonts</dir>  
    <dir>/usr/share/fonts/msfonts</dir>  
    <dir>/usr/share/X11/fonts/Type1</dir> <dir>/usr/share/X11/fonts/OTF</dir>  
    <dir>~/.fonts</dir>  
~~~
执行`fc-cache -f` 使得新加的字体立即生效。