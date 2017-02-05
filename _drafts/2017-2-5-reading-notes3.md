---
layout: post
title:  "javascript tips 3"
date:   2017-02-05 11:13:36 +86
tag: calendar
categories: coderoad
---
caller 函数表示调用当前函数的函数的引用；
```javasciprt
function outer(){
  inner();
}
function inner(){
  alert(inner.caller)
}
outer()//outer()函数源代码

//另一种写法

function inner(){
  alert(arguments.callee.caller);
  //表示调用当前参数的 函数的 调用函数的 引用（绕死了）
}
//记住ECMA5中prototype不可枚举，所有的函数都挂名在prototype下，但是prototype不可枚举。（醉了）
```
