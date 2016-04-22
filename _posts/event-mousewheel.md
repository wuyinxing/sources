---
title: 浅谈 Mousewheel 事件
tags : event
categories : JavaScript
---

Firefox使用DOMMouseScroll，其他的浏览器使用mousewheel。
滚动事件触发时Firefox使用detail属性捕捉滚轮信息，其他的浏览器使用wheelDelta。
不知道为何在该问题上其他厂商和微软的如此一致。Firefox可以使用addEventListener方法绑定DomMouseScroll事件。

```
<script type="text/javascript">
    // <![CDATA[
    var mouseWheel = document.getElementById('mouseWheel');
     
    if (mouseWheel.addEventListener) {
         
        mouseWheel.addEventListener('DOMMouseScroll', function(event) {
             
            event.target.innerHTML = event.detail;
            event.stopPropagation();
            event.preventDefault();
             
        }, false);
    }
    mouseWheel.onmousewheel = function(event) {
         
        event = event || window.event;
        mouseWheel.innerHTML = event.wheelDelta;
        event.returnValue = false;
         
    }
    // ]]>
    Firefox
    鼠标滚轮向上滚动是-3，向下滚动是3

    IE
    鼠标滚轮向上滚动是120，向下滚动是-120

    Safari
    鼠标滚轮向上滚动是360，向下滚动是-360

    Opera
    鼠标滚轮向上滚动是120，向下滚动是-120

    Chrome
    鼠标滚轮向上滚动是120，向下滚动是-120
</script>
```


### 模拟触发滚轮事件 , 并对返回值操作： 
```
$(div).onmousewheel(function(e){
        var e = window.event || e;
        e.wheelDelta = e.wheelDelta  ± 3 ;
})；

$(div).trigger('mousewheel');

```


>  “mousewheel” 事件中的 “event.wheelDelta” 属性值：返回的值，如果是正值说明滚轮是向上滚动，如果是负值说明滚轮是向下滚动；返回的值，均为 120 的倍数，即：幅度大小 = 返回的值 / 120。
> 
>  “DOMMouseScroll” 事件中的 “event.detail” 属性值：返回的值，如果是负值说明滚轮是向上滚动（与 “event.wheelDelta” 正好相反），如果是正值说明滚轮是向下滚动；返回的值，均为 3 的倍数，即：幅度大小 = 返回的值 / 3。
> 
>  “mousewheel” 事件在 Opera 10+ 中却是个特例，既有 “event.wheelDelta” 属性，也有 “event.detail” 属性。
