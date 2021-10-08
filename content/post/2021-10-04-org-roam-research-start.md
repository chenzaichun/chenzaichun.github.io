+++
title = "开启org roam之旅"
date = 2021-10-04T10:40:49+08:00
lastmod = 2021-10-08T09:30:45+08:00
tags = ["hugo", "org", "emacs"]
categories = ["emacs", "linux", "org"]
draft = false
+++

## 前言 {#前言}

听说 `org-roam` 已经差不多一年多了，但是一直没有去实践，前面阅读了《认知天性》这本书，
觉得自己还是应该整理一下目前的笔记系统（太乱了，每次都在很多文件里面用grep大法）。趁
这个十一没出门，在家里也不太想工作的事情，于是就折腾一下。


## 参考连接 {#参考连接}

主要的参考来源有以下这些（排名不分先后）

-   [如何用好 Roam Research ？（一）：「双向链接」](https://zhuanlan.zhihu.com/p/378591718)
-   [如何用好 Roam Research ？（二）：笔记节点粒度](https://zhuanlan.zhihu.com/p/378597918)
-   [如何用好 Roam Research ？（三）：Roam 不是卡片盒](https://zhuanlan.zhihu.com/p/378627738)
-   [Discovering org-roam](https://www.lucacambiaghi.com/posts/discovering-org-roam.html)
-   [Andyʼs working notes](https://notes.andymatuschak.org/About%5Fthese%5Fnotes)
-   [org-roam manual](https://www.orgroam.com/manual.html)
-   [使用 org-roam 构建自己的知识网络](https://www.zmonster.me/2020/06/27/org-roam-introduction.html)


## 安装 {#安装}

因为我使用的是 `spacemacs=， 在 =spacemacs` 的 `layers` 中enable `org-roam` ：

```lisp
(org :variables
     ...
     org-enable-roam-support t
     org-enable-roam-protocol t
     org-enable-roam-server t
     org-roam-directory "~/org/roam/"
     org-roam-v2-ack t
     ...
     )
```

如果想用最新的代码（非melpa），可以直接在 `additional-package` 里面配置对应的安装方式，这里选择的是 `master` 分支：

```lisp
(org-roam :location (recipe :fetcher github :repo "org-roam/org-roam" :branch "master"))
```

使用 `org-roam-version` 查看，当前版本为  `2.1.0`


## 使用 {#使用}

先使用 `org-roam-capture` 来创建一篇测试，这个跟 `org-capture` 是一致的（对于模板，我们后面熟悉后再配置）。

然后文件就船舰在了 `~/org/roam/` 下了。

创建连接测试，使用 `org-roam-node-insert` 可以链接到其他node或者创建一个新的node，
这样就可以创建关联关系了。

然后使用 `org-roam-graph` 查看，发现出错了

```text
Wrong type argument: commandp, org-roam-graph
```

这是什么情况？（后面有说明，org-roam没有安装好）

查看文档，文档上说现在有个 `org-roam-buffer` 显示对应的关系，可是我这边没有找到，
buffer是空的。

{{< figure src="/ox-hugo/20211007_115126_R54b1v.png" >}}

查看日志，发现有个错误：

```text
No such file or directory, org-roam-protocol
```

是没有安装好的原因，重新安装 `org-roam` 就可以了。

{{< figure src="/ox-hugo/20211007_121956_dLNa4X.png" >}}


### org-roam-protocol {#org-roam-protocol}

根据文档配置好desktopfile，见[这里](https://www.orgroam.com/manual.html#Org%5F002droam-Protocol)

`~/.local/share/applications/org-protocol.desktop`

```text
[Desktop Entry]
Name=Org-Protocol
Exec=emacsclient %u
Icon=emacs-icon
Type=Application
Terminal=false
MimeType=x-scheme-handler/org-protocol
```

```sh
xdg-mime default org-protocol.desktop x-scheme-handler/org-protocol
```

```sh
sudo mkdir -p /etc/opt/chrome/policies/managed/
sudo tee /etc/opt/chrome/policies/managed/external_protocol_dialog.json >/dev/null <<'EOF'
{
  "ExternalProtocolDialogShowAlwaysOpenCheckbox": true
}
EOF
sudo chmod 644 /etc/opt/chrome/policies/managed/external_protocol_dialog.json
```


## 模板 {#模板}

这里参考了[这里](https://www.zmonster.me/2020/06/27/org-roam-introduction.html)的模板设置

```elisp
(setq org-roam-capture-templates
      '(
        ("d" "default" plain (function org-roam-capture--get-point)
         "%?"
         :file-name "%<%Y%m%d%H%M%S>-${slug}"
         :head "#+title: ${title}\n#+roam_alias:\n#+roam_key:\n#+roam_tags:\n\n")
        ("a" "Annotation" plain (function org-roam-capture--get-point)
         "%U ${body}\n"
         :file-name "${slug}"
         :head "#+title: ${title}\n#+roam_key: ${ref}\n#+roam_alias:\n#+roam_tags:\n\n"
         :immediate-finish t
         :unnarrowed t)
        ("g" "group")
        ("ga" "Group A" plain (function org-roam-capture--get-point)
         "%?"
         :file-name "%<%Y%m%d%H%M%S>-${slug}"
         :head "#+title: ${title}\n#+roam_alias:\n\n")
        ("gb" "Group B" plain (function org-roam-capture--get-point)
         "%?"
         :file-name "%<%Y%m%d%H%M%S>-${slug}"
         :head "#+title: ${title}\n#+roam_alias:\n\n")
        )
      )
```

测试一下，发现出错了，错误如下:

```text
Template needs to specify ‘:target’
```

原来是因为模板的格式已经变化了。修改一下：

```elisp
(setq org-roam-capture-templates
      '(
        ("d" "default" plain "%?"
         :target (file+head "%<%Y%m%d%H%M%S>-${slug}.org"
                            "#+title: ${title}\n#+roam_alias:\n#+roam_key:\n#+roam_tags:\n\n")
         :unnarrowed t)
        )
      )

(setq org-roam-capture-ref-templates
      '(
        ("a" "Annotation" plain
         "%U ${body}\n"
         :target (file+head "${slug}.org"
                            "#+title: ${title}\n#+roam_key: ${ref}\n#+roam_alias:\n#+roam_tags:\n\n")
         ;; :immediate-finish t
         :unnarrowed t
         )
        ("r" "ref" plain ""
         :target (file+head "${slug}.org"
                            "#+title: ${title}\n#+roam_key: ${ref}\n#+roam_alias:\n#+roam_tags:\n\n")
         :unnarrowed t)
        )
      )
```

添加俩个 `chrome` 书签，内容如下：

```js
javascript:location.href = 'org-protocol://roam-ref?template=r&ref=' + encodeURIComponent(location.href) + '&title=' + encodeURIComponent(document.title)
```

```js
javascript:location.href = 'org-protocol://roam-ref?template=a&ref=' + encodeURIComponent(location.href) + '&title='+encodeURIComponent(document.title) + '&body='+encodeURIComponent(function(){var html = "";var sel = window.getSelection();if (sel.rangeCount) {var container = document.createElement("div");for (var i = 0, len = sel.rangeCount; i < len; ++i) {container.appendChild(sel.getRangeAt(i).cloneContents());}html = container.innerHTML;}var dataDom = document.createElement('div');dataDom.innerHTML = html;['p', 'h1', 'h2', 'h3', 'h4'].forEach(function(tag, idx){dataDom.querySelectorAll(tag).forEach(function(item, index) {var content = item.innerHTML.trim();if (content.length > 0) {item.innerHTML = content + '&#13;&#10;';}});});return dataDom.innerText.trim();}())
```

基本搞定。现在是思考怎么用起来的问题了。


## org-roam-ui {#org-roam-ui}

安装，首先在addtional pacakges里面添加

```lisp
(org-roam-ui :location (recipe :fetcher github :repo "org-roam/org-roam-ui" :branch "main" :files ("*.el" "out")))
```

然后配置

```lisp
(use-package org-roam-ui
  :after org-roam
  ;;         normally we'd recommend hooking orui after org-roam, but since org-roam does not have
  ;;         a hookable mode anymore, you're advised to pick something yourself
  ;;         if you don't care about startup time, use
  ;;  :hook (after-init . org-roam-ui-mode)
  :config
  (setq org-roam-ui-sync-theme t
        org-roam-ui-follow t
        org-roam-ui-update-on-save t
        org-roam-ui-open-on-start t)
  )
```

{{< figure src="/ox-hugo/20211007_183620_BNt7QS.png" >}}

虽然提供了ui，但是我并不是很喜欢这个功能（因为我感觉有点无用），反而我想要的是[这里](https://notes.andymatuschak.org/About%5Fthese%5Fnotes)的效果。
