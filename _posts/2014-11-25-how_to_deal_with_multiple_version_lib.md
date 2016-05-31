---
layout:     post
title:      "如何解决“保护多库版本”问题"
subtitle:   ""
date:       2014-11-25 12:00:00
author:     "Wenzhiquan"
header-img: "img/post-bg-e2e-ux.jpg"
catalog: false
tags:
    - Fedora
---

今天在fedora21上安装wine的时候，遇到一个问题，错误提示为：保护多库版本 ...

搜查了一圈，找到了一个解决方案

首先将fedora进行降级：

```
sudo yum distro-sync
```

然后进行源的清理：

```
sudo yum clean all && sudo yum makecache
```

最后，重新升级fedora：

```
sudo yum update
```
再进行安装就可以了
