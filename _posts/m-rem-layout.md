---
title: rem布局移动端自适应页面
date: 2016-10-14
tags : layout
categories : mobileWeb
---
## 前言 ##

做了很多移动端web项目，在网页在适应上走了很多弯曲的道路，刚开始采用的是[大众点评](https://m.dianping.com/)似的布局，百分比布局当然也用到了所谓的**弹性布局**`flex`，有天朋友给我推荐 [**rem布局**](https://github.com/jieyou/rem_layout)，也慢慢开启的对rem的探索！

首先我们来谈谈什么是rem

## rem简介 ##

**rem**  是CSS3新引进来的一个度量单位，大家心里肯定会觉得心灰意冷呀，担心浏览器的支持情况。其实大家不用害怕，你可能会惊讶，支持的浏览器还是蛮多的，比如：Mozilla Firefox 3.6+、Apple Safari 5+、Google Chrome、IE9+和Opera11+。只是可怜的IE6-8无法，你们就把他们当透明了吧。

**当使用 rem 单位，他们转化为像素大小取决于页根元素的字体大小，即 html 元素的字体大小。 根元素字体大小乘以你 rem 值。**

例如，根元素的字体大小 16px，10rem 将等同于 160px，即 10 x 16 = 160。

通常我们设置font-size：

```
html { font-size: 62.5%; }
```

因为**任何浏览器的默认字体大小都是16px**，所以也就是说

```
html { font-size: 10px; }
```

这样有利于我们队rem 和 em 的换算； 也就是 1rem  = 10px （10px 为根元素的的字体大小）；

**问题的引出**

这里结合自己对网易与淘宝移动端首页html元素上的font-size这个属性的思考与学习，讨论html5设计稿尺寸以及前端与设计之间协作流程的问题。

如果html5要适应各种分辨率的移动设备，应该使用rem这样的尺寸单位，同时给出了一段针对各个分辨率范围在html上设置font-size的代码：

```
html{font-size:10px}
@media screen and (min-width:321px) and (max-width:375px){html{font-size:11px}}
@media screen and (min-width:376px) and (max-width:414px){html{font-size:12px}}
@media screen and (min-width:415px) and (max-width:639px){html{font-size:15px}}
@media screen and (min-width:640px) and (max-width:719px){html{font-size:20px}}
@media screen and (min-width:720px) and (max-width:749px){html{font-size:22.5px}}
@media screen and (min-width:750px) and (max-width:799px){html{font-size:23.5px}}
@media screen and (min-width:800px){html{font-size:25px}}
```

在实际项目中，把与元素尺寸有关的css，如width,height,line-height,margin,padding等都以rem作为单位，这样页面在不同设备下就能保持一致的网页布局。举例来说，网页有一个.item类，设置了width为3.4rem，该类在不同分辨率下对应的实际宽度如下：

```
321px <= device-width <= 375px，font-size:11px        --->  .item的width：34px
376px <= device-width <= 414px，font-size:12px        --->  .item的width：37.4px
415px <= device-width <= 639px，font-size:15px        --->  .item的width：40.8px
640px <= device-width <= 719px，font-size:20px        --->  .item的width：51px
720px <= device-width <= 749px，font-size:22.5px      --->  .item的width：76.5px
750px <= device-width <= 799px，font-size:23.5px      --->  .item的width：79.8999999px
800px <= device-width         ，font-size:25px        --->  .item的width：85px
```
以上代码乍看没啥问题，响应式设计不就应该是这么干的吗？但是从工作量和复杂度方面来考虑，它有以下几个不足：

（1）.item类在所有设备下的width都是3.4rem，但在不同分辨率下的实际像素是不一样的，所以在有些分辨率下，width的界面效果不一定合适，有可能太宽，有可能太窄，这时候就要对width进行调整，那么就需要针对.item写媒介查询的代码，为该分辨率重新设计一个rem值。然而，这里有7种媒介查询的情况，css又有很多跟尺寸相关的属性，哪个属性在哪个分辨率范围不合适都是不定的，最后会导致要写很多的媒介查询才能适配所有设备，而且在写的时候rem都得根据某个分辨率html的font-size去算，这个计算可不见得每次都那么容易，比如40px / 23.5px，这个rem值口算不出来吧！由此可见这其中的麻烦有多少。

（2）以上代码中给出的7个范围下的font-size不一定是合适的，这7个范围也不一定合适，实际有可能不需要这么多，所以找出这些个范围，以及每个范围最合适的font-size也很麻烦

（3）设计稿都是以分辨率来标明尺寸的，前端在根据设计稿里各个元素的像素尺寸转换为rem时，该以哪个font-size为准呢？这需要去写才能知道。
正是因为以上提到的一些不足，我觉得这种适配方式不是特别好，写起来太麻烦。为了完成工作，我们需要找寻更简单更有效率的方法。那么html5该如何去做众多移动设备的适配呢？

## 网易的做法 ##

先来看看网易在不同分辨率下，呈现的效果：

![](/images/rem/wy-1.png) ![](/images/rem/wy-2.png)

从这两张图片可以看出，随着分辨率的增大，页面的效果会发生明显变化，主要体现在各个元素的宽高与间距。375*680的比320*680的导航栏明显要高。能够达到这种效果的根本原因就是因为网易页面里除了font-size之外的其它css尺寸都使用了rem作为单位，比如你看导航栏的高度设置代码:

![](/images/rem/height.png)

使用rem布局结合在html上根据不同分辨率设置不同font-size有很多不好解决的麻烦，网易是如何解决的呢？ 

最根本的原因在于，网易页面上html的font-size不是预先通过媒介查询在css里定义好的，而是通过js计算出来的 ，所以当分辨率发生变化时，html的font-size就会变，不过这得在你调整分辨率后，刷新页面才能看得到效果。你看代码就知道为啥font-size是直接写到html的style上面的了（js设置的原因）

![](/images/rem/wy-3.png)

它是根据什么计算的，这就跟设计稿有关了，拿网易来说，它的设计稿应该是基于iphone4或者iphone5来的，所以它的设计稿竖直放时的横向分辨率为640px，为了计算方便，取一个100px的font-size为参照，那么body元素的宽度就可以设置为width: 6.4rem，于是html的font-size=deviceWidth / 6.4。这个deviceWidth就是viewport设置中的那个deviceWidth。根据这个计算规则，可得出本部分开始的四张截图中html的font-size大小如下：

deviceWidth = 320，font-size = 320 / 6.4 = 50px
deviceWidth = 375，font-size = 375 / 6.4 = 58.59375px
deviceWidth = 414，font-size = 414 / 6.4 = 64.6875px
deviceWidth = 500，font-size = 500 / 6.4 = 78.125px
事实上网易就是这么干的，你看它的代码就知道，body元素的宽是：

![](/images/rem/wy-4.png)

根据这个可以肯定它的设计稿竖着时的横向分辨率为640。然后你再看看网易在分辨率为320*680，375*680，414*680，500*680时，html的font-size是不是与上面计算的一致：

![](/images/rem/wy-5.png)

这个deviceWidth通过document.documentElement.clientWidth就能取到了，所以当页面的dom ready后，做的第一件事情就是：

**document.documentElement.style.fontSize = document.documentElement.clientWidth / 6.4 + 'px';**

这个6.4怎么来的，当然是根据设计稿的横向分辨率/100得来的。下面总结下网易的这种做法：

- （1）先拿设计稿竖着的横向分辨率除以100得到body元素的宽度：

如果设计稿基于iphone6，横向分辨率为750，body的width为750 / 100 = 7.5rem
如果设计稿基于iphone4/5，横向分辨率为640，body的width为640 / 100 = 6.4rem

- （2）布局时，设计图标注的尺寸除以100得到css中的尺寸，比如下图：

![](/images/rem/wy-6.png)

播放器高度为210px，写样式的时候css应该这么写：height: 2.1rem。之所以取一个100作为参照，就是为了这里计算rem的方便！
（3）在dom ready以后，通过以下代码设置html的font-size:
document.documentElement.style.fontSize = document.documentElement.clientWidth / 6.4 + 'px';
6.4只是举个例子，如果是750的设计稿，应该除以7.5。 
（4）font-size可能需要额外的媒介查询，并且font-size不能使用rem，如网易的设置：
@media screen and (max-width:321px){
	.m-navlist{font-size:15px}
}
@media screen and (min-width:321px) and (max-width:400px){
	.m-navlist{font-size:16px}
}
@media screen and (min-width:400px){
	.m-navlist{font-size:18px}
}
最后还有2个情况要说明：

第一，如果采用网易这种做法，视口要如下设置：

<meta name="viewport" content="initial-scale=1,maximum-scale=1, minimum-scale=1">
第二，当deviceWidth大于设计稿的横向分辨率时，html的font-size始终等于横向分辨率/body元素宽,

![](/images/rem/wy-7.png)

之所以这么干，是因为当deviceWidth大于640时，则物理分辨率大于1280（这就看设备的devicePixelRatio这个值了），应该去访问pc网站了。事实就是这样，你从手机访问网易，看到的是触屏版的页面，如果从pad访问，看到的就是电脑版的页面。如果你也想这么干，只要把总结中第三步的代码稍微改一下就行了：

var deviceWidth = document.documentElement.clientWidth;
if(deviceWidth > 640) deviceWidth = 640;
document.documentElement.style.fontSize = deviceWidth / 6.4 + 'px';

然后就把网易代码给拔了下来：

```
    var Dpr = 1, uAgent = window.navigator.userAgent;
    var isIOS = uAgent.match(/iphone/i);
    var isYIXIN = uAgent.match(/yixin/i);
    var is2345 = uAgent.match(/Mb2345/i);
    var ishaosou = uAgent.match(/mso_app/i);
    var isSogou = uAgent.match(/sogoumobilebrowser/ig);
    var isLiebao = uAgent.match(/liebaofast/i);
    var isGnbr = uAgent.match(/GNBR/i);
    function resizeRoot(){
        var wWidth = (screen.width > 0) ? (window.innerWidth >= screen.width || window.innerWidth == 0) ? screen.width : window.innerWidth : window.innerWidth, wDpr, wFsize;
        var wHeight = (screen.height > 0) ? (window.innerHeight >= screen.height || window.innerHeight == 0) ? screen.height : window.innerHeight : window.innerHeight;
        if (window.devicePixelRatio) {
            wDpr = window.devicePixelRatio;
        } else {
            wDpr = isIOS ? wWidth > 818 ? 3 : wWidth > 480 ? 2 : 1 : 1;
        }
        if(isIOS) {
            wWidth = screen.width;
            wHeight = screen.height;
        }
        // if(window.orientation==90||window.orientation==-90){
        //     wWidth = wHeight;
        // }else if((window.orientation==180||window.orientation==0)){
        // }
        if(wWidth > wHeight){
            wWidth = wHeight;
        }
        wFsize = wWidth > 1080 ? 144 : wWidth / 7.5;
        wFsize = wFsize > 32 ? wFsize : 32;
        window.screenWidth_ = wWidth;
        if(isYIXIN || is2345 || ishaosou || isSogou || isLiebao || isGnbr){//YIXIN 和 2345 这里有个刚调用系统浏览器时候的bug，需要一点延迟来获取
            setTimeout(function(){
                wWidth = (screen.width > 0) ? (window.innerWidth >= screen.width || window.innerWidth == 0) ? screen.width : window.innerWidth : window.innerWidth;
                wHeight = (screen.height > 0) ? (window.innerHeight >= screen.height || window.innerHeight == 0) ? screen.height : window.innerHeight : window.innerHeight;
                wFsize = wWidth > 1080 ? 144 : wWidth / 7.5;
                wFsize = wFsize > 32 ? wFsize : 32;
                // document.getElementsByTagName('html')[0].dataset.dpr = wDpr;
                document.getElementsByTagName('html')[0].style.fontSize = wFsize + 'px';
                document.getElementById("fixed").style.display="none";
            },500);
        }else{
            // document.getElementsByTagName('html')[0].dataset.dpr = wDpr;
            document.getElementsByTagName('html')[0].style.fontSize = wFsize + 'px';
            document.getElementById("fixed").style.display="none";
        }
        // alert("fz="+wFsize+";dpr="+window.devicePixelRatio+";
        //UA="+uAgent+";width="+wWidth+";sw="+screen.width+";
        //wiw="+window.innerWidth+";wsw="+window.screen.width+window.screen.availWidth);
    }
    resizeRoot();
    
```

## 淘宝的做法 ##

淘宝的效果跟网易的效果其实是类似的，随着分辨率的变化，页面元素的尺寸和间距都相应变化，这是因为淘宝的尺寸也是使用了rem的原因。在介绍它的做法之前，先来了解一点关于viewport的知识，通常我们采用如下代码设置viewport:

```
<meta name="viewport"   content="width=device-width, initial-scale=1, maximum-scale=1, user-scalable=no">
```

这样整个网页在设备内显示时的页面宽度就会等于设备逻辑像素大小，也就是device-width。这个device-width的计算公式为：

**设备的物理分辨率/(devicePixelRatio * scale)**，在scale为1的情况下，device-width = 设备的物理分辨率/devicePixelRatio 。

devicePixelRatio称为设备像素比，每款设备的devicePixelRatio都是已知，并且不变的，目前高清屏，普遍都是2，不过还有更高的，比如2.5, 3 等，魅族note的手机的devicePixelRatio就是3。淘宝触屏版布局的前提就是viewport的scale根据devicePixelRatio动态设置。

- 在devicePixelRatio为2的时候，scale为0.5

- 在devicePixelRatio为3的时候，scale为0.3333

这么做目的当然是为了保证页面的大小与设计稿保持一致了，比如设计稿如果是750的横向分辨率，那么实际页面的device-width，以iphone6来说，也等于750，这样的话设计稿上标注的尺寸只要除以某一个值就能够转换为rem了。通过js设置viewport的方法如下：

```
var scale = 1 / devicePixelRatio;

document
    .querySelector('meta[name="viewport"]')
    .setAttribute('content','initial-scale=' + scale + ', maximum-scale=' + scale + ', minimum-scale=' + scale + ', user-scalable=no');
```
淘宝布局的第二个要点，就是html元素的font-size的计算公式，font-size = deviceWidth / 10：

接下来要解决的问题是，元素的尺寸该如何计算，比如说设计稿上某一个元素的宽为150px，换算成rem应该怎么算呢？这个值等于设计稿标注尺寸/该设计稿对应的html的font-size。拿淘宝来说的，他们用的设计稿是750的，所以html的font-size就是75，如果某个元素时150px的宽，换算成rem就是150 / 75 = 2rem。总结下淘宝的这些做法：

（1）动态设置viewport的scale

```
var scale = 1 / devicePixelRatio;

document
    .querySelector('meta[name="viewport"]')
    .setAttribute('content','initial-scale=' + scale + ', maximum-scale=' + scale + ', minimum-scale=' + scale + ', user-scalable=no');
```

（2）动态计算html的font-size : `document.documentElement.style.fontSize = document.documentElement.clientWidth / 10 + 'px';`

（3）布局的时候，各元素的css尺寸=设计稿标注尺寸/设计稿横向分辨率/10

（4）font-size可能需要额外的媒介查询，并且font-size不使用rem，这一点跟网易是一样的。
最后还有一个情况要说明，跟网易一样，淘宝也设置了一个临界点，当设备竖着时横向物理分辨率大于1080时，html的font-size就不会变化了，原因也是一样的，分辨率已经可以去访问电脑版页面了。


>  互联网上还有很多不同的布局方式，但是万变不离其宗，大致的思路就是这个样子的！