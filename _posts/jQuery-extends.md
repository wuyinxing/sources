---
title: jQuery.extend 和 jQuery.fn.extend
date: 2016-05-16
tags : jQuery
categories : JavaScript
---


>  阅读本文前，请阅读  [jQuery整体架构的无new构建](/2016/03/18/jQuery-globle)
> 

jQuery的主体框架就是这样，但是根据一般设计者的习惯，如果要为jQuery或者jQuery prototype添加属性方法，同样如果要提供给开发者对方法的扩展，从封装的角度讲是不是应该提供一个接口才对，字面就能看懂是对函数扩展，而不是看上去直接修改prototype.友好的用户接口，

jQuery支持自己扩展属性，这个对外提供了一个接口，jQuery.fn.extend()来对对象增加方法

从jQuery的源码中可以看到，jQuery.extend和jQuery.fn.extend其实是同指向同一方法的不同引用

```

jQuery.extend = jQuery.fn.extend = function() {}

jQuery.extend 对jQuery本身的属性和方法进行了扩展

jQuery.fn.extend 对jQuery.fn的属性和方法进行了扩展 

```

通过extend()函数可以方便快速的扩展功能，不会破坏jQuery的原型结构

jQuery.extend = jQuery.fn.extend = function(){...}; 这个是连等，也就是2个指向同一个函数，怎么会实现不同的功能呢？这就是this 力量了！

针对fn与jQuery其实是2个不同的对象，在之前有讲述：

- jQuery.extend 调用的时候，this是指向jQuery对象的(jQuery是函数，也是对象！)，所以这里扩展在jQuery上。

- 而jQuery.fn.extend 调用的时候，this指向fn对象，jQuery.fn 和jQuery.prototype指向同一对象，扩展fn就是扩展jQuery.prototype原型对象。 (从[jQuery整体架构的无new构建](/2016/03/18/jQuery-globle)可以推理出 jQuery.fn.extend = jQuery.prototype.extend)

- 这里增加的是原型方法，也就是对象方法了。所以jQuery的api中提供了以上2中扩展函数。

## 在实际应用中

> Objectj Query.extend( target, object1, [objectN])   用一个或多个其他对象来扩展一个对象，返回被扩展的对象
>
> jQuery.fn.extend(object) 对jQuery.prototype进得扩展，就是为jQuery类添加“成员函数”。jQuery类的实例可以使用这个“成员函数”。