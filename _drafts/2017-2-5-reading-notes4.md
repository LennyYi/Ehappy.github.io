---
layout: post
title:  "javascript tips 4"
date:   2017-02-05 11:13:36 +86
tag: calendar
categories: coderoad
---
call 函数与 apply 函数 就是简单粗暴的在方法内部设置函数体内的this对象的值。apply()函数，call()函数作用其实都一样，就是传函数参数的时候有些不同
其真正作用就是除了传递参数外，就是扩充函数赖以运行的作用域
```javasciprt
window.color = "rad";
var o = {color : "blue"};
function sayColor(){
  alert(this.color);
}
sayColor();

sayColor.call(this);
sayColor.call(window);
sayColor.call(o);



```
ECMA5还提供的bind方法，这个方法会常见一个函数实例，其this值会绑定到传给bind()函数的值。其优点在很多地方用到。
