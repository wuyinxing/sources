---
title: Javascript模块化编程 — RequireJS
date: 2016-09-22
tags : require
categories : JavaScript
---
RequireJS是一个工具库，主要用于客户端的模块管理。它可以让客户端的代码分成一个个模块，实现异步或动态加载，从而提高代码的性能和可维护性。它的模块管理遵守AMD规范（Asynchronous Module Definition）。

RequireJS的基本思想是，通过define方法，将代码定义为模块；通过require方法，实现代码的模块加载。

## 前言 ##

入门时，我们所有Javascript代码都写在一个文件里面，只要加载这一个文件就够了。慢慢的，代码越来越多，一个文件不够了，必须分成多个文件，依次加载。就形成了下面的结构模式

```
  　<script src="aa.js"></script>
    <script src="bb.js"></script>
    <script src="cc.js"></script>
    <script src="dd.js"></script>
    <script src="ee.js"></script>
    <script src="ff.js"></script>
    ……
```

这样的写法有很大的缺点。首先，加载的时候，浏览器会停止网页渲染，加载文件越多，网页失去响应的时间就会越长；其次，由于js文件之间存在依赖关系，因此必须严格保证加载顺序（比如上例的aa.js要在bb.js的前面），依赖性最大的模块一定要放到最后加载，当依赖关系很复杂的时候，代码的编写和维护都会变得困难。

**require.js的诞生，就是为了解决这两个问题：**

> 1）实现js文件的异步加载，避免网页失去响应；
> 
> 2）管理模块之间的依赖性，便于代码的编写和维护。

## require.js的加载很简介 ##

使用require.js的第一步，是先去官方网站下载最新版本。
下载后，假定把它放在js子目录下面，就可以加载了。

```
	<script src="js/require.js"></script>
```

加载这个文件，也可能造成网页失去响应。解决办法有两个，一个是把它放在网页底部加载，另一个是写成下面这样：

```
	<script src="js/require.js" defer async="true" ></script>
```

async属性表明这个文件需要异步加载，避免网页失去响应。IE不支持这个属性，只支持defer，所以把defer也写上。

加载require.js以后，下一步就要加载我们自己的代码了。假定我们自己的代码文件是main.js，也放在js目录下面。那么，只需要写成下面这样就行了：

```
	<script src="js/require.js" data-main="js/main"></script>
```

data-main属性的作用是，指定网页程序的主模块。在上例中，就是js目录下面的main.js，这个文件会第一个被require.js加载。由于require.js默认的文件后缀名是js，所以可以把main.js简写成main。

main.js , 是整个网页的入口代码 , 所有代码都从这儿开始运行。

如果我们的代码不依赖任何其他模块，那么可以直接写入javascript代码。

```
	// main.js
	alert("加载成功！");
```

但这样的话，就没必要使用require.js了。真正常见的情况是依赖于其他模块，这时就要使用AMD规范定义的的require()函数。

```
	 // main.js
	require(['moduleA', 'moduleB', 'moduleC'], function (moduleA, moduleB, moduleC){
　　　　// some code here
	});
```
require()函数接受两个参数。第一个参数是一个数组，表示所依赖的模块，上例就是['moduleA', 'moduleB', 'moduleC']，即依赖这三个模块；第二个参数是一个回调函数，当前面指定的模块都加载成功后，它将被调用。加载的模块会以参数形式传入该函数，从而在回调函数内部就可以使用这些模块。

require()异步加载moduleA，moduleB和moduleC，浏览器不会失去响应；它指定的回调函数，只有前面的模块都加载成功后，才会运行，解决了依赖性的问题。

下面，我们看一个实际的例子。
如果依赖jquery、underscore和backbone这三个模块，main.js就可以这样写：

```
	require(['jquery', 'underscore', 'backbone'], function ($, _, Backbone){
　　　　// some code here
　　});
```
require.js会先加载jQuery、underscore和backbone，然后再运行回调函数。代码就写在回调函数中。


使用require.config()方法，我们可以对模块的加载行为进行自定义。require.config()就写在 main.js的头部。参数就是一个对象，这个对象的paths属性指定各个模块的加载路径。

```
	require.config({
　　　　paths: {
　　　　　　"jquery": "jquery.min",
　　　　　　"underscore": "underscore.min",
　　　　　　"backbone": "backbone.min"
　　　　}
	});
```

上面的代码给出了三个模块的文件名，路径默认与main.js在同一个目录（js子目录）。如果这些模块在其他目录，比如js/lib目录，则有两种写法。一种是逐一指定路径。

```
　　require.config({
　　　　paths: {
　　　　　　"jquery": "lib/jquery.min",
　　　　　　"underscore": "lib/underscore.min",
　　　　　　"backbone": "lib/backbone.min"
　　　　}
　　});
```

