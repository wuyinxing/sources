---
title: jQuery的链式调用
date: 2016-05-08
tags : jQuery
categories : JavaScript
---


>  阅读本文前，请阅读  [jQuery整体架构的无new构建](/2016/03/18/jQuery-globle)
> 

DOM链式调用的处理：

1. 节约JS代码.

2. 所返回的都是同一个对象，可以提高代码的效率

 

通过简单扩展原型方法并通过return this的形式来实现跨浏览器的链式调用。

利用JS下的简单工厂模式，来将所有对于同一个DOM对象的操作指定同一个实例。

这个原理就超简单了

```
	aQuery().init().name()

	分解
	a = aQuery();
	a.init()
	a.name()
```
把代码分解一下，很明显实现链式的基本条件就是实例this的存在，并且是同一个
```
aQuery.prototype = {
    init: function() {
        return this;
    },
    name: function() {
        return this
    }
}
````
所以我们在需要链式的方法访问this就可以了，因为返回当前实例的this,从而又可以访问自己的原型了

> 优点：节省代码量，提高代码的效率，代码看起来更优雅
> 
> 最糟糕的是所有对象的方法返回的都是对象本身，也就是说没有返回值，这不一定在任何环境下都适合。