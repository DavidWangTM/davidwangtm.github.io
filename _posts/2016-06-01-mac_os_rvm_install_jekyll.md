---
layout:     post
title:      "Mac OS安装jekyll第二篇"
subtitle:   ""
date:       2015-06-01 09:00:00
author:     "DavidWang"
header-img: "img/home-bg-o.jpg"
catalog: true
tags:
    - MacOS
    - Jekyll
---

> “六一快乐,由于很多同学安装通过第一篇jekyll安装不成功，因此本文将使用RVM更新系统Ruby版本安装jekyll”


### 步骤1 － 安装 RVM
RVM 是干什么的这里就不解释了，后面你将会慢慢搞明白。

```
$ gpg --keyserver hkp://keys.gnupg.net --recv-keys 409B6B1796C275462A1703113804BB82D39DC0E3
$ curl -sSL https://get.rvm.io | bash -s stable
# 如果上面的连接失败，可以尝试: 
$ curl -L https://raw.githubusercontent.com/wayneeseguin/rvm/master/binscripts/rvm-installer | bash -s stable
```
期间可能会问你 sudo 管理员密码，以及自动通过 Homebrew 安装依赖包，等待一段时间后就可以成功安装好 RVM。

然后，载入 RVM 环境（新开 Termal 就不用这么做了，会自动重新载入的）

```
$ source ~/.rvm/scripts/rvm
```
修改 RVM 下载 Ruby 的源，到 Ruby China 的镜像:

```
echo "ruby_url=https://cache.ruby-china.org/pub/ruby" > ~/.rvm/user/db
```

检查一下是否安装正确

```
$ rvm -v
rvm 1.22.17 (stable) by Wayne E. Seguin <wayneeseguin@gmail.com>, Michal Papis <mpapis@gmail.com> [https://rvm.io/]
```

### 步骤2 － 用 RVM 安装 Ruby 环境

```
$ rvm requirements
$ rvm install 2.3.1

```

同样继续等待漫长的下载，编译过程，完成以后，Ruby, Ruby Gems 就安装好了。

### 步骤3 － 设置 Ruby 版本

RVM 装好以后，需要执行下面的命令将指定版本的 Ruby 设置为系统默认版本

```
rvm use 2.3.1 --default

```

同样，也可以用其他版本号，前提是你有用 rvm install 安装过那个版本

这个时候你可以测试是否正确

```
$ ruby -v
ruby 2.3.0 ...

$ gem -v
2.1.6

$ gem sources --add https://gems.ruby-china.org/ --remove https://rubygems.org/
//或者
$ gem sources --add https://ruby.taobao.org/ --remove https://rubygems.org/


```

### 步骤4 － 安装jkeyll

安装最新版本的jekyll，命令如下：

```
gem install jekyll

```

因为我们将会使用Markdown语言作为标记语言，所以还需要安装kramdown以及jekyll-paginate ，命令如下：

```
gem install kramdown
gem install jekyll-paginate

```

至此，本机的jekyll运行环境就已经搭建好了

### 步骤5 － 运行例子

安装好之后，就可以自己构建一个博客系统，或者也可以到[jekyll模板网站]( http://jekyllthemes.org/ "jekyll 模板网站") 下载自己喜欢的模板进行修改，然后运行并查看效果，要运行jekyll首先要进入博客的根目录，然后运行命令`jekyll server`，最后会显示启动信息：

```
Server address: http://127.0.0.1:4000/
Server running... press ctrl-c to stop.
```

说明程序已经成功启动，在浏览器中输入localhost:4000，就可以查看自己的博客了。

