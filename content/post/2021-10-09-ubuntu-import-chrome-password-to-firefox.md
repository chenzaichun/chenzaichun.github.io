+++
title = "ubuntu下将chrome中的密码导入到firefox中"
date = 2021-10-09T11:00:49+08:00
lastmod = 2021-10-09T11:21:47+08:00
tags = ["hugo", "org", "emacs"]
categories = ["emacs", "linux", "org"]
draft = false
+++

## 前言 {#前言}

ubuntu下firefox的导入功能提示可以到入chrome的password，但是实际上没有找到对应的功能：

{{< figure src="/ox-hugo/20211009_110103_YDeXKD.png" >}}

{{< figure src="/ox-hugo/20211009_110126_RPgJoV.png" >}}

难道在windows支持？可是我没有windows的环境，所以无法验证。


## 解决 {#解决}

查到网络，发现了[这里](https://superuser.com/questions/1355790/import-chrome-passwords-to-firefox)


### 导出chrome的密码 {#导出chrome的密码}

首先通过chrome将密码导出为文件（csv）：

```text
Google Chrome > Settings > Passwords > Next to Saved Passwords there're 3 dots, press them > Export passwords -> Save to Desktop
```

{{< figure src="/ox-hugo/20211009_110405_dOfClM.png" >}}

导入为文件之后，我们后面将用到。


### 导入chrome密码 {#导入chrome密码}

这里借助了python的一个库叫作 `ffpass` ，使用pip安装

```sh
pip install ffpass
```

然后导入到firefox

```sh
ffpass import --file ~/Documents/Chrome\ Passwords.csv
```

重启firefox，然后就可以看到密码已经导入进去了。