---
title: 教你使用Travis CI自动化部署Hexo
tags: [travis ci,hexo]
categories: [collect]
date: 2016-08-28 16:29:48
---

原文参考:[手把手教你使用Travis CI自动部署你的Hexo博客到Github上](http://blog.csdn.net/woblog/article/details/51319364),[Travis CI自动化部署Hexo](https://segmentfault.com/a/1190000004714256#articleHeader3)

这里记下,做个收藏,有版权问题.请及时联系.

<!-- more -->

### 什么是Travis CI

Travis CI 是目前新兴的开源持续集成构建项目，它与jenkins，GO的很明显的特别在于采用yaml格式，同时他是在在线的服务，不像jenkins需要你本地打架服务器，简洁清新独树一帜。目前大多数的github项目都已经移入到Travis CI的构建队列中，据说Travis CI每天运行超过4000次完整构建。对于做开源项目或者github的使用者，如果你的项目还没有加入Travis CI构建队列，那么我真的想对你说out了。

### 开通Travis CI

利用 GitHub账号登录[Travis CI](https://travis-ci.org)

### 项目开启Travis CI

这里引用了[Travis CI自动化部署Hexo](https://segmentfault.com/a/1190000004714256#articleHeader3)的图片
![项目开启Travis CI](https://segmentfault.com/img/bVtWyl)

在项目的设置中开启Build only if .travis.yml is present这一项.
![](https://segmentfault.com/img/bVtWyq)

### Github中生成Access Token

>这个用于操作repo,否则没有权限.

![](https://segmentfault.com/img/bVtWys)
![](https://segmentfault.com/img/bVtWyv)

### 安装Travis

>注意:需要安装Ruby,并且需要安装rubygems插件

```shell
gem isntall travis
```

### 创建配置文件

在项目根目录创建.travis.yml文件

```shell
touch .travis.yml
```

### 编辑配置文件

```shell
language: node_js
branches:
  only:
  - master #源码分支名称
before_install:
- npm install -g hexo
- npm install -g hexo-cli
before_script:
- git config --global user.name 'yourname'
- git config --global user.email 'youremail'
- sed -i'' "s~git@github.com:<yourname>/<projectname>.git~https://${REPO_TOKEN}:x-oauth-basic@github.com/<yourname>/<projectname>.git~" _config.yml
install:
- npm install
script:
- hexo clean
- hexo generate
after_success:
- hexo deploy
```

### 配置Travis

1.登录travis
```shell
travis login --auto
```

2.添加变量信息
在项目根目录下执行:
```shell
travis encrypt 'REPO_TOKEN=<TOKEN>' --add
```
之后会在.travis.yml文件中添加下面的信息
```shell
env:
  global:
    secure: fxBE17yzFhC2+FjwVLYbgIhggyfliv3dFCDozTJD7U3n...
```

>这里的REPO_TOKEN是变量名,在后面的配置文件中会用到.TOKEN是上面github生成的Token.

### 测试效果
Push本地的代码至远程仓库，然后,在[Travis CI](https://travis-ci.org)看项目自动化执行.
