---
title: Gulp 任务管理工具
date: 2016-08-03
tags : gulp
categories : tools
---

# 1 . 安装和入门 #

** 全局安装 gulp：**

```
$ npm install --global gulp    

或者简写

npm install -g gulp
```

** 作为项目的开发依赖（devDependencies）安装：**

```
$ npm install --save-dev gulp
```

**  在项目根目录下创建一个名为 gulpfile.js 的文件：**

项目根目录中的gulpfile.js，是Gulp的配置文件。下面就是一个典型的gulpfile.js文件。

```
var gulp = require('gulp');
var uglify = require('gulp-uglify');

gulp.task('minify', function () {
  gulp.src('js/app.js')
    .pipe(uglify())
    .pipe(gulp.dest('build'))
});
```

上面代码中，gulpfile.js加载gulp和gulp-uglify模块之后，使用gulp模块的task方法指定任务minify。

task方法有两个参数，第一个是任务名，第二个是任务函数。

在任务函数中，使用gulp模块的src方法，指定所要处理的文件，然后使用pipe方法，将上一步的输出转为当前的输入，进行链式处理。

** task方法的回调函数使用了两次pipe方法，也就是说做了两种处理。**

第一种处理是使用gulp-uglify模块，压缩源码；

第二种处理是使用gulp模块的dest方法，将上一步的输出写入本地文件夹，这里是build.js（代码中省略了后缀名js）。

执行minify任务时，就在项目目录中执行下面命令就可以了。

**4. 运行 gulp：**

```
$ gulp minify  

// 注释：： 
$ gulp <taskName>
```

> 默认的名为 default 的任务（task）将会被运行，如果没有taskName这默认执行叫default 的任务。
> 
> 想要单独执行特定的任务（task），请输入 gulp [task] [othertask]。

从上面的例子中可以看到，gulp充分使用了“管道”思想，就是一个数据流（stream）：src方法读入文件产生数据流，dest方法将数据流写入文件，中间是一些中间步骤，每一步都对数据流进行一些处理。

# 2 . gulp模块的方法 #

## src() ##

gulp模块的src方法，用于产生数据流。它的参数表示所要处理的文件，这些指定的文件会转换成数据流。参数的写法一般有以下几种形式。

> js/app.js：指定确切的文件名。
> 
> js/*.js：某个目录所有后缀名为js的文件。
> 
> js/**/*.js：某个目录及其所有子目录中的所有后缀名为js的文件。
> 
> !js/app.js：除了js/app.js以外的所有文件。
> 
> *.+(js	css)：匹配项目根目录下，所有后缀名为js或css的文件。

src方法的参数还可以是一个数组，用来指定多个成员。

```
gulp.src(['js/**/*.js', '!js/**/*.min.js'])
```

## dest() ##

dest方法将管道的输出写入文件，同时将这些输出继续输出，所以可以依次调用多次dest方法，将输出写入多个目录。如果有目录不存在，将会被新建。

```
gulp.src('./client/templates/*.jade')
  .pipe(jade())
  .pipe(gulp.dest('./build/templates'))
  .pipe(minify())
  .pipe(gulp.dest('./build/minified_templates'));
```

dest方法还可以接受第二个参数，表示配置对象。

```
gulp.dest('build', {
  cwd: './app',
  mode: '0644'
})
```

配置对象有两个字段。cwd字段指定写入路径的基准目录，默认是当前目录；mode字段指定写入文件的权限，默认是0777。

## task() ##

task方法用于定义具体的任务。它的第一个参数是任务名，第二个参数是任务函数。下面是一个非常简单的任务函数。

```
gulp.task('greet', function () {
   console.log('Hello world!');
});
```

task方法还可以指定按顺序运行的一组任务。

```
gulp.task('build', ['css', 'js', 'imgs']);
```

上面代码先指定build任务，它由css、js、imgs三个任务所组成，task方法会并发执行这三个任务。注意，由于每个任务都是异步调用，所以没有办法保证js任务的开始运行的时间，正是css任务运行结束。

如果希望各个任务严格按次序运行，可以把前一个任务写成后一个任务的依赖模块。

