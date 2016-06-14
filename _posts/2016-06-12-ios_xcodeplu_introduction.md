---
layout:     post
title:      "IOS 快速开发工具"
subtitle:   "Xcode 觉得好用的插件库"
date:       2016-06-13 21:30:00
author:     "DavidWang"
header-img: "img/post-bg-apple-event-2015.jpg"
catalog: true
tags:
    - ios
    - Xcode
---  


### 第一部分 Alcatraz插件的介绍

Alcatraz 是Xcode管理插件的工具,在上面能找到各种提高效率的插件。

源码地址：[https://github.com/FuzzyAutocomplete/FuzzyAutocompletePlugin](https://github.com/FuzzyAutocomplete/FuzzyAutocompletePlugin)

安装的方法也很简单，如果你以前没有安装过那执行下面指令

```
curl -fsSL https://raw.github.com/alcatraz/Alcatraz/master/Scripts/install.sh | sh
```

如果之前安装过但是Xcode最新版本不能用了，那就先卸载旧的再安装下新的。

卸载的方法是：

```
rm -rf ~/Library/Application\ Support/Developer/Shared/Xcode/Plug-ins/Alcatraz.xcplugin
rm -rf ~/Library/Application\ Support/Alcatraz/
```

### 第二部分 插件的安装

in Xcode: `go to Windows → Package Manager` and search for `插件名`.

![URL](https://camo.githubusercontent.com/70505dece9a75af5ca4715fff66271127f7d5b78/687474703a2f2f616c63617472617a2e696f2f696d616765732f6d656e754032782e706e67)

![URL](https://camo.githubusercontent.com/919efe4e1e53237df51d7010c862bd5c04fd6a70/687474703a2f2f616c63617472617a2e696f2f696d616765732f73637265656e73686f744032782e706e67)

### 第三部分 插件的汇总

#### VVDocumenter-Xcode

VVDocumenter 是一个Xcode的代码注释框架，可以简单的操作三斜杠`///`即可生成对应的代码注释，在菜单Window中找到VVDocumenter 可以进行一些个性化的设置。总体来说还是非常实用和好用的。

源码地址：[https://github.com/onevcat/VVDocumenter-Xcode](https://github.com/onevcat/VVDocumenter-Xcode)

![URL](https://camo.githubusercontent.com/ca5518c9872e15b8a95b9d8c5f44bc331977d710/68747470733a2f2f7261772e6769746875622e636f6d2f6f6e65766361742f5656446f63756d656e7465722d58636f64652f6d61737465722f53637265656e53686f742e676966)

**推荐指数：五星**

#### KSImageNamed

KSImageNamed 是一款Xcode 图片文件提示工具.当你输入`imageNamed: `的时候会弹出所有图片的名称和预览图片选择，。

源码地址：[https://github.com/ksuther/KSImageNamed-Xcode](https://github.com/ksuther/KSImageNamed-Xcode) 

![URL](https://camo.githubusercontent.com/c354bf04524df86daeabe7a6d2b9926fac790f85/68747470733a2f2f7261772e6769746875622e636f6d2f6b7375746865722f4b53496d6167654e616d65642d58636f64652f6d61737465722f73637265656e73686f742e676966)

**推荐指数：五星**

#### SCXcodeMiniMap

SCXcodeMiniMap 是Xcode中类似于sublime的功能，这是非常好用的。 并且右边的代码迷你地图提供了很多可配置的地方，而且会高亮显示宏和注释部分，小地图中点击任何地方也会自动滚动至此。这在有的类代码特别长时你写着写着都不知道自己在哪里了,会很有用.

源码地址：[https://github.com/stefanceriu/SCXcodeMiniMap](https://github.com/stefanceriu/SCXcodeMiniMap)

![URL](https://camo.githubusercontent.com/202fb6a7e1e1eb580af27ac00a0b3c80ef5b154d/68747470733a2f2f646c2e64726f70626f7875736572636f6e74656e742e636f6d2f752f31323734383230312f5265636f7264696e67732f534358636f64654d696e696d61702f76322e322f73656c656374656453796d626f6c496e7374616e636573486967686c69676874696e672e676966)

**推荐指数：四星**

#### BBUDebuggerTuckAway

BBUDebuggerTuckAway 在Xcode编辑代码的时候，调试框会自动缩回。

源码地址：[https://github.com/neonichu/BBUDebuggerTuckAway](https://github.com/neonichu/BBUDebuggerTuckAway)

![URL](https://github.com/neonichu/BBUDebuggerTuckAway/raw/master/plugin.gif)

**推荐指数：三星**

#### cocoapods-xcode-plugin

cocoapods-xcode-plugin 是一个Xcode上Pod管理工具。

源码地址：[https://github.com/kattrali/cocoapods-xcode-plugin](https://github.com/kattrali/cocoapods-xcode-plugin)

![URL](https://github.com/kattrali/cocoapods-xcode-plugin/raw/master/menu.png)

**推荐指数：三星**

#### SCXcodeSwitchExpander

SCXcodeSwitchExpander  是Xcode非常方便的创建枚举库，自动生成了所有可能，并且每种里面都包含代码块，可以直接tab切换。  虽然使用率不会特别高但是用到的时候还是非常方便的。

源码地址： [https://github.com/stefanceriu/SCXcodeSwitchExpander](https://github.com/stefanceriu/SCXcodeSwitchExpander)

![URL](https://camo.githubusercontent.com/a544a54d43b6e26c75d56889b7a6a4df8a90b4a5/68747470733a2f2f646c2e64726f70626f7875736572636f6e74656e742e636f6d2f752f31323734383230312f534358636f6465537769746368457870616e6465722f534358636f6465537769746368457870616e646572322e676966)

**推荐指数：四星**

#### HOStringSense

HOStringSense 是Xcode内容编辑工具，会把换行，空格转化成代码片段。

源码地址：[https://github.com/holtwick/HOStringSense-for-Xcode](https://github.com/holtwick/HOStringSense-for-Xcode)

![URL](https://github.com/holtwick/HOStringSense-for-Xcode/raw/master/StringDemoAnimation.gif)

**推荐指数：三星**

#### XAlign

XAlign 是Xcode中自动对齐库,可以在 `Xcode -> Edit -> XAlign` 设置快捷键。默认为 `Shift + Cmd + X`.

源码地址：[https://github.com/qfish/XAlign](https://github.com/qfish/XAlign)

![URL](https://camo.githubusercontent.com/7973c0e352b1f91e3efe5b3550cff5df97f4589a/687474703a2f2f7166692e73682f58416c69676e2f696d616765732f657175616c2e676966)

**推荐指数：五星**

#### Dash-Plugin-for-Xcode

Dash-Plugin-for-Xcode 是Bogdan Popescu开发的一款集成了Dash文档查看器应用的Xcode插件，允许开发者在使用Option-Click或作用相同的快捷键操作查看当前文本的相关文档时，用Dash代替Xcode的文档查看器。

源码地址：[https://github.com/omz/Dash-Plugin-for-Xcode](https://github.com/omz/Dash-Plugin-for-Xcode)

**推荐指数：三星**

#### Peckham

Peckham 是Xcode快速导入头文件的插件。

源码地址：[https://github.com/markohlebar/Peckham)

![URL](https://github.com/markohlebar/Peckham/raw/master/Misc/Peckham.gif)

**推荐指数：四星**

#### XToDo-Xcode

XToDo-Xcode 是一个注释辅助插件, 可以把项目中的 TODO FIXME等注释列出来. 

源码地址：[https://github.com/trawor/XToDo](https://github.com/trawor/XToDo)

![URL](https://github.com/trawor/XToDo/raw/master/screenshots/2.png)

**推荐指数：四星**