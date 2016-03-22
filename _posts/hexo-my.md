---
title: 自己在搭建Hexo中所遇到的问题
tags : hexo
categories : tools
---
Hexo 是一个快速、简洁且高效的博客框架。Hexo 使用 Markdown（或其他渲染引擎）解析文章，在几秒内，即可利用靓丽的主题生成静态网页。[hexo.io](https://hexo.io/)

## 参考文档

* https://hexo.io/zh-cn/docs/

## 准备工作

> 安装GIT

> 安装Node.JS


## 安装Hexo

配置好<code>GitHub</code>目录后，双击桌面上的<code>Git Shell</code>，输入<code>npm</code>命令即可安装 

``` 
npm install hexo-cli -g
npm install hexo --save

#如果命令无法运行，可以尝试更换taobao的npm源
npm install -g cnpm --registry=https://registry.npm.taobao.org

```
在命令行中输入：<code> hexo -v </code> 你可以看到Hexo是不是已经安装好了。

## 创建Hexo文件夹

安装完成后，根据自己喜好建立目录（如<code>E:\kuaipan\GitHub\hexo</code>），进入<code>Git Shell</code>切换到该路径下<code>E:\kuaipan\GitHub\hexo</code>执行以下指令

``` bash
exo init

#安装 Hexo 完成后，请执行下列命令，Hexo 将会在指定文件夹中新建所需要的文件。
$ hexo init <folder>
$ cd <folder>
$ npm install

#新建完成后，指定文件夹的目录如下
.
├── _config.yml
├── package.json
├── scaffolds
├── scripts
├── source
|      ├── _drafts
|      └── _posts
└── themes
```

## 安装Hexo插件

``` bash
npm install hexo-generator-index --save
npm install hexo-generator-archive --save
npm install hexo-generator-category --save
npm install hexo-generator-tag --save
npm install hexo-server --save
npm install hexo-deployer-git --save
npm install hexo-deployer-heroku --save
npm install hexo-deployer-rsync --save
npm install hexo-deployer-openshift --save
npm install hexo-renderer-marked@0.2 --save
npm install hexo-renderer-stylus@0.2 --save
npm install hexo-generator-feed@1 --save
npm install hexo-generator-sitemap@1 --save

```
## 本地查看效果

``` 
hexo server

```
## 本地查看效果
``` bash

hexo n #生成文章，或者source\_posts手动编辑
hexo s #本地发布预览效果
hexo g #生成public静态文件

```

## 部署问题

>  先hexo g再执行hexo d布署，也可使直接用hexo d -g
>  hexo 更新到3.0之后，deploy的type 的github需要改成git
>  在执行 hexo deploy 后,出现 error deployer not found:git 的错误处理
>  输入代码 ：  npm install hexo-deployer-git --save   即可


## 主题选择

```
git clone https://github.com/litten/hexo-theme-yilia.git themes/yilia
```

## 主题遇到问题

- 图片使用本地路径：在`hexo/source`目录下新建一个`images`文件夹，将图片放入该文件夹下，插入图片时链接即为`/images/图片名称`
- 添加“关于自己” 在`source`先新建文件夹about，然后添加编辑`source/about/index.md` ， 编辑themes/yilia/_config.yml，添加如下：
  ```
  menu:
  关于: /about
  ```
- 给主题添加自己写的样式，在主题对应的 `css`目录下新建自己的 `_my.styl` 然后在 `style.styl` 添加

  ```
  @import "_my"
  ```
  在自己的  `_my.styl` css中注意图片路劲
  ```
	#container .left-col .overlay{
		background:url('/img/bg.jpg');
	}
  ```
- 我的hexo能成功部署 且大开链接也有效果 只是每次在hexo s 的时候会有瑕疵，有如下错误：
  ```
  	➜  blog  hexo s
	INFO  Start processing
	ERROR Process failed: layout/.DS_Store
	TypeError: Cannot read property 'compile' of undefined
	    at View._precompile (/Users/idisfkj/blog/node_modules/hexo/lib/theme/view.js:104:22)
	    at View (/Users/idisfkj/blog/node_modules/hexo/lib/theme/view.js:13:8)
	    at new Theme._View.View (/Users/idisfkj/blog/node_modules/hexo/lib/theme/index.js:37:10)
	    at Theme.setView (/Users/idisfkj/blog/node_modules/hexo/lib/theme/index.js:71:20)
	    at /Users/idisfkj/blog/node_modules/hexo/lib/theme/processors/view.js:14:14
	    at tryCatcher (/Users/idisfkj/blog/node_modules/bluebird/js/release/util.js:16:23)
	    at Promise._settlePromiseFromHandler (/Users/idisfkj/blog/node_modules/bluebird/js/release/promise.js:503:31)
	    at Promise._settlePromise (/Users/idisfkj/blog/node_modules/bluebird/js/release/promise.js:560:18)
	    at Promise._settlePromise0 (/Users/idisfkj/blog/node_modules/bluebird/js/release/promise.js:605:10)
	    at Promise._settlePromises (/Users/idisfkj/blog/node_modules/bluebird/js/release/promise.js:684:18)
	    at Promise._fulfill (/Users/idisfkj/blog/node_modules/bluebird/js/release/promise.js:629:18)
	    at Promise._resolveCallback (/Users/idisfkj/blog/node_modules/bluebird/js/release/promise.js:424:57)
	    at Promise._settlePromiseFromHandler (/Users/idisfkj/blog/node_modules/bluebird/js/release/promise.js:515:17)
	    at Promise._settlePromise (/Users/idisfkj/blog/node_modules/bluebird/js/release/promise.js:560:18)
	    at Promise._settlePromise0 (/Users/idisfkj/blog/node_modules/bluebird/js/release/promise.js:605:10)
	    at Promise._settlePromises (/Users/idisfkj/blog/node_modules/bluebird/js/release/promise.js:684:18)
	    at Promise._fulfill (/Users/idisfkj/blog/node_modules/bluebird/js/release/promise.js:629:18)
	    at /Users/idisfkj/blog/node_modules/bluebird/js/release/nodeback.js:42:21
	    at /Users/idisfkj/blog/node_modules/graceful-fs/graceful-fs.js:78:16
	    at tryToString (fs.js:414:3)
	    at FSReqWrap.readFileAfterClose [as oncomplete] (fs.js:401:12)
	ERROR Process failed: layout/_partial/.DS_Store
	TypeError: Cannot read property 'compile' of undefined
	    at View._precompile (/Users/idisfkj/blog/node_modules/hexo/lib/theme/view.js:104:22)
	    at View (/Users/idisfkj/blog/node_modules/hexo/lib/theme/view.js:13:8)
	    at new Theme._View.View (/Users/idisfkj/blog/node_modules/hexo/lib/theme/index.js:37:10)
	    at Theme.setView (/Users/idisfkj/blog/node_modules/hexo/lib/theme/index.js:71:20)
	    at /Users/idisfkj/blog/node_modules/hexo/lib/theme/processors/view.js:14:14
	    at tryCatcher (/Users/idisfkj/blog/node_modules/bluebird/js/release/util.js:16:23)
	    at Promise._settlePromiseFromHandler (/Users/idisfkj/blog/node_modules/bluebird/js/release/promise.js:503:31)
	    at Promise._settlePromise (/Users/idisfkj/blog/node_modules/bluebird/js/release/promise.js:560:18)
	    at Promise._settlePromise0 (/Users/idisfkj/blog/node_modules/bluebird/js/release/promise.js:605:10)
	    at Promise._settlePromises (/Users/idisfkj/blog/node_modules/bluebird/js/release/promise.js:684:18)
	    at Promise._fulfill (/Users/idisfkj/blog/node_modules/bluebird/js/release/promise.js:629:18)
	    at Promise._resolveCallback (/Users/idisfkj/blog/node_modules/bluebird/js/release/promise.js:424:57)
	    at Promise._settlePromiseFromHandler (/Users/idisfkj/blog/node_modules/bluebird/js/release/promise.js:515:17)
	    at Promise._settlePromise (/Users/idisfkj/blog/node_modules/bluebird/js/release/promise.js:560:18)
	    at Promise._settlePromise0 (/Users/idisfkj/blog/node_modules/bluebird/js/release/promise.js:605:10)
	    at Promise._settlePromises (/Users/idisfkj/blog/node_modules/bluebird/js/release/promise.js:684:18)
	    at Promise._fulfill (/Users/idisfkj/blog/node_modules/bluebird/js/release/promise.js:629:18)
	    at /Users/idisfkj/blog/node_modules/bluebird/js/release/nodeback.js:42:21
	    at /Users/idisfkj/blog/node_modules/graceful-fs/graceful-fs.js:78:16
	    at tryToString (fs.js:414:3)
	    at FSReqWrap.readFileAfterClose [as oncomplete] (fs.js:401:12)
	INFO  Hexo is running at http://localhost:4000/. Press Ctrl+C to stop.

	```
    解决方法：cd 进到你使用的`theme`对应的目录，再进到`layout/`和`layout/_partial/`下，分别执行`rm .DS_Store`