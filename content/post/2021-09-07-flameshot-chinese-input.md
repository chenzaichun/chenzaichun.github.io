+++
title = "Flameshot中文输入"
date = 2021-09-07T09:29:26+08:00
lastmod = 2021-09-07T10:36:02+08:00
tags = ["hugo", "org", "emacs"]
categories = ["emacs", "linux", "org"]
draft = false
+++

在我的系统上 `ubuntu 20.04` ， `flameshot 0.10.0` 上，中文输入的候选词窗口不可见。

查了一下 `flameshot` 的 `issue` ，关联的项有以下几条：

-   <https://github.com/flameshot-org/flameshot/issues/562>
-   <https://github.com/flameshot-org/flameshot/issues/1585>

查看 `flameshot` 进程的环境变量：

```sh
flameshotpid=$(pidof flameshot)
cat /proc/${flameshotpid}/environ | tr '\0' '\n'
```

可以看到对应的 `IM` 相关环境变量都是存在并且正常设置为 `fcitx` 的。

```sh
XMODIFIERS=@im=fcitx
GTK_IM_MODULE=fcitx
QT_IM_MODULE=fcitx
```

而且对应的 `fcitx-qt5-1` 这个包是已经安装状态。

使用  `fcitx-diagnose` 查看，发现了一个有趣的输出

```text
4.  User Interface:

    Found 2 enabled user interface addons:

        fcitx-classic-ui
        fcitx-kimpanel-ui

    Kimpanel process:

          12425 /usr/bin/gnome-shell
```

禁用 `kimpanel-ui`

{{< figure src="/ox-hugo/20210907_103547_KNd4pb.png" >}}

候选词窗口就出来了。
