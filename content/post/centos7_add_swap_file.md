+++
title = "oracle install mkswap"
date = 2018-12-21T17:54:00+08:00
lastmod = 2018-12-21T18:02:19+08:00
tags = ["linux", "emacs", "org"]
categories = ["linux"]
draft = false
weight = 2001
+++

-   State "DONE"       from "TODO"       <span class="timestamp-wrapper"><span class="timestamp">[2018-12-21 Fri 17:54]</span></span>

```sh
dd if=/dev/zero of=/swapfile bs=1024 count=4096000
chmod 600 /swapfile
mkswap /swapfile
swapon /swapfile
```

<!--more-->

{{< figure src="/ox-hugo/20181221_175916_4y7Q1C.png" >}}
