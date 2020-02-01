---
title: R语言的安装以及添加到Jupyter
tags:  Jupyter R 
date:   2020-02-01 02:00:00 +0800
key: installrandaddtojupyter
---
服务器部署JupyterLab系列文章：（二）

<!--more-->

# R语言安装
## 安装依赖项
~~~bash
apt install dirmngr --install-recommends
#向Debian添加PPA引用
apt install software-properties-common
#为了确保我们对安全协议有HTTPS支持，我们将安装以下工具：
sudo apt install apt-transport-https
~~~
## 安装R
~~~bash
#先添加相关的GPG密钥。
apt-key adv --keyserver keys.gnupg.net --recv-key 'E19F5F87128899B192B1A2C2AD5F960A256A04AF'
#一旦我们拥有了可信密钥，我们就可以添加存储库。请注意，如果您不使用Debian 9（Stretch），您可以查看支持的R Project Debian分支，以每个版本命名。
add-apt-repository 'deb https://cloud.r-project.org/bin/linux/debian stretch-cran35/'
#更新
apt update
#安装R
apt install r-base r-base-dev
~~~

# Jupyter Notebook 中添加R内核
~~~R
R
>install.packages(c('repr', 'IRdisplay', 'evaluate', 'crayon', 'pbdZMQ', 'devtools', 'uuid', 'digest'))

#如时间充足，也可顺便把一下常用库安装上，也可今后再安装
>install.packages(c('plotly', 'ape', 'ggplot2', 'cluster', 'devtools', 'datasets.load', 'dplyr', 'ellipse', 'ggfortify', 'pheatmap', 'vegan', 'stringi', 'ade4', 'FactoClass', 'factoextra', 'gplots', 'showtext', 'Cairo'))

#等运行结束，输入
devtools::install_github('IRkernel/IRkernel')
#等运行结束，输入
IRkernel::installspec(user = FALSE)
~~~
若出现**devtools**无法安装的问题，可用下列指令解决：
~~~bash
apt install build-essential libcurl4-gnutls-dev libxml2-dev libssl-dev
~~~