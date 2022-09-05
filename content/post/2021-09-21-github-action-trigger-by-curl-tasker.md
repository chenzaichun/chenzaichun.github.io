+++
title = "使用tasker自动触发打卡早起"
date = 2021-09-21T10:40:26+08:00
lastmod = 2022-09-05T16:02:51+08:00
tags = ["hugo", "org", "emacs"]
categories = ["emacs", "linux", "org"]
draft = false
+++

## 前言 {#前言}

自从使用github issue来记录之后，运动渐渐的适应了，记得去记录了。但是有个问题就是，早起等的打开我始终会忘记。
这里能不能搞成自动的呢？


## github action {#github-action}

参考链接：

-   <https://goobar.dev/manually-trigger-a-github-actions-workflow/>
-   <https://dev.to/rikurouvila/how-to-trigger-a-github-action-with-an-htt-request-545>
-   <https://github.com/yihong0618/gitblog/issues/198> （主要参考yihong的记录)

因为本身有action的支持，先拿到这个项目的 actions id (需要自行申请 token)

```sh
token=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
curl -XGET https://api.github.com/repos/chenzaichun/2021/actions/workflows \
     -H "Authorization: token ${token}" # change to your config

```

得到的结果如下形式：

```json
{
  "total_count": 3,
  "workflows": [
    {
      "id": 12980024,
      "node_id": "W_kwxxxxxxx",
      "name": "GET UP",
      "path": ".github/workflows/get_up.yml",
      "state": "active",
      "created_at": "2021-09-09T12:41:48.000+08:00",
      "updated_at": "2021-09-09T12:41:48.000+08:00",
      "url": "https://api.github.com/repos/chenzaichun/2021/actions/workflows/12980024",
      "html_url": "https://github.com/chenzaichun/2021/blob/main/.github/workflows/get_up.yml",
      "badge_url": "https://github.com/chenzaichun/2021/workflows/GET%20UP/badge.svg"
    },
    {
      "id": 12980025,
      "node_id": "W_kwxxxxxxx",
      "name": "Replace README",
      "path": ".github/workflows/replace_readme.yml",
      "state": "active",
      "created_at": "2021-09-09T12:41:48.000+08:00",
      "updated_at": "2021-09-09T12:41:48.000+08:00",
      "url": "https://api.github.com/repos/chenzaichun/2021/actions/workflows/12980025",
      "html_url": "https://github.com/chenzaichun/2021/blob/main/.github/workflows/replace_readme.yml",
      "badge_url": "https://github.com/chenzaichun/2021/workflows/Replace%20README/badge.svg"
    },
    {
      "id": 12980026,
      "node_id": "W_kwxxxxxxx",
      "name": "Get Daily",
      "path": ".github/workflows/run_daily.yml",
      "state": "active",
      "created_at": "2021-09-09T12:41:48.000+08:00",
      "updated_at": "2021-09-09T12:41:48.000+08:00",
      "url": "https://api.github.com/repos/chenzaichun/2021/actions/workflows/12980026",
      "html_url": "https://github.com/chenzaichun/2021/blob/main/.github/workflows/run_daily.yml",
      "badge_url": "https://github.com/chenzaichun/2021/workflows/Get%20Daily/badge.svg"
    }
  ]
}
```

我们需要的是 `Get Up` 的id `12980024`

```sh
curl -H "Content-Type:application/json" -XPOST \
     -d '{
"inputs": {},
"ref": "main"
}' \
     https://api.github.com/repos/chenzaichun/2022/actions/workflows/17148798/dispatches  \
     -H "Authorization: token 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"  # change to your config
```

测试一下，可以触发。


## tasker {#tasker}

tasker怎么配置呢，创建一个任务 `早起打卡` , 创建一个 `运行Shell命令` 的代码操作。

如下就好。

{{< figure src="/ox-hugo/Screenshot_20210921-103012.jpg" >}}

搞定。