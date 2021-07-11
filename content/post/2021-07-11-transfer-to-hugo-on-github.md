+++
title = "将博客从heroku迁移到github"
author = ["Zai-Chun Chen"]
date = 2021-07-11T16:29:26+08:00
lastmod = 2021-07-11T18:18:50+08:00
tags = ["hugo", "org", "emacs"]
categories = ["emacs", "linux", "org"]
draft = false
+++

## 前言 {#前言}

虽然我的博客很久都没有更新过了，以前写博客的过程是相当艰辛，历经了很多次迁移。最近在思考一个问题，为什么我现在连博客也不写了？

趁这个周末，我将原来布署在 `heroku` 上的博客迁移到了 `github` （虽然我曾经也使用过github），也将博客转换从 `hexo` 迁移到了  =hugo=。


## 迁移过程 {#迁移过程}

我以前也记录了怎么从 `hexo` 迁移到 `hugo` 的文章，需要转换的可以看一下。以前我使用 `ox-hugo` 来转换到hugo支持的markdown，现在我决定不再使用 `ox-hugo` 了，我决定直接使用 `org-mode` 来写博客。


### 初始化博客 {#初始化博客}

安装hugo直接参考官方文档。这里先初始化

```sh
hugo new site /path/to/site
```


### 配置主题 {#配置主题}

这里我使用 `submodule` 的方式引入了 `even` 主题，为了保证兼容性，我从[官方](https://github.com/olOwOlo/hugo-theme-even)仓库 `fork` 了一份出来，防止以后升级不兼容，然后使用 `submodule` 引入：

```sh
git submodule add https://github.com/chenzaichun/hugo-theme-even.git themes/even
```


### 配置 {#配置}

拷贝  `themes/even/exampleSite/config.toml` 到site根目录下，然后按照要求修改配置。


### 配置github actions自动发布 {#配置github-actions自动发布}


#### 设置密钥 {#设置密钥}

生成密钥

```sh
ssh-keygen -t rsa -b 4096 -C "$(git config user.email)" -f gh-pages -N ""
```

得到 `gh-pages` 和 `gh-pages.pub` 两个文件

打开 `GitHub` 上 `Hugo` 项目代码库的 `Setting` 页面

-   `Deploy keys` > `Add deploy key` ，把文件 `gh-pages.pub` 的内容填入，勾选 `Allow write access`
-   `Secrets` > `Add a new secret` ，Name 为 `ACTIONS_DEPLOY_KEY` ，Value 为文件 `gh-pages` 的内容


#### 添加配置文件 {#添加配置文件}

这里使用了[peaceiris/actions-hugo](https://github.com/peaceiris/actions-hugo) 来自动发布，我修改了一下， 使用了 `main` 分支来发布， `.github/workflows/gh-pages.yml` 配置如下：

```yaml
name: github pages

on:
  push:
    branches:
      - main  # Set a branch to deploy
  pull_request:

jobs:
  deploy:
    runs-on: ubuntu-20.04
    steps:
      - uses: actions/checkout@v2
        with:
          submodules: true  # Fetch Hugo themes (true OR recursive)
          fetch-depth: 0    # Fetch all history for .GitInfo and .Lastmod

      - name: Setup Hugo
        uses: peaceiris/actions-hugo@v2
        with:
          hugo-version: '0.85.0'
          extended: true

      - name: Build
        run: hugo --minify

      - name: Deploy
        uses: peaceiris/actions-gh-pages@v3
        if: github.ref == 'refs/heads/main
        with:
          github_token: ${{ secrets.GITHUB_TOKEN }}
          publish_dir: ./public
```

进入repo设置界面，设置pages：

{{< figure src="/ox-hugo/20210711_180305_TQ66as.png" >}}


### comment {#comment}

这里采用了[utteranc](https://utteranc.es/?installation%5Fid=18211811&setup%5Faction=install) , 配置见对应的文档。这里不再详述


## 迁移完成 {#迁移完成}

每次push代码后会自动发布