另一种则是直接改变基目录（baseUrl）。

```
　　require.config({
　　　　baseUrl: "js/lib",
　　　　paths: {
　　　　　　"jquery": "jquery.min",
　　　　　　"underscore": "underscore.min",
　　　　　　"backbone": "backbone.min"
　　　　}
　　});
```

如果某个模块在另一台主机上，也可以直接指定它的网址，比如：

```
　　require.config({
　　　　paths: {
　　　　　　"jquery": "https://ajax.googleapis.com/ajax/libs/jquery/1.7.2/jquery.min"
　　　　}
　　});
```

require.js要求，每个模块是一个单独的js文件。这样的话，如果加载多个模块，就会发出多次HTTP请求，会影响网页的加载速度。因此，require.js提供了一个优化工具，当模块部署完毕以后，可以用这个工具将多个模块合并在一个文件中，减少HTTP请求数。


# ** 对上述概念的详细解释 ** #


## 1.基本API  ##

require会定义三个变量：define,require,requirejs，其中require === requirejs，一般使用require更简短

> define 从名字就可以看出这个api是用来定义一个模块,RequireJS要求每个模块放在一个单独的文件里。
> 
> require 加载依赖模块，并执行加载完后的回调函数


### 1.1 define方法：定义模块  ###

#### （1）独立模块  ####

如果被定义的模块是一个独立模块，不需要依赖任何其他模块，可以直接用define方法生成。

```
define({
    method1: function() {},
    method2: function() {},
});
```
上面代码生成了一个拥有method1、method2两个方法的模块。

把对象写成一个函数，该函数的返回值就是输出的模块。
```
define(function () {
	return {
	    method1: function() {},
		method2: function() {},
    };
});
```

后一种写法的自由度更高一点，可以在函数体内写一些模块初始化代码。

值得指出的是，define定义的模块可以返回任何值，不限于对象。

#### （2）非独立模块  ####

如果被定义的模块需要依赖其他模块，则define方法必须采用下面的格式。

```
define(['module1', 'module2'], function(m1, m2) {
   ...
});
```

**define方法的第一个参数是一个数组，它的成员是当前模块所依赖的模块。**

比如，[‘module1’, ‘module2’]表示我们定义的这个新模块依赖于module1模块和module2模块，只有先加载这两个模块，新模块才能正常运行。一般情况下，module1模块和module2模块指的是，当前目录下的module1.js文件和module2.js文件，等同于写成[’./module1’, ‘./module2’]。

**define方法的第二个参数是一个函数，当前面数组的所有成员加载成功后，它将被调用。**

它的参数与数组的成员一一对应，比如function(m1, m2)就表示，这个函数的第一个参数m1对应module1模块，第二个参数m2对应module2模块。这个函数必须返回一个对象，供其他模块调用。

```
define(['module1', 'module2'], function(m1, m2) {
    return {
        method: function() {
            m1.methodA();
            m2.methodB();
        }
    };

});
```

上面代码表示新模块返回一个对象，该对象的method方法就是外部调用的接口，menthod方法内部调用了m1模块的methodA方法和m2模块的methodB方法。

需要注意的是，回调函数必须返回一个对象，这个对象就是你定义的模块。

如果依赖的模块很多，参数与模块一一对应的写法非常麻烦。

```
define(
    [ 'dep1', 'dep2', 'dep3', 'dep4', 'dep5', 'dep6', 'dep7', 'dep8'],
    function(dep1,   dep2,   dep3,   dep4,   dep5,   dep6,   dep7,   dep8){
        ...
    }
);
```

为了避免像上面代码那样繁琐的写法，RequireJS提供一种更简单的写法。

```
define(
    function (require) {
        var dep1 = require('dep1'),
            dep2 = require('dep2'),
            dep3 = require('dep3'),
            dep4 = require('dep4'),
            dep5 = require('dep5'),
            dep6 = require('dep6'),
            dep7 = require('dep7'),
            dep8 = require('dep8');
            ...
    }
});
```

下面是一个define实际运用的例子。

```
define(['math', 'graph'], 
    function ( math, graph ) {
		return {
            plot: function(x, y){
                return graph.drawPie(math.randomGrid(x,y));
            }
        }
    };
);
```

上面代码定义的模块依赖math和graph两个库，然后返回一个具有plot接口的对象。

另一个实际的例子是，通过判断浏览器是否为IE，而选择加载zepto或jQuery。

```
define(('__proto__' in {} ? ['zepto'] : ['jquery']), function($) {
    return $;
});
```

上面代码定义了一个中间模块，该模块先判断浏览器是否支持__proto__属性（除了IE，其他浏览器都支持），如果返回true，就加载zepto库，否则加载jQuery库。


### 1.2 require方法：调用模块 ###

require方法用于调用模块。它的参数与define方法类似

