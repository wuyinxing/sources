---
title: window.open不被拦截的解决方法之一
tags : window
categories : HTML
---

> 在处理页面ajax请求过程中，想实现请求后打开新页面，就想到通过 window.open 来实现，但是最终都被浏览器拦截了。
>
> 在谷歌搜索有没有解决方法，有些说可以通过新建a标签，模拟点击来实现，但是测试发现都实现不了，照样被浏览器拦截。
>
> 最后找到了一个折中的办法，可以实现新页面打开，但是没有a标签的那种直接流量新页面的效果。
>
> 当然这只是解决方法之一

### 实现代码：
```
$obj.click(function(){
	var newTab=window.open('about:blank');
	$.ajax({
		success:function(data){
			if(data){
			    //window.open('http://www.baidu.com');
			    newTab.location.href="http://www.baidu.com";
			}
		}
	})
})
```