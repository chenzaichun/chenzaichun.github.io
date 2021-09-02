+++
title = "企业微信发送消息"
date = 2021-09-02T10:05:26+08:00
lastmod = 2021-09-02T11:57:58+08:00
tags = ["hugo", "org", "emacs", "wxwork", "wechat"]
categories = ["emacs", "linux", "org"]
draft = false
+++

<div class="note">
  <div></div>

前提条件是设置企微联系人权限，可以参考[这里](https://work.weixin.qq.com/api/doc/90000/90135/92109#%E9%85%8D%E7%BD%AE%E5%8F%AF%E4%BD%BF%E7%94%A8%E5%AE%A2%E6%88%B7%E8%81%94%E7%B3%BB%E6%8E%A5%E5%8F%A3%E7%9A%84%E5%BA%94%E7%94%A8)

</div>


## 微信开发者工具 {#微信开发者工具}

直接打开页面，并在 `console` 下执行选择联系人的接口：

```js
wx.invoke('selectExternalContact', {
  "filterType": 0, //0表示展示全部外部联系人列表，1表示仅展示未曾选择过的外部联系人。默认值为0；除了0与1，其他值非法。在企业微信2.4.22及以后版本支持该参数
}, function(res){
  console.log(res);
  if(res.err_msg == "selectExternalContact:ok"){
    userIds  = res.userIds ; //返回此次选择的外部联系人userId列表，数组类型
  }else {
    //错误处理
  }
});
```

会出现错误：

```text
{errMsg: "selectExternalContact:fail, the permission value is offline verifying"}
```


## 企业微信桌面版 {#企业微信桌面版}

桌面版本开启调试模式可以参考这里：

-   <https://work.weixin.qq.com/api/doc/90000/90139/90315#%E5%AE%A2%E6%88%B7%E7%AB%AF%E8%B0%83%E8%AF%95>
-   <https://zhuanlan.zhihu.com/p/43484898>

<div class="note">
  <div></div>

新版本桌面端其实已经集成了开发者工具了，所以不需要安装了，只需要使用 `ctrl + alt + shift + d` 开启就好了

</div>

发送消息测试一下：

```js
wx.invoke('selectExternalContact', {
  "filterType": 0, //0表示展示全部外部联系人列表，1表示仅展示未曾选择过的外部联系人。默认值为0；除了0与1，其他值非法。在企业微信2.4.22及以后版本支持该参数
}, function(res){
  console.log(res);
  if(res.err_msg == "selectExternalContact:ok"){
    userIds  = res.userIds ; //返回此次选择的外部联系人userId列表，数组类型
  }else {
    //错误处理
  }
});
```

{{< figure src="/ox-hugo/20210901_175021_SZYCUd.png" >}}

错误的message是： `fail_no_permission`

但是权限已经按照前面的方法设置过了。

这里我们重新调用agentconfig配置一下：

```js
wx.agentConfig({
  agentid: "1000004",
  appId: "ww319d502067667268",
  corpid: "ww319d502067667268",
  jsApiList: ["selectExternalContact", "openEnterpriseChat", "shareToExternalContact"], //必填
  nonceStr: "VZ7ydRc7FDrc6wit",
  signature: "22024421a7dfd0d2a877e1cedd08c6d97619fb94",
  timestamp: "1630548726952",
  success: function(res) {
    // 回调
    console.log(res);
    // wx.invoke(
    //   "selectExternalContact",
    //   {
    //     filterType: 0, //0表示展示全部外部联系人列表，1表示仅展示未曾选择过的外部联系人。默认值为0；除了0与1，其他值非法。在企业微信2.4.22及以后版本支持该参数
    //   },
    //   function(res) {
    //     if (res.err_msg == "selectExternalContact:ok") {
    //       console.log(res.userIds);
    //       that.startPast(res.userIds); //返回此次选择的外部联系人userId列表，数组类型
    //     } else {
    //       //错误处理
    //     }
    //   }
    // );

    wx.invoke(
      "shareToExternalContact", {
      text: {
        content:"hello world",    // 文本内容
      },
    },
      function(res) {
        console.log(res);
        if (res.err_msg == "shareToExternalContact:ok") {
        }
      }
    );
  },
  fail: function(res) {
    console.log(res);
    if (res.errMsg.indexOf("function not exist") > -1) {
      alert("版本过低请升级");
    }
  },
});

```

{{< figure src="/ox-hugo/20210902_103413_1CSXtT.png" >}}

这个窗口终于弹出来了。

手机上测试：

```js
wx.agentConfig({
  agentid: "1000004",
  appId: "ww319d502067667268",
  corpid: "ww319d502067667268",
  jsApiList: ["selectExternalContact", "openEnterpriseChat", "shareToExternalContact"], //必填
  nonceStr: "daoiXrElKjprVYiC",
  signature: "59f0d81a81240c45ed37a80e86b5562f8ecafb40",
  timestamp: 1630550692634,
  success: function(res) {
    // 回调
    console.log(res);
    // wx.invoke(
    //   "selectExternalContact",
    //   {
    //     filterType: 0, //0表示展示全部外部联系人列表，1表示仅展示未曾选择过的外部联系人。默认值为0；除了0与1，其他值非法。在企业微信2.4.22及以后版本支持该参数
    //   },
    //   function(res) {
    //     if (res.err_msg == "selectExternalContact:ok") {
    //       console.log(res.userIds);
    //       that.startPast(res.userIds); //返回此次选择的外部联系人userId列表，数组类型
    //     } else {
    //       //错误处理
    //     }
    //   }
    // );

    wx.invoke(
      "shareToExternalContact", {
      text: {
        content:"hello world",    // 文本内容
      },
    },
      function(res) {
        console.log(res);
        if (res.err_msg == "shareToExternalContact:ok") {
        }
      }
    );
  },
  fail: function(res) {
    console.log(res);
    if (res.errMsg.indexOf("function not exist") > -1) {
      alert("版本过低请升级");
    }
  },
});
```

{{< figure src="/ox-hugo/20210902_111043_tETDiE.png" >}}

{{< figure src="/ox-hugo/20210902_111121_EKu87S.png" >}}

{{< figure src="/ox-hugo/20210902_111135_E5HdZc.png" >}}

这有点坑……
