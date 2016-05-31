---
layout:     post
title:      "Fedora 20 中文字体美化"
subtitle:   " 如何美化Fedora 20的中文字体"
date:       2014-10-10 12:00:00
author:     "Wenzhiquan"
header-img: "img/post-bg-2015.jpg"
catalog: true
tags:
    - Fedora
---

> “Fedora 20 自带的中文字体确实难以入眼。”


新装了Fedora 20，但是他的中文字体看起来略微有一些奇怪，通过上网查找和自己的实践，总结出了以下美化的步骤：

## 1. 安装字体

安装文泉驿微米黑并开启文泉驿微米黑渲染：

```
sudo yum install wqy-microhei-fonts
```

```
vi /etc/fonts/conf.d/65-0-wqy-microhei.conf
```

将false改成true,然后保存。

```
<?xml version="1.0"?>
<!DOCTYPE fontconfig SYSTEM "fonts.dtd">
<fontconfig>
    <match target="font">
        <test name="family">
            <string>WenQuanYi Micro Hei</string>
        </test>
        <edit name="hinting" mode="assign">
            <bool>false</bool>
        </edit>
    </match>
</fontconfig>
```

## 2.安装字体渲染软件

```
sudo rpm -Uvh http://www.infinality.net/fedora/linux/infinality-repo-1.0-1.noarch.rpm
sudo yum install freetype-infinality fontconfig-infinality
```

## 3.配置渲染

*(1)建立自定义渲染方案：*

```
cd /etc/fonts/infinality/styles.conf.avail
sudo mkdir fedora
sudo cp linux/* fedora
```

*(2)在/etc/fonts/infinality/styles.conf.avail/fedora目录中：*

编辑`20-aliases-default-linux.conf`文件：

```
sudo gedit 20-aliases-default-linux.conf
```

在每一对`<prefer>`和`</prefer>`标签之间添加以下文本：

```
<family>WenQuanYi Micro Hei</family>
```

比如：

```
<alias>
    <family>sans-serif</family>
    <prefer>
        <family>DejaVu Sans</family>
    </prefer>
</alias>
```

就变成了：

```
<alias>
    <family>sans-serif</family>
        <prefer>
            <family>DejaVu Sans</family>
            <family>WenQuanYi Micro Hei</family>
    </prefer>
</alias>
```

总共添加三处，然后保存退出。


*(3) 创建中文渲染配置文件*

创建`62-group-chinese-fonts.conf`

```
sudo gedit 62-group-chinese-fonts.conf
```

添加以下代码：

```
<?xml version='1.0'?>
<!DOCTYPE fontconfig SYSTEM 'fonts.dtd'>
<fontconfig>
   <match target="font">
      <test name="force_autohint">
         <bool>false</bool>
      </test>
      <test name="family">
         <string>WenQuanYi Micro Hei</string>
        </test>
      <edit name="font_type" mode="assign">
         <string>Chinese Font</string>
      </edit>
   </match>

   <match target="font">
      <test name="force_autohint">
         <bool>false</bool>
      </test>
      <test name="family">
         <string>WenQuanYi Micro Hei Light</string>
        </test>
      <edit name="font_type" mode="assign">
         <string>Chinese Font</string>
      </edit>
   </match>

   <match target="font">
      <test name="force_autohint">
         <bool>false</bool>
      </test>
      <test name="family">
         <string>WenQuanYi Micro Hei Mono</string>
        </test>
      <edit name="font_type" mode="assign">
         <string>Chinese Font</string>
      </edit>
   </match>

   <match target="font">
      <test name="force_autohint">
         <bool>false</bool>
      </test>
      <test name="family">
         <string>WenQuanYi Micro Hei Mono Light</string>
        </test>
      <edit name="font_type" mode="assign">
         <string>Chinese Font</string>
      </edit>
   </match>

   <match target="font">
      <test name="force_autohint">
         <bool>false</bool>
      </test>
      <test name="family">
         <string>WenQuanYi Zen Hei</string>
        </test>
      <edit name="font_type" mode="assign">
         <string>Chinese Font</string>
      </edit>
   </match>

   <match target="font">
      <test name="force_autohint">
         <bool>false</bool>
      </test>
      <test name="family">
         <string>WenQuanYi Zei Hei Mono</string>
        </test>
      <edit name="font_type" mode="assign">
         <string>Chinese Font</string>
      </edit>
   </match>
</fontconfig>
```

然后保存退出。

创建`63-group-chinese-fonts-rendering.conf`

```
sudo gedit 63-group-chinese-fonts-rendering.conf
```

添加以下代码：

```
<?xml version='1.0'?>
<!DOCTYPE fontconfig SYSTEM 'fonts.dtd'>
<fontconfig>
   <match target="font">
      <test name="font_type">
         <string>Chinese Font</string>
      </test>
      <edit name="antialias" mode="assign">
         <bool>true</bool>
      </edit>
      <edit name="hintstyle" mode="assign">
         <const>hintslight</const>
      </edit>
      <edit name="autohint" mode="assign">
         <bool>false</bool>
      </edit>
   </match>
</fontconfig>
```

然后保存退出。

## 4.选择配置方案

```
sudo /etc/fonts/infinality/infctl.sh setstyle
```

输入我们创建的渲染方案fedora的序号 2，回车。

重启计算机之后就可以看到比较令人满意的显示效果了。
