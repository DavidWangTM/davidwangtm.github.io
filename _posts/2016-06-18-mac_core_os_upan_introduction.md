---
layout:     post
title:      "制作Mac OS X EL Capotan U盘引导安装"
subtitle:   "U盘引导安装"
date:       2016-06-18 09:30:00
author:     "DavidWang"
header-img: "img/post-bg-2015.jpg"
catalog: true
tags:
    - Mac
---  


## 制作 Mac OS X El Capitan 的U盘引导安装


### 一、准备工作：

- 准备一个 8GB 或者 8G 以上容量的 U 盘。

```
确保里面的数据已经妥善备份好（该过程会抹掉 U 盘全部数据）
最好使用3.0的U盘、速度快 (本人比较苦逼，没有U盘，直接使用的硬盘~ 都是一样的啦)
```

- 打开 `App store` 下载 `OS X El Capitan` 系统

```
下载 OS X 系统 这个就没啥可说的了，直接看图吧..
```

### 二、下载OS系统

- 搜索系统

![IMG](/img/in-post/ios_introduction/u_1.jpeg)

- 点击下载

![IMG](/img/in-post/ios_introduction/u_2.jpeg)

- 点击继续

![IMG](/img/in-post/ios_introduction/u_3.png)

- 当你下载完成以后, 打开你的 launchpad

![IMG](/img/in-post/ios_introduction/u_4.jpeg)

- 找到刚才下载的这个 OS X El Capitan

![IMG](/img/in-post/ios_introduction/u_5.png)

### 三、格式化优盘

- 插入你的 U 盘，然后在「应用程序」->「实用工具」里面找到并打开「磁盘工具」

![IMG](/img/in-post/ios_introduction/u_6.png)

- 在左方列表中找到 外置 -> U盘的名称并点击

![IMG](/img/in-post/ios_introduction/u_7.png)

- 右边顶部选择 抹掉 并且在 名称 输入 DavidWang
由于后面的命令中会用到此名称，如果你要修改成其他(英文)，
请务必对应修改后面的命令, (如果不清楚、请按照我的步骤来，包你不会碰壁！）

![IMG](/img/in-post/ios_introduction/u_8.png)

- 开始格式化 (抹掉U盘)

### 四、输入终端命令开始制作启动盘

- 请再次确保名为 “安装 OS X El Capitan” 的文件是保存在 应用程序的目录中

- 在应用程序->实用工具里面找到终端``并打开, 也可以直接通过Spotlight```搜索终端打开

- 复制下面的命令，并粘贴到终端里，按回车运行! (指令有点长！请复制完全哦~)

```
sudo /Applications/Install\ OS\ X\ El\ Capitan.app/Contents/Resources/createinstallmedia --volume 

/Volumes/DavidWang --applicationpath /Applications/Install\ OS\ X\ El\ Capitan.app --nointeraction

```

命令说明：Install\ OS\ X\ El\ Capitan.app 这个是正式版的“安装OS X El Capitan
Mac OS X EI Capitan 这个是U盘的名字 终端上的如果有空格需要加上 \(空格)
在终端上U盘的名字是这样的 Volumes/Mac\ OS\ X\ EI\ Capitan

- 接回车之后，系统会提示你输入电脑的管理员密码 (如果没有设置密码直接再按一次回车)

![IMG](/img/in-post/ios_introduction/u_9.png)

- 接下来就是等待系统开始制作启动盘了
这时，命令执行中你会`陆续``陆续``陆续看到类似以下的信息:

```
Erasing Disk: 0%... 10%... 20%... 30%...100%...
Copying installer files to disk...
Copy complete.
Making disk bootable...
Copying boot files...
Copy complete.
Done.
```

当你看到最后有Copy complete 和Done字样出现就是表示启动盘已经制作完成了!!!

![IMG](/img/in-post/ios_introduction/u_10.png)

- 6.当你制作完成的OS X El Capitan启动盘之后,优盘就会显示
Install OS X El Capitan的盘符那么就表示启动盘是正常的了。


### 五、U盘启动安装 OS X El Capitan 的方法

- 1.先在目标电脑上插上 U盘，然后重启你的Mac
然后一直按住键盘option 或者是(alt) 按键不放，
直到屏幕显示多出一个 USB 启动盘的选项，如下图所示

![IMG](/img/in-post/ios_introduction/u_11.png)

- 2.这时，你可以直接覆盖安装系统(升级)，也可以在磁盘工具里面格式化抹掉整个硬盘，或者重新分区等实现全新的干净的安装。
- 3.选择磁盘工具.....

![IMG](/img/in-post/ios_introduction/u_12.png)

- 选择电脑内置磁盘....

![IMG](/img/in-post/ios_introduction/u_13.png)

- 抹掉....(磁盘名称 Macintosh HD)

![IMG](/img/in-post/ios_introduction/u_14.png)

- 安装.....

![IMG](/img/in-post/ios_introduction/u_15.png)

- 继续....

![IMG](/img/in-post/ios_introduction/u_16.png)

- 继续....

![IMG](/img/in-post/ios_introduction/u_17.png)

- 继续....

![IMG](/img/in-post/ios_introduction/u_18.png)

- 选择电脑内置磁盘进行安装.....

![IMG](/img/in-post/ios_introduction/u_19.png)
