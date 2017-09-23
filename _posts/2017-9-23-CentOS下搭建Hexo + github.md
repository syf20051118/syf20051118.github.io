---
layout:     post
title:      CentOS下搭建Hexo + github
subtitle:   CentOS下搭建Hexo + github组建个人博客发布平台
date:       2017-9-23
author:     Sunie
header-img: img/post-bg-ios9-web.jpg
catalog: true
tags:
    - hexo
    - github
---
> 本篇主要讲如何在虚拟机centos6.5环境下构建一个github平台，用于发布Hexo这个静态页面。

# 1.安装环境：
虚拟机下CentOS6.5，nat模式保证可以上网即可。windows环境也有相关软件，比较难以理解。

# 2.必备插件安装：

```
yum -y install gcc zlib-devel openssl-devel perl cpio expat-devel gettext-devel curl autoconf gcc-c++ openssl-devel
```


# 3.安装Node.js

```
curl -sL https://rpm.nodesource.com/setup | bash -
yum install -y nodejs
```

# 4.安装git

```
yum install git
```

# 5.安装Hexo

```
npm install -g hexo-cli
#如果当前路径是/hexo的话，直接在这个路劲下可以不接folder
hexo init <folder> #folader是目录路径
cd <folder>
npm install
```
## 5.1相关插件

```
npm install hexo-generator-index --save
npm install hexo-generator-archive --save
npm install hexo-generator-category --save
npm install hexo-generator-tag --save
npm install hexo-server --save
npm install hexo-deployer-git --save
npm install hexo-deployer-heroku --save
npm install hexo-deployer-rsync --save
npm install hexo-deployer-openshift --save
npm install hexo-renderer-marked --save
npm install hexo-renderer-stylus --save
npm install hexo-generator-feed --save
npm install hexo-generator-sitemap --save
```
## 5.2修改hexo配置文件
修改Hexo配置文件   
提示：key对应没有值的时候，冒号后面一定要有空格！否则会报错      
例如: timezone:会报错，timezone: 则不会。

```
# Hexo Configuration  
## Docs: https://hexo.io/docs/configuration.html
## Source: https://github.com/hexojs/hexo/

# Site 站点信息设置
title: sunnie #站名
subtitle:  #副标题
description: #站描述
author: sunnie #作者
language: zh-CN #语言
timezone:

# URL 链接设置
## If your site is put in a subdirectory, set url as 'http://yoursite.com/child' and root as '/child/'
url: 
root: /
permalink: :year/:month/:day/:title/
permalink_defaults:

# Directory 文件目录
source_dir: source
public_dir: public
tag_dir: tags
archive_dir: archives
category_dir: categories
code_dir: downloads/code
i18n_dir: :lang
skip_render:

# Writing 文章
new_post_name: :title.md # File name of new posts
default_layout: post
titlecase: false # Transform title into titlecase
external_link: true # Open external links in new tab
filename_case: 0
render_drafts: false
post_asset_folder: false
relative_link: false
future: true
highlight:
  enable: true
  line_number: true
  auto_detect: true
  tab_replace:

# Category & Tag
default_category: uncategorized
category_map:
tag_map:

# Date / Time format 日期
## Hexo uses Moment.js to parse and display date
## You can customize the date format as defined in
## http://momentjs.com/docs/#/displaying/format/
date_format: YYYY-MM-DD
time_format: HH:mm:ss

# Pagination 分页
## Set per_page to 0 to disable pagination
per_page: 20
pagination_dir: page

# Extensions 扩展
## Plugins: https://hexo.io/plugins/
## Themes: https://hexo.io/themes/
theme: landscape

# Deployment 这里设置了Git获
#这里一定要注意不要写错了，否则部署到Github上会出问题
## Docs: https://hexo.io/docs/deployment.html
deploy:
  type: git
  repo: git@github.com:syf20051118/syf20051118.github.io.git
  branch: master
  message: '站点更新:{{now("YYYY-MM-DD HH/mm/ss")}}'
```
# 6.部署到GitHub
## 6.1.配置本机全局git环境（如果已经配置过请忽略）
首先请使用邮箱注册github账号，否则会影响下面操作，记住你注册的邮箱。
另外，请在VPS上设置你的git
email=注册用的email     
user.name=用户名

```
git config --global user.email "you@example.com"
git config --global user.name "Your Name"
```

## 6.2.生成SSH秘钥
先确定你的VPS 有没有生成过ssh的key，
验证


```
less  ~/.ssh/id_rsa.pub
```

如果报错    
执行下面代码


```
# -C后面跟住你在github的用户名邮箱，这样公钥才会被github认可
 ssh-keygen -t rsa -C example@126.com
# 查看 公钥内容 稍后加入Github 账户的 sshkey中
 less ~/.ssh/id_rsa.pub
```

你会看到一堆代码
如果没有报错


```
 # -C后面跟住你在github的用户名邮箱，这样公钥才会被github认可
 ssh-keygen -t rsa -C example@126.com
 # 回车后，输入一个文件夹名字，存储新的SSH 秘钥
 .ssh/github
 # 查看 公钥内容 稍后加入Github 账户的 sshkey中
 less ~/.ssh/id_rsa.pub
```

当然，也有可能遇到这种情况，你可以选择覆盖（确认没有人使用当前的SSH秘钥），或者不覆盖 ）
## 6.3.创建博客工程
创建一个新项目，项目名称为 用户名.github.io     
用其他的也是可以访问的的，但是发布路径不一样，所以url会有所不同。
git@github.com:syf20051118/syf20051118.github.io.git
## 6.4.将ssh秘钥添加到github中
生成后，需要将秘钥放到github上去，登录你的github账号
进入秘钥设置面板:https://github.com/settings/ssh

再执行
```
less ~/.ssh/id_rsa.pub
```
你会看到一堆长密码。复制粘贴进去
## 6.5.配置Hexo，编译与部署
还记得我们在_config.yml里最后一段的配置吗？

Deployment 这里设置了Git获
这里一定要注意不要写错了，否则部署到Github上会出问题

```
## Docs: https://hexo.io/docs/deployment.html
deploy: 
  type: git
  repo: git@github.com:syf20051118/syf20051118.github.io.git
  branch: master
  message: '站点更新:{{now("YYYY-MM-DD HH/mm/ss")}}'
```
这里，repo就是你创建的博客工程

最后一步，编译，上传静态代码

## 6.6.修改主题和配置配置文件

### 6.6.1.安装主题NexT
ps:我们现在在目录hexo下


```
git clone https://github.com/iissnan/hexo-theme-next themes/next
```

并在目录hexo下的_config.yml中


```
# 找到  theme:  修改后面的参数，默认是  landscape
theme: next
```
### 6.6.2.找主题

```
https://hexo.io/themes/
```

## 6.7.编译

```
hexo generate
```

在主机的hexo目录下 执行以下命令将自动更新到Github

```
hexo d
```


# 7.访问:

看，现在通过 昵称.github.io可以访问静博客了
http://syf20051118.github.io

> 最后感谢简书的攻略     
漠然-mrtid      
本文参考了以下站点文章：      
Github 搭建 Hexo 静态博客       
Hexo 主题 next
