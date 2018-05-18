---
title: Vue key的用法 
date: 2018-05-18 14:57:29
tags : vue
categories : javascript
---


> 很多人在学习vue的过程中，只是知道v-for语句，添加key就不会有警告，仅仅如此而已！

我们知道，vue和react都实现了一套虚拟DOM，使我们可以不直接操作DOM元素，只操作数据便可以重新渲染页面。而隐藏在背后的原理便是其高效的Diff算法（这里不做详细介绍）。因此key在日常使用中通常的作用有两点，其中第二点很少人使用：

**1. key的作用主要是为了高效的更新虚拟DOM**

**2. 完整地触发组件的生命周期钩子**


----------

首先我们聊一下v-for中的使用：

```
<ul>
  <li v-for="item in items" :key="item.id">...</li>      
  <li v-for="(item,index) in items" :key="index">...</li>
  <li v-for="(item,key,index) in items" :key="key">...</li>
</ul>
```

上面代码是三种常用的Key的使用方式，但是这三种是有区别的，假设item是对象，且有id属性：

> 第一种，当id值变化，元素（li）重新渲染，id值无变化，元素（li）不会重新渲染。 
> 
> 第二种，当id值变化，元素（li）不会重新渲染， 当改变位置时，元素（li）重新渲染。
> 
> 第三种，当id值变化，元素（li）不会重新渲染， 当改变位置时，元素（li）重新渲染。

通过上面的三种结论我们得出：

**使用v-for渲染元素时，使用元素自身的id属性去指定渲染元素的key值有利于单个元素的重新渲染，若采用其他如v-for提供的index, key等值，在改变渲染出来的DOM结构时，会触发所有元素的重新渲染，当数据过大时，可能会造成性能负担。**

**因此当我们在使用v-for进行渲染时，尽可能使用渲染元素自身属性的id给渲染的元素绑定一个key值，这样在当前渲染元素的DOM结构发生变化时，能够单独响应该元素而不触发所有元素的渲染。**


----------

从上面的v-for实例中，如果我们把元素（li）替换成组件，该组件如果重新渲染，则会触发生命周期的钩子。

下面我们通过实例来说明key的另一种使用方法.

```
<swiper class="myswper" ref="mySwiper" :options="swiperOption"   :key="swiperSlides.length" >
    <swiper-slide   class="swiper-no-swiping" v-for="(file, index) in swiperSlides" :key="index"  >
        <img class="myImgs" :src="file.url"  :draggable="false" >
    </swiper-slide>
</swiper>
```

上面这段代码其实是两个组件外层 `swiper`组件， 里面sort部分是新的组件`swiper-slide`。 `swiper`组件也有一个key属性，这样写的目的是当 `swiper-slide`的数据长度有变化，`swiper`会重新渲染，然后触发生命周期！


也可以简单的理解为

```
<A-component :key="swiperSlides.length"></A-component>
<B-component  v-for="(item, index) in obj" :key="index" ></B-component>
```

A组件想要在B组件变化时，重新渲染自己。 我们可以改变他的Key来实现。