```
require(['a', 'b'], function ( a, b ) {
        b.doSomething();
});
```
上面方法表示加载a和b两个模块，当这两个模块都加载成功后，执行一个回调函数。该回调函数就用来完成具体的任务。

require方法的第一个参数，是一个表示依赖关系的数组。这个数组可以写得很灵活，请看下面的例子。

```
require( [ window.JSON ? undefined : 'util/json2' ], function ( JSON ) {
  JSON = JSON || window.JSON;

  console.log( JSON.parse( '{ "JSON" : "HERE" }' ) );
});
```

上面代码加载JSON模块时，首先判断浏览器是否原生支持JSON对象。如果是的，则将undefined传入回调函数，否则加载util目录下的json2模块。

require方法也可以用在define方法内部。(define中介绍多模块加载提到过)

```
define(function (require) {
   var otherModule = require('otherModule');
});
```

下面的例子显示了如何动态加载模块

```
define(function ( require ) {
    var isReady = false, foobar;

    require(['foo', 'bar'], function (foo, bar) {
        isReady = true;
        foobar = foo() + bar();
    });
 
    return {
        isReady: isReady,
        foobar: foobar
    };
});
```

上面代码所定义的模块，内部加载了foo和bar两个模块，在没有加载完成前，isReady属性值为false，加载完成后就变成了true。因此，可以根据isReady属性的值，决定下一步的动作。

下面的例子是模块的输出结果是一个promise对象。

```
define(['lib/Deferred'], function( Deferred ){
    var defer = new Deferred(); 
    require(['lib/templates/?index.html','lib/data/?stats'],
        function( template, data ){
            defer.resolve({ template: template, data:data });
        }
    );
    return defer.promise();
});
```

上面代码的define方法返回一个promise对象，可以在该对象的then方法，指定下一步的动作。

如果服务器端采用JSONP模式，则可以直接在require中调用，方法是指定JSONP的callback参数为define。

```
require( [ 
    "http://someapi.com/foo?callback=define"
], function (data) {
    console.log(data);
});
```

require方法允许添加第三个参数，即错误处理的回调函数。

```
require(
    [ "backbone" ], 
    function ( Backbone ) {
        return Backbone.View.extend({ /* ... */ });
    }, 
    function (err) {
		// ...
    }
);
```

require对象还允许指定一个全局性的Error事件的监听函数。所有没有被上面的方法捕获的错误，都会被触发这个监听函数。

```
requirejs.onError = function (err) {
    // ...
};
```

## 3 配置config方法 ##
require方法本身也是一个对象，它带有一个config方法，用来配置require.js运行参数。config方法接受一个对象作为参数。

```
//这里的jquery模块定义多个位置(2个位置) 如果第一个位置加载失败，则加载第二个位置
require.config({
    paths: {
        jquery: [
            '//cdnjs.cloudflare.com/ajax/libs/jquery/2.0.0/jquery.min.js',
            'lib/jquery'
        ]
    }
});
```

config方法的参数对象有以下主要成员：

（**1）paths**

paths参数指定各个模块的位置。这个位置可以是同一个服务器上的相对位置，也可以是外部网址。**可以为每个模块定义多个位置**，如果第一个位置加载失败，则加载第二个位置，上面的示例就表示如果CDN加载失败，则加载服务器上的备用脚本。需要注意的是，指定本地文件路径时，可以省略文件最后的js后缀名。

```
require(["jquery"], function($) {
    // ...
});
```

上面代码加载jquery模块，因为jquery的路径已经在paths参数中定义了，所以就会到事先设定的位置下载。

**（2）baseUrl**

baseUrl参数指定本地模块位置的基准目录，即本地模块的路径是相对于哪个目录的。该属性通常由require.js加载时的data-main属性指定。

**（3）shim**

```
require.config({
    paths: {
        "backbone": "vendor/backbone",
        "underscore": "vendor/underscore"
    },
    shim: {
        "backbone": {
            deps: [ "underscore" ],
            exports: "Backbone"
        },
        "underscore": {
            exports: "_"
        }
    }
});
```
上面代码中的backbone和underscore就是非AMD规范的库。shim指定它们的依赖关系（backbone依赖于underscore），以及输出符号（backbone为“Backbone”，underscore为“_”）。

有些库不是AMD兼容的，这时就需要指定shim属性的值。shim可以理解成“垫片”，用来帮助require.js加载非AMD规范的库。

## 加载非规范的模块 ##

理论上，require.js加载的模块，必须是按照AMD规范、用define()函数定义的模块。但是实际上，虽然已经有一部分流行的函数库（比如jQuery）符合AMD规范，更多的库并不符合。

这样的模块在用require()加载之前，要先用require.config()方法，定义它们的一些特征。

