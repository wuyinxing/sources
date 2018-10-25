---
title: javascript 常用的“沙箱模式”
date: 2018-10-25
tags : Sandbox
categories : javascript
---

## 一、理解沙箱 ##

> 1、 javascript中的沙箱是指，javascript中处理模块依赖关系的闭包被称之为沙箱

> 2、 沙箱模式(Sandbox Pattern),顾名思义沙箱模式是创建了一个"沙箱"，可以理解为创建了一个黑盒，我们不管在里面做什么都不会影响到外面。而在JavaScript中就意味着，在沙箱中的操作被限死在当前作用域，不会对其他模块和个人沙箱造成任何影响。


## 二、常用写法 ##

modules必须是数组，callback必须是函数，两个参数都要传不然会报错。


```
(function(w) {
	var Sandbox = function(modules, callback) {
		if(!(this instanceof Sandbox)) {
			return new Sandbox(modules, callback);
		}
		for(var i=0, len=modules.length; i<len; i++) {
			Sandbox.modules[modules[i]](this);
		}
		callback(this);
	}
	Sandbox.modules = {};
	w.Sandbox = Sandbox;
})(this);
Sandbox.modules.get = function() {
	alert('123123');
}
Sandbox(['get'], function() {});
```

## 三、兼容写法  ##

```
function Sandbox() {
	var args = Array.prototype.slice.call(arguments), // 参数转成数组形式
		callback = args.pop(), // 取出数组最后一个元素（回调函数）
		modules = (args[0] && typeof args[0]==='string') ? args : args[0]; // 判断数组剩下内容的第一个元素
	if(!(this instanceof Sandbox)) { // 如果不是沙箱实例，则实例化
		return new Sandbox(modules, callback);
	}
	if(!modules || modules[0]==='*') { // 判断模块参数是否存在或者第一个参数是否是*
		modules = [];
		for(var i in Sandbox.modules) {
			if(Sandbox.modules.hasOwnProperty(i)) {
				modules.push(i);
			}
		}
	}
	for(var i=0; i<modules.length; i++) { // 循环模块调用
		Sandbox.modules[modules[i]](this);
	}
	callback(this); // 回调
}
Sandbox.prototype = { // 原型
	name: 'sandbox',
	version: '1.0.0',
	getName: function() {
		return this.name;
	}
}
// 定义模块及调用
Sandbox.modules = {};
Sandbox.modules.event = function(box) {
	box.addEvent = function(selector, evtype, callback) {
		selector.addEventListener(evtype, callback);
	}
}
Sandbox(['event'], function(box) {
	box.addEvent(document, 'click', function(e) {
		alert(e.clientX+'px, '+e.clientY+'px');
	});
});
```