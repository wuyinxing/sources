---
title: html中div反复滚动
tags : scroll
categories : JavaScript
---

> html中div反复滚动的实例

```

<div id="content">
	<div id="zs" class="pages">
		内容1……
	</div>
	<div id="lis" class="pages">
		内容2……
	</div>
	<div id="wf" class="pages">
		内容3……
	</div>
	<div id="zd" class="pages">
		内容4……
	</div>
	……
</div>
<div>
	<div class="prevbtn"></div>
	<div class="nextbtn"></div>
</div>

<script type="text/javascript">
//存储所有的id
var contentpageArr = (function(){
    $.map($('.pages'),function(elem){
        arr.push($(elem).attr('id'));
    });
    return arr;
})();

//下一个
$('.nextbtn').on('click',function(){
    var down = getNextEle(); 
    $('#content').scrollTo('#' + down,500);  
});

//上一个
$('.prevbtn').on('click',function(){
    var up = getPrevEle();
    $('#content').scrollTo('#' + up,500);  
});

//获取当前可视容器前面的容器ID
function getPrevEle(){
    var sTop = $('#content').scrollTop(); 
    for(var x = contentpageArr.length-1 ; x >= 0 ; x--){
        var tp = $('#'+contentpageArr[x]).offset().top;
        if (tp < 0) { 
            return contentpageArr[x];
        }
    }
    return contentpageArr[0];
}
//获取当前可视容器后面的容器ID
function getNextEle(){
    var sTop = $('#content').scrollTop();
    for(var x = 0;  x < contentpageArr.length ; x++ ){
        var tp =$('#'+contentpageArr[x]).offset().top;
        if (sTop < tp) {
            return contentpageArr[x];
        }
    }
    return contentpageArr[contentpageArr.length-1];
}
</script>
```