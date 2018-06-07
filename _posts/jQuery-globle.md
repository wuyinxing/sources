---
title: jQuery整体架构的无new构建
date: 2016-05-04
tags : jQuery
categories : JavaScript
---

### 首先我们来看看jQuery的核心代码

```
(function( window, undefined ) {

	var jQuery = (function() {

		// 构建jQuery对象

		var jQuery = function( selector, context ) {

		return new jQuery.fn.init( selector, context, rootjQuery );

	}

	// jQuery对象原型

	jQuery.fn = jQuery.prototype = {

		constructor: jQuery,

		init: function( selector, context, rootjQuery ) {
		   //.......
		}

	};

	// Give the init function the jQuery prototype for later instantiation

	jQuery.fn.init.prototype = jQuery.fn;        // 这个非常关键的一点

	// 合并内容到第一个参数中，后续大部分功能都通过该函数扩展

	// 通过jQuery.fn.extend扩展的函数，大部分都会调用通过jQuery.extend扩展的同名函数

	jQuery.extend = jQuery.fn.extend = function() {};

	// 在jQuery上扩展静态方法

	jQuery.extend({
		//......
	});

	// 到这里，jQuery对象构造完成，后边的代码都是对jQuery或jQuery对象的扩展

	return jQuery;

	})();

	window.jQuery = window.$ = jQuery;

})(window);
```

好了看完源码开始分析

##  一：jQuery的无new构建

JavaScript是函数式语言，函数可以实现类，类就是面向对象编程中最基本的概念，通常： 

```
var aQuery = function(selector, context) {
        //构造函数
}
aQuery.prototype = {
    //原型
    name:function(){},
    age:function(){}
}

var a = new aQuery();

a.name();
```
这种常规的使用方法，显而易见，jQuery不是这样玩的

jQuery没有使用new运行符将jQuery显示的实例化，然后直接调用其函数

按照jQuery的抒写方式

> 
> $().ready()
> 
> $().hide()

要实现这样,那么jQuery就要看成一个类，那么$()应该是返回类的实例才对

** 所以尝试的把代码改一下：** 

```
var aQuery = function(selector, context) {
       return new aQuery();
}
aQuery.prototype = {
    name:function(){},
    age:function(){}
}
```
通过new aQuery()，虽然返回的是一个实例，OK ，死循环了！！！！

## 那么如何返回一个正确的实例？


> 在javascript中实例this只跟原型有关系   (这句话很重要哦)
>
> prototye 说白了就是一个特殊的 **对象** ， 每个函数都有一个默认的prototype属性 ，而 this表示当前对象 ； 没被绕晕就继续
> 

因此我们可以把jQuery类当作一个工厂方法来创建实例，把这个方法放到jQuery.prototye原型对象中

```
var aQuery = function(selector, context) {
       return  aQuery.prototype.init();
}
aQuery.prototype = {
    init:function(){
        return this;
    }
    name:function(){},
    age:function(){}
}
```
很明显aQuery()返回的是aQuery类的实例，在init中的this也是指向的aQuery类的实例

**问题来了init的this指向的是aQuery类，如果把init函数也当作一个构造器，那么内部的this要如何处理？**

```
var aQuery = function(selector, context) {
       return  aQuery.prototype.init();
}
aQuery.prototype = {
    init: function() {
        this.age = 18
        return this;
    },
    name: function() {},
    age: 20
}

aQuery().age  //18
```
这样的情况下就出错了，因为this只是指向aQuery类的，所以需要设计出独立的作用域才行 

-----------------

** jQuery框架分隔作用域的处理 ** 

```
jQuery = function( selector, context ) {
        // The jQuery object is actually just the init constructor 'enhanced'
        return new jQuery.fn.init( selector, context, rootjQuery );
    },
```
很明显通过实例init函数，每次都构建新的init实例对象，来分隔this,避免交互混淆

** 很显然一个是jQuery，一个是jQuery.fn.init 。返回的不是同一个对象那么肯定又出现一个新的问题 ** 

```
var aQuery = function(selector, context) {
       return  new aQuery.prototype.init();
}
aQuery.prototype = {
    init: function() {
        this.age = 18
        return this;
    },
    name: function() {},
    age: 20
}

//Uncaught TypeError: Object [object Object] has no method 'name' 
console.log(aQuery().name())
```

抛出错误，无法找到这个方法，所以很明显new的init函数跟jquery类的this分离了，背离我们  `要实现这样,那么jQuery就要看成一个类，那么$()应该是返回类的实例才对` 这句话

## 怎么访问jQuery类原型上的属性与方法？

** 关键点 ** 
```
jQuery.fn = jQuery.prototype

jQuery.fn.init.prototype = jQuery.fn;

//也就是

jQuery.fn.init.prototype = jQuery.prototype

```

通过原型传递解决问题，把jQuery的原型传递给jQuery.prototype.init.prototype

换句话说jQuery的原型对象覆盖了init构造器的原型对象，this的问题也就解决了

因为是引用传递所以不需要担心这个循环引用的性能问题

```
var aQuery = function(selector, context) {
       return  new aQuery.prototype.init();
}
aQuery.prototype = {
    init: function() {
        return this;
    },
    name: function() {
        return this.age
    },
    age: 20
}

aQuery.prototype.init.prototype = aQuery.prototype;

console.log(aQuery().name()) //20
```