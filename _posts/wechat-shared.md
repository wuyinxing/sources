---
title: 微信分享和SDK
date: 2016-08-27
tags : wechat
categories : JavaScript
---

## 1 、最简单的方法

> 标题：会取当前页面title里面的内容。
> 
> 图片：会取当前页面body内最前面的一张符合条件的图片。

**图片规格有要求：**

**尺寸必须大于： 300px * 300px
把符合以上两个条件的图片放到`<img>`里，放到页面`<body>`内的最前面。
这样分享时就会取这张图作为缩略图了。**

```
<head>
    <title>微信分享</title>
</head>
<body>
	<div style='margin:0 auto;width:0px;height:0px;overflow:hidden;'>
		<img src="" width='300'>
	</div>
</body>
// 图片实际大小最小不能小于 300*300 ，不能通过设置图片样式 width=800 之类的以期骗过微信
```

## 2 微信JS-SDK

微信JS-SDK是微信公众平台面向网页开发者提供的基于微信内的网页开发工具包（例如：分享回调，扫一扫，支付，打开微信地图等等等）

**具体我们可以通过官网地址操作：**[微信JS-SDK](https://mp.weixin.qq.com/wiki/7/aaa137b55fb2e0456bf8dd9148dd613f.html "微信JS-SDK"); 