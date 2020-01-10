---
title: 在VPS上搭建Jekyll静态博客
tags: Jekyll
date:   2018-11-13 18:00:00 +0800
key: 	vpsinstalljekyll
---
~~VPS仅用于开代理有些浪费，开个网站充分利用一下。~~

*（现已迁移到[GitHub Pages](https://pages.github.com/),教程暂留存，以备后用。）*

<!--more-->

---


## 安装RVM
本例使用 rvm 进行 ruby 的安装，可以快捷的切换 ruby 环境。具体可以去 rvm.io 查看。
~~~bash
gpg --keyserver hkp://keys.gnupg.net --recv-keys 409B6B1796C275462A1703113804BB82D39DC0E3 7D2BAF1CF37B13E2069D6956105BD0E739499BDB
\curl -sSL https://get.rvm.io | bash -s stable
# 之后可能需要重启
reboot
~~~
检查一下
~~~bash
rvm -v
~~~
最后安装rvm依赖包：
~~~bash
rvm requirements
~~~

## 安装Ruby
到[Ruby官网](https://www.ruby-lang.org/en/)查看最新版本号，目前的稳定版本是2.5.3
~~~bash
rvm install 2.5.3
~~~
安装完成之后，设置默认 ruby 的版本
~~~bash
rvm 2.5.3 --default
~~~
检查一下ruby和gem的版本
~~~bash
ruby -v
gem -v
~~~

## 安装Jekyll
查看源列表
~~~bash
gem sources -l
~~~
我的vps在国外，就不切换源了，如果在国内可以替换为国内Ruby China的镜像
~~~bash
gem source -a https://gems.ruby-china.org --remove https://rubygems.org/
~~~
安装jekyll, 比较好的方式是同时安装`bundler`，可以管理依赖
~~~bash
gem install jekyll bundler
~~~
安装完后，再确认一下版本
~~~bash
jekyll -v
bundle -v
~~~
新建博客，可参考[官方指南](https://jekyllrb.com/)
~~~bash
jekyll new myblog
~~~
## 安装主题
对博客进行美化，可以安装主题，比如本站采用的[jekyll-TeXt-theme](https://github.com/kitian616/jekyll-TeXt-theme)
~~~bash
git clone https://github.com/kitian616/jekyll-TeXt-theme.git
~~~
把所有的主题文件拷贝到`myblog`文件夹下
~~~bash
cp -r jekyll-TeXt-theme/* myblog/
cd myblog
bundle install
~~~

## 文章发布
把md格式的文章放在`_posts/`文件夹下，然后生成静态网页
~~~bash
jekyll build
~~~
开启`comments`功能后，发现上述指令仍无法使用评论功能，这时需要使用下面命令
~~~bash
JEKYLL_ENV=developments jekyll build
~~~
## Nginx安装与配置
### Nginx安装
我的服务器在[V2ray搭建](https://xresearcher.com/2018/11/12/v2ray.html)时已经安装了Nginx，不需要再安装，网上的安装教程挺多，就不赘述，可以参考文章[^54321], [^54322]

[^54321]: [用 Jekyll/Nginx/Let'sEncrypt 搭建一个博客站点](https://tomisacat.xyz/tech/2017/02/27/Deploy-a-blog-site-with-Jekyll-and-Nginx.html)

[^54322]: [在CentOS 6上使用Jekyll+Nginx搭建博客](http://kevinlt.top/2018/07/01/build-blog/)

### Nginx配置
修改Nginx配置文件里的`root`目录，每个人的配置文件所处路径不同，请填写正确，下面是我的文件位置
~~~bash
vi /etc/nginx/conf.d/v2ray.conf
# root    /home/wwwroot/myblog/_site
~~~
然后重新载入Nginx
~~~bash
nginx -s reload
# 如出现错误，reboot重启机器
~~~
这时配置结束，就可以通过域名访问博客了。

### 待填的坑
- `git` 远程同步vps上Jekyll
- 如何自动生成`_site/`