```
gulp.task('css', ['greet'], function () {
   // Deal with CSS here
});
```

上面代码表明，css任务依赖greet任务，所以css一定会在greet运行完成后再运行。

task方法的回调函数，还可以接受一个函数作为参数，这对执行异步任务非常有用。

```
// 执行shell命令
var exec = require('child_process').exec;
gulp.task('jekyll', function(cb) {
  // build Jekyll
  exec('jekyll build', function(err) {
    if (err) return cb(err); // return error
    cb(); // finished task
  });
});
```

如果一个任务的名字为default，就表明它是“默认任务”，在命令行直接输入gulp命令，就会运行该任务。

```
gulp.task('default', function () {
  // Your default task
});

// 或者

gulp.task('default', ['styles', 'jshint', 'watch']);
```

执行的时候，直接使用gulp，就会运行styles、jshint、watch三个任务。

## watch() ##

watch方法用于指定需要监视的文件。一旦这些文件发生变动，就运行指定任务。

```
gulp.task('watch', function () {
   gulp.watch('templates/*.tmpl.html', ['build']);
});
```

上面代码指定，一旦templates目录中的模板文件发生变化，就运行build任务。

watch方法也可以用回调函数，代替指定的任务。

```
gulp.watch('templates/*.tmpl.html', function (event) {
   console.log('Event type: ' + event.type);
   console.log('Event path: ' + event.path);
});

gulp.watch('js/**/*.js', function(event) {
  console.log('File ' + event.path + ' was ' + event.type + ', running tasks...');
});

```

另一种写法是watch方法所监控的文件发生变化时（修改、增加、删除文件），会触发change事件。可以对change事件指定回调函数。

```
var watcher = gulp.watch('js/**/*.js', ['uglify','reload']);
watcher.on('change', function(event) {
  console.log('File ' + event.path + ' was ' + event.type + ', running tasks...');
});
```

除了change事件，watch方法还可能触发以下事件。

> end：回调函数运行完毕时触发。
> 
> error：发生错误时触发。
> 
> ready：当开始监听文件时触发。
> 
> nomatch：没有匹配的监听文件时触发。

watcher对象还包含其他一些方法。

> watcher.end()：停止watcher对象，不会再调用任务或回调函数。
> 
> watcher.files()：返回watcher对象监视的文件。
> 
> watcher.add(glob)：增加所要监视的文件，它还可以附件第二个参数，表示回调函数。
> 
> watcher.remove(filepath)：从watcher对象中移走一个监视的文件

# 3 . 模块插件 #

**和grunt有相同的地方，就是gulp也有相似的插件对文件进行处理，我们需要哪个插件就将其引入**


** 这里是我之前写的测试demo , 这里都是当时的测试代码！记录下 **

