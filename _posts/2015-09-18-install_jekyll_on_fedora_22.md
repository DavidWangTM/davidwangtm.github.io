---
layout:     post
title:      "Fedora 22上安装jekyll"
subtitle:   ""
date:       2015-09-18 12:00:00
author:     "Wenzhiquan"
header-img: "img/home-bg-o.jpg"
catalog: true
tags:
    - Fedora
    - Jekyll
---

> “jekyll 是一款简单的博客系统，静态网站生成器。”

### 1. 概述

jekyll是一个简单的免费的Blog生成工具，类似WordPress。但是和WordPress又有很大的不同，原因是jekyll只是一个生成静态网页的工具，不需要数据库支持。但是可以配合第三方服务,例如Disqus。最关键的是jekyll可以免费部署在Github上，而且可以绑定自己的域名。

### 2. 安装最新版ruby

调用命令

```
sudo dnf install ruby
```

安装完成后,输入命令

```
ruby -v
```

会有如下类似的显示:

```
ruby 2.2.3p173 (2015-08-18 revision 51636) [x86_64-linux]
```

说明已经成功安装了2.2.3版本的ruby。

<!--break-->

### 3. 安装jkeyll

安装最新版本的jekyll，命令如下：

```
gem install jekyll
```

安装过程中可能会报如下错误:

```
Fetching: ffi-1.9.10.gem (100%)
Building native extensions.  This could take a while...
ERROR:  Error installing jekyll:
	ERROR: Failed to build gem native extension.

    /usr/bin/ruby -r ./siteconf20150918-17908-j7rbks.rb extconf.rb
mkmf.rb can't find header files for ruby at /usr/share/include/ruby.h

extconf failed, exit code 1

Gem files will remain installed in /home/wenzhiquan/.gem/ruby/gems/ffi-1.9.10 for inspection.
Results logged to /home/wenzhiquan/.gem/ruby/extensions/x86_64-linux/ffi-1.9.10/gem_make.out
```

原因是没有安装gem本地扩展,输入命令`sudo dnf install ruby-devel`安装dev版本的ruby即可解决问题。如果是报gcc相关的错误,只需要`sudo dnf install gcc`进行安装即可。


因为我们将会使用Markdown语言作为标记语言，所以还需要安装kramdown，命令如下：

```
gem install kramdown
```

至此，本机的jekyll运行环境就已经搭建好了。

### 4. 运行例子

安装好之后，就可以自己构建一个博客系统，或者也可以到[jekyll模板网站]( http://jekyllthemes.org/ "jekyll 模板网站") 下载自己喜欢的模板进行修改，然后运行并查看效果，要运行jekyll首先要进入博客的根目录，然后运行命令`jekyll server`，最后会显示启动信息：

```
Server address: http://127.0.0.1:4000/
Server running... press ctrl-c to stop.
```

说明程序已经成功启动，在浏览器中输入localhost:4000，就可以查看自己的博客了。
