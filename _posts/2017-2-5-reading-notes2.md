---
layout: post
title:  "javascript tips 2"
date:   2017-02-05 11:13:36 +86
tag: calendar
categories: coderoad
---
函数的名字就是包含指针的变量而已，因此在不同环境下执行，全局的sayColor函数与o.sayColor()指向的任然是同一函数。

```javasciprt
window.color = red;
var o = {color:"blue"};
function sayColor(){
   alert(this.color);

}
sayColor();//red
o.sayColor = sayColor;
o.sayColor();//blue
```