举例来说，md1和md2这两个库，都没有采用AMD规范编写。如果要加载它们的话，必须先定义它们的特征。

```
　　require.config({
　　　　shim: {
　　　　　　'md1':{
　　　　　　　　exports: 'md.aa'
　　　　　　},
　　　　　　'md2': {
　　　　　　　　deps: ['jquery', 'myQuery'],
　　　　　　　　exports: 'md.bb'
　　　　　　}
　　　　}
　　});
```
require.config()接受一个配置对象，这个对象除了有前面说过的paths属性之外，还有一个shim属性，专门用来配置不兼容的模块。具体来说，每个模块要定义:

> （1）exports值（输出的变量名），表明这个模块外部调用时的名称；
> 
> （2）deps数组，表明该模块的依赖性。


比如，jQuery的插件可以这样定义：

```
　　shim: {
　　　　'jquery.scroll': {
　　　　　　deps: ['jquery'],
　　　　　　exports: 'jQuery.fn.scroll'
　　　　}
　　}
```

## 插件 ##
RequireJS允许使用插件，加载各种格式的数据。完整的插件清单可以查看 [**require插件**](https://github.com/requirejs/requirejs/wiki/Plugins "插件")。

下面是插件的例子。

text和image插件，则是允许require.js加载文本和图片文件。

```
	define([
	   'jquery',
　　　　'text!review.txt',
　　　　'image!cat.jpg'
　　　　],
　　　　function(review,cat){
　　　　　　console.log(review);
　　　　　　document.body.appendChild(cat);
　　　　}
　　);
```

## 工具 r.js  ##
 **RequireJS提供一个基于node.js的命令行工具r.js，用来压缩多个js文件。它的主要作用是将多个模块文件压缩合并成一个脚本文件，以减少网页的HTTP请求数。**

第一步是安装r.js（已经安装node.js）

```
npm install -g requirejs
```

然后，使用的时候，直接在命令行键入以下格式的命令。

```
node r.js -o <arguments>
```

<argument>表示命令运行时，所需要的一系列参数，比如像下面这样：

```
node r.js -o baseUrl=. name=main out=main-built.js
```

除了直接在命令行提供参数设置，也可以将参数写入一个文件，假定文件名为build.js。

```
({
    baseUrl: ".",
    name: "main",
    out: "main-built.js"
})

```

然后，在命令行下用r.js运行这个参数文件，就OK了，不需要其他步骤了。

```
node r.js -o build.js
```


下面是一个参数文件的范例，假定位置就在根目录下，文件名为build.js。

```
({
    appDir: './',
    baseUrl: './js',
    dir: './dist',
    modules: [
        {
            name: 'main'
        }
    ],
    fileExclusionRegExp: /^(r|build)\.js$/,
    optimizeCss: 'standard',
    removeCombined: true,
    paths: {
        jquery: 'lib/jquery',
        underscore: 'lib/underscore',
        backbone: 'lib/backbone/backbone',
        backboneLocalstorage: 'lib/backbone/backbone.localStorage',
        text: 'lib/require/text'
    },
    shim: {
        underscore: {
            exports: '_'
        },
        backbone: {
            deps: [
                'underscore',
                'jquery'
            ],
            exports: 'Backbone'
        },
        backboneLocalstorage: {
            deps: ['backbone'],
            exports: 'Store'
        }
    }
})
```

上面代码将多个模块压缩合并成一个main.js。

参数文件的主要成员解释如下：

- **appDir**：项目目录，相对于参数文件的位置。

- **baseUrl**：js文件的位置。

- **dir**：输出目录。

- **modules**：一个包含对象的数组，每个对象就是一个要被优化的模块。

- **fileExclusionRegExp**：凡是匹配这个正则表达式的文件名，都不会被拷贝到输出目录。

- **optimizeCss**: 自动压缩CSS文件，可取的值包括“none”, “standard”, “standard.keepLines”, “standard.keepComments”, “standard.keepComments.keepLines”。

- **removeCombined**：如果为true，合并后的原文件将不保留在输出目录中。

- **paths**：各个模块的相对路径，可以省略js后缀名。

- **shim**：配置依赖性关系。如果某一个模块不是AMD模式定义的，就可以用shim属性指定模块的依赖性关系和输出值。

- **generateSourceMaps**：是否要生成source map文件。

更详细的解释可以参考[官方文档](https://github.com/jrburke/r.js/blob/master/build/example.build.js)。

运行优化命令后，可以前往dist目录查看优化后的文件。

下面是另一个build.js的例子。

```
({
    mainConfigFile : "js/main.js",
    baseUrl: "js",
    removeCombined: true,
    findNestedDependencies: true,
    dir: "dist",
    modules: [
        {
            name: "main",
            exclude: [
                "infrastructure"
            ]
        },
        {
            name: "infrastructure"
        }
    ]
})
```