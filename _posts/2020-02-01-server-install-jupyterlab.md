---
title: 服务器端JupyterLab计算平台的安装
tags:  Jupyter系列
date:   2020-02-01 01:00:00 +0800
key: serverinstallJupyterLab
---
`服务器部署JupyterLab系列文章：（一）`{:.error}

<!--more-->
>2020-02-25 更新，规范了 `jupyter.conf` 的书写格式。

本文以 `Debian9` 为例。
# Linux [安装Anaconda](https://docs.anaconda.com/anaconda/install/linux/)
~~~bash
#安装依赖
apt install libgl1-mesa-glx libegl1-mesa libxrandr2 libxrandr2 libxss1 libxcursor1 libxcomposite1 libasound2 libxi6 libxtst6
#下载Anaconda
wget https://repo.anaconda.com/archive/Anaconda3-2019.10-Linux-x86_64.sh
#安装Anaconda
bash Anaconda3-2019.10-Linux-x86_64.sh
~~~
# 配置 Jupyter
## 创建 nbserver
~~~bash
#创建一个名为nbserver的配置。
ipython profile create nbserver

#这将创建一个文件夹，其中包含一些原始的配置文件。我们跳转到这个文件夹进行一些配置
cd ~/.ipython/profile_nbserver/

#由于ipython Notebook要求https连接，因此我们需要创建一个ssl证书。
openssl req -x509 -nodes -days 365 -newkey rsa:1024 -keyout mycert.pem -out mycert.pem
~~~
命令执行后根据提示输入信息就好，当然，这个证书并未获得认证，因此用浏览器访问的时候会得到一些错误信息，这个问题我们一会儿说。接下来我们创建一个密文的密码。

~~~bash
#打开 IPython
ipython

In [1]: from notebook.auth import passwd

In [2]: passwd()
Enter password:123456
Verify password:123456
Out[2]: 'sha1:4d6044e56b49:e7e80a36f9c15ab9e6a42cca16cddc3818a11a3f'
~~~
输入密码后会输出此密码对应的 `hash`（格式为 `'type:salt:hashed-password'`），比如 `'sha1:4d6044e56b49:e7e80a36f9c15ab9e6a42cca16cddc3818a11a3f'`。记下此 `hash` 字串，随后会用到。

## 修改 jupyter notebook 配置文件
查看用户目录 `~/.jupyter` 路径下是否存在 `jupyter_notebook_config.py` 文件。若不存在，使用以下指令生成。
~~~bash
#生成配置文件
jupyter notebook --generate-config
~~~

内容修改为：
~~~json
c = get_config()

#所有matplotlib的图像都通过iline的方式显示
c.IPKernelApp.pylab = 'inline'
#这一行指向我们刚刚创建的ssl证书
c.NotebookApp.certfile = u'/root/.ipython/profile_nbserver/mycert.pem'
#给出刚刚创建的密码的哈希值
c.NotebookApp.password = u'sha1:4d6044e56b49:e7e80a36f9c15ab9e6a42cca16cddc3818a11a3f'
c.NotebookApp.ip = '0.0.0.0'
#  给出运行的端口，ipython默认为8888
c.NotebookApp.port = 8888
#  禁止在运行ipython的同时弹出浏览器
c.NotebookApp.open_browser = False
c.NotebookApp.notebook_dir = '/root/JupyterLab/'
c.NotebookApp.allow_root = True
c.NotebookApp.trust_xheaders = True
~~~
## 设置域名
生成 `SSL` 证书
~~~bash
~/.acme.sh/acme.sh --issue -d jupyter.XXXXXX.com --standalone -k ec-256 --force
~/.acme.sh/acme.sh --installcert -d jupyter.XXXXXX.com --fullchainpath /root/.ipython/profile_nbserver/jupyter.crt --keypath /root/.ipython/profile_nbserver/jupyter.key --ecc
~~~
## 配置 Nginx
创建文件`/etc/nginx/conf/conf.d/jupyter.conf`，内容为：
~~~bash
#仅供参考
#Jupyter lab website
server {
    server_name jupyter.XXXXXX.com;
    listen 80;
    location / {
        proxy_pass http://127.0.0.1:8888/;
        proxy_set_header X-Real-IP $remote_addr;
        proxy_set_header Host $host;
        proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
        proxy_http_version 1.1;
        proxy_set_header Upgrade $http_upgrade;
        proxy_set_header Connection "upgrade";
        proxy_redirect off;
        root /var/www/notebook_ssl;
    }
}
server {
        listen 443 ssl;
        ssl_certificate       /root/.ipython/profile_nbserver/jupyter.crt;
        ssl_certificate_key   /root/.ipython/profile_nbserver/jupyter.key;
        ssl_protocols         TLSv1 TLSv1.1 TLSv1.2;
        ssl_ciphers           HIGH:!aNULL:!MD5;
        server_name jupyter.XXXXXX.com;
        location / { proxy_set_header X-Real-IP $remote_addr;
        proxy_set_header Host $http_host;
        proxy_pass https://127.0.0.1:8888; }

        location ~* /(api/kernels/[^/]+/(channels|iopub|shell|stdin)|terminals/websocket)/? {
        proxy_pass            https://127.0.0.1:8888;
        proxy_set_header      Host $host;
        # websocket support
        proxy_http_version    1.1;
        proxy_set_header      Upgrade "websocket";
        proxy_set_header      Connection "Upgrade";
        proxy_read_timeout    86400;
        }
}
~~~

>Reference:
>- [https://gist.github.com/paparaka/294450b727c2aa5455e7125f695e54ed](https://medium.com/@nedjalkov.ivan.j/jupyter-lab-behind-a-nginx-reverse-proxy-the-docker-way-8f8d825a2336)
>- [Github Gist](https://gist.github.com/paparaka/294450b727c2aa5455e7125f695e54ed).

## 添加 JupyterLab 为系统服务
将 `Jupyter Lab` 设定为系统服务并自动启动，创建 `jupyter.service` 并放置于 `/etc/systemctl/system/` 文件夹下，内容为：
~~~bash
[Unit]
Description=Jupyterlab
After=syslog.target network.target

[Service]
User=root
Environment="PATH=/bin:/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/root/anaconda3/bin"
ExecStart=/root/anaconda3/bin/jupyter lab --config=/root/.jupyter/jupyter_notebook_config.py

[Install]
WantedBy=multi-user.target
~~~

运行：`systemctl daemon-reload` . 然后 `systemctl <start|stop|status> jupyter` 来启动服务。
