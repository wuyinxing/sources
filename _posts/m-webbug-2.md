---
title: 移动web开发中的全局设置
tags : globle
categories : mobileWeb
---

## 字体设置 ##

```
body {
    font-family: "Helvetica Neue", Helvetica, STHeiTi, sans-serif;
}
```

## 设置全局的CSS样式 ##
```
a, img {
    -webkit-touch-callout: none; /* 禁止长按链接与图片弹出菜单 */
}

html, body , .xxx {
    -webkit-user-select: none;   /* 禁止选中文本（如无文本选中需求，此为必选项） */
    user-select: none;
}

html, a, .xxx {
    -webkit-tap-highlight-color: rgba(0, 0, 0, 0);  /* 移动端a标签 取消touch高亮效果 */
}


img {
    -webkit-touch-callout: none;   /* 何禁止保存或拷贝图像(该方法只在 iOS 上有效。) */
}

.xxx {
    overflow: auto; /* auto | scroll */
    -webkit-overflow-scrolling: touch;     /* 加速滚动，这个属性存在一定的兼容性 */
}


html {
    -webkit-text-size-adjust: 100%;  /* 禁止文本缩放 当移动设备横竖屏切换时，文本的大小会重新计算，进行相应的缩放，当我们不需要这种情况时，可以选择禁止 */
}

```

## 邮箱设置 ##

> 在 Android （iOS不会）上，浏览器会自动识别看起来像邮箱地址的字符串，不论有你没有加上邮箱链接，当你在这个字符串上长按，会弹出发邮件的提示。

```
<meta name="format-detection" content="email=no" />   //关闭邮箱地址识别

<a mailto:"dooyoe@gmail.com">dooyoe@gmail.com</a> //  开启邮件发送
 
```
## 移动端手机号码设置 ##


```
<meta name="format-detection" content="telephone=no" /> //关闭电话识别 （iOS（其他浏览器和Android均不会）上会对看起来像是电话号码的数字处理为电话链接）

<a href="tel:123456">123456</a>  //开启电话功能

<a href="sms:123456">123456</a> // 开启短信功能
 
```

## 屏幕设置 ##

```
<meta content="width=device-width, initial-scale=1.0, maximum-scale=1.0, user-scalable=0" name="viewport">

```
>  width                      – viewport的宽度
>  height                     – viewport的高度
>  initial-scale          – 初始的缩放比例
>  minimum-scale  – 允许用户缩放到的最小比例
>  maximum-scale – 允许用户缩放到的最大比例
>  user-scalable       – 用户是否可以手动缩放


```
<!-- IOS中Safari允许全屏浏览 -->
<meta content="yes" name="apple-mobile-web-app-capable">  

<!-- IOS中Safari顶端状态条样式 -->
<meta content="black" name="apple-mobile-web-app-status-bar-style"> 

<!-- 忽略邮箱地址电话 -->
<meta name="format-detection" content="telephone=no">
<meta name="format-detection" content="email=no">
<meta name="format-detection" content="adress=no">
也可以连写：meta name="format-detection" content="telephone=no,email=no,adress=no" 

<!-- IOS中Safari设置保存到桌面图标 -->
<link rel="apple-touch-icon" href="custom_icon.png"> 

<!-- uc强制竖屏 -->
<meta name="screen-orientation" content="portrait">

<!-- UC强制全屏 --> 
<meta name="full-screen" content="yes">

<!-- UC应用模式 --> 
<meta name="browsermode" content="application">

<!-- QQ强制竖屏 -->
<meta name="x5-orientation" content="portrait">

<!-- QQ强制全屏 -->
<meta name="x5-fullscreen" content="true">

<!-- QQ应用模式 -->
<meta name="x5-page-mode" content="app">

... ... 

```