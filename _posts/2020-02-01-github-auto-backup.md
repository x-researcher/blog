---
title: 利用Github自动备份数据
tags:  Github SSH Git crontab Jupyter系列
date:   2020-02-01 04:00:00 +0800
key: githubautobackup
---
`服务器部署JupyterLab系列文章：（四）`{:.error}

<!--more-->
可以利用git功能将重要的数据**自动备份**到`Github的Private仓库`。

本文利用此功能实现JupyterLab数据的自动备份。
# 配置SSH密钥
## 生成SSH
~~~bash
cd /root/.ssh/
ssh-keygen -t rsa -b 4096 -C "your_email@example.com"
#This creates a new ssh key, using the provided email as a label.
> Generating public/private rsa key pair.
#When you're prompted to "Enter a file in which to save the key," press Enter. This accepts the default file location.
> Enter a file in which to save the key (/c/Users/you/.ssh/id_rsa):[Press enter]
#At the prompt, type a secure passphrase. For more information, see "Working with SSH key passphrases".
> Enter passphrase (empty for no passphrase): [Type a passphrase]
> Enter same passphrase again: [Type passphrase again]
~~~
`/root/.ssh/` 下就生成了两个文件，`id_rsa`和`id_rsa.pub`

## 添加到Github公钥
将`id_rsa.pub`文件中的内容拷贝到[Github公钥](https://github.com/settings/ssh/new)。

修改`id_rsa`的权限，如不修改会出现无法建立连接的问题。
~~~bash
chmod 600 /root/.ssh/id_rsa
~~~
如果还有连接失败等问题，可尝试修改`/etc/ssh/sshd_config`
~~~bash
nano /etc/ssh/sshd_config
PermitRootLogin prohibit-password to PermitRootLogin yes 
PasswordAuthentication no to PasswordAuthentication yes
~~~
然后重启`ssh`服务:
~~~bash
service ssh restart
~~~

# 建立私人仓库
先[新建一个仓库](https://github.com/new)用来存放备份文件，名称随意，记得下面一定要勾选`Private`，也就是私人仓库。
# 配置本地仓库
## 定位本地仓库
把需要备份的文件夹定为本地仓库，使用命令：
~~~bash
#进入需要备份的文件夹
cd /root/JupyterLab/

#初始化你的github仓库
git init

#关联到远程github仓库
git remote add origin git@github.com:xxxxxx/JupyterLabBackup.git
~~~
## 初次备份
~~~bash
#进入备份的文件夹
cd /root/JupyterLab/

#把目录下所有文件更改状况提交到暂存区，包括增，删，改。
git add -A

#说明随意，这里为Backup JupyterLab files
git commit -m "Backup JupyterLab files"

#开始推送到Github
git push -u origin master
~~~
推送的时候可能会提示The authenticity of host 'github.com' can't be established.信息，直进yes即可。

然后可以看到仓库的备份文件了。

# 设置定时备份
在根目录先新建一个bash脚本：
~~~bash
nano ~/gitback.sh
#!/bin/bash
cd /root/JupyterLab/
git add -A
git commit -m "Auto Backup JupyterLab files"
git push -u origin master
~~~

然后编辑好了后，使用ctrl+x，y保存退出。然后测试下脚本：
~~~bash
bash ~/gitback.sh
~~~
脚本没问题的话，再设置为每天03:00执行一次，并将运行日志输出到根目录的JupyterLab-backup.log文件
~~~bash
echo "00 03 * * * bash ~/gitback.sh > ~/JupyterLab-backup.log 2>&1 &" > bt.cron
crontab bt.cron
rm -rf bt.cron
~~~
最后使用`crontab -l`命令查看添加成功没。成功的话，就基本上算完成了。