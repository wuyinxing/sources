---
title: 原生JavaScript基础方法(连载中……)
tags : base
categories : JavaScript
---

> 
>  记录一些简单的基础JavaScript方法
>

### 原生JavaScript判断数组

```
function isArray (arr){
	return Object.prototype.toString.call(arr) == "[object Array]"
}
```

### js数组排序sort()方法

```

//这里我们需要注意，JavaScript数组sort 是按字符编码进行排序的

arr.sort(function(a,b){return a>b});//从小到大排序
arr.sort(function(a,b){return a<b});//从大到小排序

```

### 原生JavaScript实现字符串长度截取
```
function cutstr(str, len) { 
    var temp; 
    var icount = 0; 
    var patrn = /[^\x00-\xff]/; 
    var strre = ""; 
    for (var i = 0; i < str.length; i++) { 
        if (icount < len - 1) { 
            temp = str.substr(i, 1); 
            if (patrn.exec(temp) == null) { 
                icount = icount + 1 
            } else { 
                icount = icount + 2 
            } 
            strre += temp 
        } else { 
            break 
        } 
    } 
    return strre + "..." 
} 
```

### 原生JavaScript获取域名主机
```
function getHost(url) { 
    var host = "null"; 
    if(typeof url == "undefined"|| null == url) { 
        url = window.location.href; 
    } 
    var regex = /^\w+\:\/\/([^\/]*).*/; 
    var match = url.match(regex); 
    if(typeof match != "undefined" && null != match) { 
        host = match[1]; 
    } 
    return host; 
} 
```

### 原生JavaScript清除空格
```
String.prototype.trim = function() { 
    var reExtraSpace = /^\s*(.*?)\s+$/; 
    return this.replace(reExtraSpace, "$1")
} 
```
### 原生JavaScript替换全部
```
String.prototype.replaceAll = function(s1, s2) { 
    return this.replace(new RegExp(s1, "gm"), s2) 
} 
```

### 原生JavaScript转义html标签
```
function HtmlEncode(text) {
    return text.replace(/&/g, '&amp').replace(/\"/g, '&quot;').replace(/</g, '&lt;').replace(/>/g, '&gt;')
}
```

### 原生JavaScript还原html标签
```
function HtmlDecode(text) {
    return text.replace(/&amp;/g, '&').replace(/&quot;/g, '\"').replace(/&lt;/g, '<').replace(/&gt;/g, '>')
}
```
### 原生JavaScript判断是否为数字类型
```
function isDigit(value) { 
    var patrn = /^[0-9]*$/; 
    if (patrn.exec(value) == null || value == "") { 
        return false 
    } else { 
        return true 
    } 
} 
```
### 原生JavaScript设置cookie值
```
function setCookie(name, value, Hours) { 
    var d = new Date(); 
    var offset = 8; 
    var utc = d.getTime() + (d.getTimezoneOffset() * 60000); 
    var nd = utc + (3600000 * offset); 
    var exp = new Date(nd); 
    exp.setTime(exp.getTime() + Hours * 60 * 60 * 1000); 
    document.cookie = name + "=" + escape(value) + ";path=/;expires=" + exp.toGMTString() + ";domain=360doc.com;" 
} 
```
### 原生JavaScript获取cookie值
```
function getCookie(name) { 
    var arr = document.cookie.match(new RegExp("(^| )" + name + "=([^;]*)(;|$)")); 
    if (arr != null) return unescape(arr[2]); 
    return null
 } 
```
### 原生JavaScript加入收藏夹
```
function AddFavorite(sURL, sTitle) { 
    try { 
        window.external.addFavorite(sURL, sTitle) 
    } catch(e) { 
        try { 
            window.sidebar.addPanel(sTitle, sURL, "") 
        } catch(e) { 
            alert("加入收藏失败，请使用Ctrl+D进行添加") 
        } 
    } 
} 
```
### 原生JavaScript元素显示的通用方法
```
function $(id) { 
        return !id ? null : document.getElementById(id); } function display(id) { 
        var obj = $(id); 
        if(obj.style.visibility) { 
                obj.style.visibility = obj.style.visibility == 'visible' ? 'hidden' : 'visible'; 
        } else { 
                obj.style.display = obj.style.display == '' ? 'none' : ''; 
        } 
} 
```
### 原生JavaScript简单的日期操作
```
    var date = new Date();               //当前日期   
    var _DayOfWeek = date.getDay()-1;    //今天本周的第几天   
    var _curDay = date.getDate();        //当前日   
    var _curMonth = date.getMonth();     //当前月   
    var _curYear = date.getFullYear();   //当前年 

    //获得本周的开始日期   
    function getWeekStartDate() {   
        var weekStartDate = new Date(_curYear, _curMonth, _curDay - _DayOfWeek);   
        return formatDate(weekStartDate);   
    }   

    //获得本周的结束日期   
    function getWeekEndDate() {   
        var weekEndDate = new Date(_curYear, _curMonth, _curDay + (6 - _DayOfWeek));   
        return formatDate(weekEndDate);   
    } 

    //获得上周的开始日期   
    function getLastWeekStartDate() {   
        var weekStartDate = new Date(_curYear, _curMonth, _curDay - _DayOfWeek - 7);   
        return formatDate(weekStartDate);   
    }   

    //获得上周的结束日期   
    function getLastWeekEndDate() {   
        var weekEndDate = new Date(_curYear, _curMonth, _curDay - _DayOfWeek - 1);   
        return formatDate(weekEndDate);   
    }

    //获得本月的开始日期   
    function getMonthStartDate() {   
        var monthStartDate = new Date(_curYear, _curMonth, 1);   
        return formatDate(monthStartDate);   
    }   
    //获得本月的结束日期   
    function getMonthEndDate() {   
        var monthEndDate = new Date(_curYear, _curMonth, getMonthDays(_curMonth));   
        return formatDate(monthEndDate);   
    }

    //格式化日期：yyyy-MM-dd   
    function formatDate(date) {   
        var myyear = date.getFullYear();   
        var mymonth = date.getMonth() + 1;   
        var myweekday = date.getDate();   
        if (mymonth < 10) {   
            mymonth = "0" + mymonth;   
        }   
        if (myweekday < 10) {   
            myweekday = "0" + myweekday;   
        }   
        return (myyear + "-" + mymonth + "-" + myweekday);   
    } 

    //获得某月的天数   
    function getMonthDays(myMonth) {   
        var monthStartDate = new Date(nowYear, myMonth, 1);   
        var monthEndDate = new Date(nowYear, myMonth + 1, 1);   
        var days = (monthEndDate - monthStartDate) / (1000 * 60 * 60 * 24);   
        return days;   
    } 
```