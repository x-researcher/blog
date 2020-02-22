---
title: 利用Github的Action功能，自动实现Gitbook的部署
tags:  Gitbook Github Action
date:   2020-02-22 17:30:00 +0800
key: gitbookaction
---
Gitbook的结构文件保存在master分支里，然后利用Github Action自动生成gh-pages分支。
<!--more-->
# 0. 为什么要这样做？
[上篇文章](https://blog.xresearcher.com/2020/02/02/gitbook-tutorial.html)里记录了将`gitbook`部署到`GitHub Pages`的过程，但是每次都要经过两个过程，生成`_book`文件夹和将文件拷贝到`gh-pages`分支再推送，比较麻烦，那能否把`gitbook`的结构文件推送到`master`分支，然后自动生成其`gh-pages`分支呢？

当然是可以的！我们需要用到`GitHub`推出了[Action](https://github.com/features/actions)功能，利用此功能可以自动完成静态网页部署到`gh-pages`分支的操作，可谓是一劳永逸啊。
# 1. 添加 Secret
本文用的是[ZanderZhao](https://github.com/ZanderZhao/gitbook-action)开发的[Gitbook Action](https://github.com/marketplace/actions/gitbook-action)文件。

首先为本仓库添加5个`secret`, 路径为 `repo->Setting->Secrets->Add a new secret`, 5个`secret`如下表：

| Name             | Value                                   | 说明                                                         |
| ---------------- | --------------------------------------- | ------------------------------------------------------------ |
| GIT_NAME         | your git name                           | 你的git名称                                                  |
| GIT_EMAIL        | your git email                          | 你的git的email                                               |
| THE_GITHUB_TOKEN | your Personal access tokens             | 可以在`Settings->Developer settings->Personal access tokens->Generate new token`生成，注意权限要给足 |
| THE_GITHUB_REF   | github.com/your_name/your_repo_name.git | 注意**不要**添加`https://`                                   |
| RES_FOR_PAGES    | your branch                             | 注意要提前建好，且在设置里面设置为pages                      |

>表格引用自[官方文档](https://zlogs.net/gitbook-action/books/introduction_cn.html)。

表格中5个`secret`，分别独立添加，而不是将所有的`secret`添加成一个，这个地方错了好多次（我是真的蠢呀🙃），效果如图。
{:.info}

![](/photos/github-add-secret.png)
# 2. 创建 Action 文件
`Action` -> `set up a workflow yourself` (在右上部分) 

文件名保存为 `gitbook_action.yml`，文件保存在 `/.github/workflows/` 下，内容为：
~~~bash
# You must put this in your repo master branch  as repo/.github/workflow/gitbook_action.yml
# You must add the following secrets in your Secrets(repo->Setting->Secrets->Add a new secret)  
# Name:   GIT_NAME              Value:  your git name like ZanderZhao
# Name:   GIT_EMAIL             Value:  your git email like example@exp.com
# Name:   THE_GITHUB_TOKEN      Value:  you can git it from Settings->Developer settings->Personal access tokens->Generate new token
# Name:   THE_GITHUB_REF        Value:  github.com/your_name/your_repo_name.git
# Name:   RES_FOR_PAGES         Value:  put the branch name here like gh-pages,where the build book you want put for
# Pay attention，THE_GITHUB_REF can't add https:// before, 
# And RES_FOR_PAGES must be create and be set as pages before.
# When you make THE_GITHUB_TOKEN ,the push permission is must have，the others, you can add.
# You can visit https://zlogs.net/gitbook-action/ for more information.

name: 'Gitbook Action'

on:
  push:
    branches:    
      - master

jobs:
  build:
    runs-on: ubuntu-latest
    strategy:
      matrix:
        node-version: [8]

    steps:
    - uses: actions/checkout@v1
    - name: Use Node.js ${{ matrix.node-version }}
      uses: actions/setup-node@v1
      with:
        node-version: ${{ matrix.node-version }}
    - name: install_gitbook_plugins
      run: |
        npm install gitbook-cli  -g
        gitbook -v
        gitbook install
    - name: build_gitbook
      run: |
        gitbook build
    - name: push_to_pages
      run: |
        cd ./_book
        git init
        git config --local user.name "${{ secrets.GIT_NAME }}"
        git config --local user.email "${{ secrets.GIT_EMAIL }}"
        git add *
        git commit -m "update"
        git push --force  "https://${{ secrets.THE_GITHUB_TOKEN }}@${{ secrets.THE_GITHUB_REF }}" master:${{ secrets.RES_FOR_PAGES }}
      env:
        CI: true
~~~

# 3. Action的自动运行
根目录下面的`README.md`和`SUMMARY.md`是必须的，如果需要个性化配置可以添加`book.json`，另外也可以添加`CNAME`文件以指定域名。

一切妥当后，一旦位于`master`的文件得到修改，`Action`则会自动运行以生成`gh-pages`分支(`gh-pages`分支需要提前创建)，之后刷新网页即可看到新的修改。