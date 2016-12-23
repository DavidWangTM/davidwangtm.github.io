---
layout:     post
title:      "Ubuntu 安装GitLab"
subtitle:   "Ubuntu 安装gitlab"
date:       2016-12-22 07:00:00
author:     "DavidWang"
header-img: "img/post-bg-e2e-ux.jpg"
catalog: true
tags:
    - Ubuntu
    - GitLab
--- 

### Ubuntu 14.04下安装GitLab指南

#### 1.安装需要的库和软件

```
sudo apt-get install curl openssh-server ca-certificates postfix
```

![IMG](/img/in-post/gitlab_img1.png)   

![IMG](/img/in-post/gitlab_img2.png)   

#### 2. 添加GitLab的包并进行安装

```
curl https://packages.gitlab.com/install/repositories/gitlab/gitlab-ce/script.deb.sh | sudo bash
sudo apt-get install gitlab-ce
```

### 3. 配置和启动GitLab

```
sudo vim /etc/gitlab/gitlab.rb
```

配置完成之后，需要执行下面的命令使得变更生效。

```
sudo gitlab-ctl reconfigure
```

有时候你还需要重启postfix

```
sudo /etc/init.d/postfix restart
```

#### 4.访问GitLab

这样你就可以通过访问： http://192.168.1.10:8080 来进行GitLab的访问了

默认管理员的账户密码：

```
Username: root
Password: 5iveL!fe
```

#### 5. 参考资料

1. [GitLab安装篇-Ubuntu 14.04 LTS](http://www.tuicool.com/articles/3uAzay)
2. [GitLab官网安装指南](https://about.gitlab.com/downloads/)
3. [GitLab中的SMTP服务器设置的例子](https://gitlab.com/gitlab-org/omnibus-gitlab/blob/master/doc/settings/smtp.md#examples)
4. [postfix的基础介绍和新手指南](http://wiki.ubuntu.org.cn/PostfixBasicSetupHowto)




