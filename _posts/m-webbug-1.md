---
title: 触发手机端浏览器中html的搜索框input
tags : form
categories : mobileWeb
---

> 用html5做一个手机端浏览的网页。里面有个搜索框(input='search'),
>
> 想和baidu等网站一样不单独加一个搜索按钮在页面上，而是使用输入法软键盘里的搜索按钮或图标来触发事件。
>

### 实现代码 
```
<form action="" method="get" >
	<input title="keyword" type="search" />
</form>

//也就是给input添加一个 form 外层！！！

```

当然在移动端会根据不同的场景来处理这种问题， 比方说加一个搜索按钮等等，只要不妨碍用户体验就好