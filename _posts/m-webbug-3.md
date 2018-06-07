---
title: Android手机浏览器overflow是scroll失效
date: 2016-06-23
tags : scroll
categories : mobileWeb
---

>  在一个微信web项目中，发现Android手机浏览器中 overflow : scroll 失效
>
>  Android低版本中 QQ X5浏览器 对div的 overflow支持很差，只能用body和html。
>
>  在4.0以上的浏览器中不能存在两个overflow是scroll的div，即：嵌套的overflow : scroll，第二个一定失效
>
>  第一种办法就是 : 在需要scroll时候，尽量保证当前页面只有一个overflow : scroll 作用于页面
>
>  第二种办法就是 : 采用iScroll插件（早期用过iScroll4，对于现在的iScroll5不怎么了解,还需要注意的是iscroll是存在BUG的）
