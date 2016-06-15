---
layout:     post
title:      "IOS 第三方库管理工具"
subtitle:   "CocoaPods安装和使用教程"
date:       2016-06-15 07:30:00
author:     "DavidWang"
header-img: "img/home-bg-art.jpg"
catalog: true
tags:
    - ios
---  

### CocoaPods是什么？

当你开发iOS应用时，会经常使用到很多第三方开源类库，比如`JSONKit，AFNetWorking`等等。可能某个类库又用到其他类库，所以要使用它，必须得另外下载其他类库，而其他类库又用到其他类库，“子子孙孙无穷尽也”，这也许是比较特殊的情况。总之意思就是，手动一个个去下载所需类库十分麻烦。另外一种常见情况是，你项目中用到的类库有更新，你必须得重新下载新版本，重新加入到项目中，十分麻烦。如果能有什么工具能解决这些恼人的问题，那将“善莫大焉”。所以，你需要CocoaPods。

CocoaPods应该是iOS最常用最有名的类库管理工具了，上述两个烦人的问题，通过cocoaPods，只需要一行命令就可以完全解决，当然前提是你必须正确设置它。重要的是，绝大部分有名的开源类库，都支持CocoaPods。所以，作为iOS程序员的我们，掌握CocoaPods的使用是必不可少的基本技能了。

### 如何下载和安装CocoaPods？

在安装CocoaPods之前，首先要在本地安装好Ruby环境。至于如何在Mac中安装好Ruby环境，请google一下，本文不再涉及。

假如你在本地已经安装好Ruby环境，那么下载和安装CocoaPods将十分简单，只需要一行命令。在Terminator（也就是终端）中输入以下命令.

```
sudo gem install cocoapods
```

如果你在天朝，在终端中敲入这个命令之后，会发现半天没有任何反应。原因无他，因为那堵墙阻挡了cocoapods.org。

```
gem sources --remove https://rubygems.org/
//等有反应之后再敲入以下命令
$ gem sources -a https://ruby.taobao.org/
```

为了验证你的Ruby镜像是并且仅是taobao，可以用以下命令查看：

```
gem sources -l
```
只有在终端中出现下面文字才表明你上面的命令是成功的：

```
*** CURRENT SOURCES ***

https://ruby.taobao.org/
```
这时候，你再次在终端中运行：

```
$ sudo gem install cocoapods
```
等上十几秒钟，CocoaPods就可以在你本地下载并且安装好了，不再需要其他设置。

### 如何使用CocoaPods？

好了，安装好CocoPods之后，接下来就是使用它。所幸，使用CocoPods和安装它一样简单，也是通过一两行命令就可以搞定。

#### 场景1：利用CocoaPods，在项目中导入AFNetworking类库

AFNetworking类库在GitHub地址是：[https://github.com/AFNetworking/AFNetworking](https://github.com/AFNetworking/AFNetworking)

为了确定AFNetworking是否支持CocoaPods，可以用CocoaPods的搜索功能验证一下。在终端中输入：

```
$ pod search AFNetworking
```
过几秒钟之后，你会在终端中看到关于AFNetworking类库的一些信息。

这说明，AFNetworking是支持CocoaPods，所以我们可以利用CocoaPods将AFNetworking导入你的项目中。

你看到这里也许会问，CocoaPods为什么能下载AFNetworking呢，而不是下载其他类库呢？这个问题的答案是，有个文件来控制CocoaPods该下载什么。这个文件就叫做“Podfile”（注意，一定得是这个文件名，而且没有后缀）。你创建一个Podfile文件，然后在里面添加你需要下载的类库，也就是告诉CocoaPods，“某某和某某和某某某，快到碗里来！”。每个项目只需要一个Podfile文件。

好吧，废话少说，我们先创建这个神奇的PodFile。在终端中进入（cd命令）你项目所在目录，然后在当前目录下，利用vim创建Podfile，运行：

```
$ vim Podfile
```
然后在Podfile文件中输入以下文字：

```
platform :ios, '7.0'
pod "AFNetworking", "~> 2.0"
```
然后保存退出。vim环境下，保存退出命令是：`:wq`.

这时候，你会发现你的项目目录中，出现一个名字为Podfile的文件，而且文件内容就是你刚刚输入的内容。注意，`Podfile文件应该和你的工程文件.xcodeproj`在同一个目录下。

这时候，你就可以利用CocoPods下载`AFNetworking`类库了。还是在终端中的当前项目目录下，运行以下命令：

```
$ pod install
```
因为是在你的项目中导入AFNetworking，这就是为什么这个命令需要你进入你的项目所在目录中运行。

运行上述命令之后,终端出现以下信息：

```
Analyzing dependencies
        Downloading dependencies
        Installing AFNetworking (2.0.2)
        Generating Pods project
        Integrating client project

        [!] From now on use `CocoaPodsDemo.xcworkspace`.
```

注意最后一句话，意思是：以后打开项目就用 CocoaPodsDemo.xcworkspace 打开，而不是之前的.xcodeproj文件。

如果需要更新使用 

```
$ pod update
```
上述都只是CocoaPods的最基本用法。要继续研究CocoaPods其他高级用法，请点击这里[CocoaPods Wiki](https://github.com/CocoaPods/CocoaPods/wiki) 。