```
var gulp = require('gulp'),
    jshint = require('gulp-jshint'),
    uglify = require('gulp-uglify'),
    concat = require('gulp-concat'),
    minifycss = require('gulp-minify-css'),
    imagemin = require('gulp-imagemin'),
    cache = require('gulp-cache'),
    rename = require('gulp-rename'),
    notify = require('gulp-notify'),
    minifyHtml = require("gulp-minify-html");
    del = require('del');


gulp.task('cleanFile', function(cb) {
    del(['minJS/*', 'minCss/*'], cb)
});


gulp.task('minijs',function () {
   gulp.src('js/*.js')
      .pipe(rename({suffix: '.min'}))
      .pipe(uglify())
      .pipe(gulp.dest('minJs'))
      .pipe(notify({ message: 'js任务完成' }));
});

gulp.task('minicss', function () { 
    gulp.src(['css/*.css'])
        .pipe(rename({suffix: '.min'}))
        .pipe(minifycss())
        .pipe(gulp.dest('minCss'))
        .pipe(notify({ message: 'CSS任务完成' }));
});

gulp.task('hebin',function () {
   gulp.src(['js/*.js'])
   	  .pipe(concat('main.js'))
   	  .pipe(rename({suffix: '.min'}))
      .pipe(uglify())
      .pipe(gulp.dest('minJs'))
      .pipe(notify({ message: 'js任务完成' }));
});



//好奇怪！！！！！对这里的多个文件写法
gulp.task('accc',function () {
   gulp.src(['js/a.js'],['js/c.js'])
      .pipe(concat('ac.min.js'))
      .pipe(uglify())
      .pipe(gulp.dest('minJs'));
});


gulp.task('haha', function () {
    gulp.src(['css/*.css'])
        .pipe(rename({suffix: '.min'}))
        .pipe(minifycss())
        .pipe(gulp.dest('haha'))
        .pipe(notify({ message: 'CSS任务完成' }));
    gulp.src('js/*.js')
      .pipe(rename({suffix: '.min'}))
      .pipe(uglify())
      .pipe(gulp.dest('haha'))
      .pipe(notify({ message: 'js任务完成' }));
});

gulp.task('minihtml', function () {
    gulp.src('*.html') // 要压缩的html文件
    .pipe(minifyHtml()) //压缩
    .pipe(gulp.dest('html'));
});



gulp.task('imagemin',function(){
    // 1. 找到图片
    gulp.src('images/*.*')
    // 2. 压缩图片缓存 只有变动的才被压缩
    .pipe(cache(imagemin({ optimizationLevel: 3, progressive: true, interlaced: true })))
    // 3. 另存图片
    .pipe(gulp.dest('minImg'))
    .pipe(notify({ message: 'Images task complete' }));

});


// 在命令行使用 gulp auto 启动此任务
gulp.task('auto', function () {
    // 监听文件修改，当文件被修改则执行 images 任务
    gulp.watch('images/*.*)', ['imagemin']);
});

// 使用 gulp.task('default') 定义默认任务
// 在命令行使用 gulp 启动 images 任务和 auto 任务
gulp.task('default', ['imagemin', 'auto']);

// gulp.task('default',['cleanFile'],function () {
//     gulp.start('minijs','minicss');
// });
```

## gulp-load-plugins模块 ##

一般情况下，gulpfile.js中的模块需要一个个加载。

```
var gulp = require('gulp'),
    jshint = require('gulp-jshint'),
    uglify = require('gulp-uglify'),
    concat = require('gulp-concat');

gulp.task('js', function () {
   return gulp.src('js/*.js')
      .pipe(jshint())
      .pipe(jshint.reporter('default'))
      .pipe(uglify())
      .pipe(concat('app.js'))
      .pipe(gulp.dest('build'));
});
```

上面代码中，除了gulp模块以外，还加载其他很多个模块。

这种一一加载的写法，** 比较麻烦。 **

** 使用gulp-load-plugins模块，可以加载package.json文件中所有的gulp模块。上面的代码用gulp-load-plugins模块改写，就是下面这样。 **

```
var gulp = require('gulp'),
    gulpLoadPlugins = require('gulp-load-plugins'),
    plugins = gulpLoadPlugins();

gulp.task('js', function () {
   return gulp.src('js/*.js')
      .pipe(plugins.jshint())
      .pipe(plugins.jshint.reporter('default'))
      .pipe(plugins.uglify())
      .pipe(plugins.concat('app.js'))
      .pipe(gulp.dest('build'));
});
```
上面代码假设package.json文件包含以下内容。

```
{
   "devDependencies": {
      "gulp-concat": "~2.2.0",
      "gulp-uglify": "~0.2.1",
      "gulp-jshint": "~1.5.1",
      "gulp": "~3.5.6"
   }
}
```

## gulp-livereload模块  ##

gulp-livereload模块用于自动刷新浏览器，反映出源码的最新变化。它除了模块以外，还需要在浏览器中安装插件，用来配合源码变化。

```
var gulp = require('gulp'),
    less = require('gulp-less'),
    livereload = require('gulp-livereload'),
    watch = require('gulp-watch');

gulp.task('less', function() {
   gulp.src('less/*.less')
      .pipe(watch())
      .pipe(less())
      .pipe(gulp.dest('css'))
      .pipe(livereload());
});
```

上面代码监视less文件，一旦编译完成，就自动刷新浏览器。

