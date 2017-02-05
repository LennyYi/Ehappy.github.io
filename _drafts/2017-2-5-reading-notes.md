---
layout: post
title:  "javascript 读书笔记"
date:   2017-02-05 11:13:36 +86
tag: javasciprt
categories: coderoad
---
``` javasciprt

function factorial(num){
   if(num <= 1){
		 return 1;
	 }else{
		 return num * factorial(num - 1);
	 }

}

function factorial(num){
   if(num <= 1){
		 reutrn 1;
	 }else{
		 return num *  arguments.callee(num-1);
	 }

}

var trueFactorial = factorial;
factorial = function(){
	return 0;
}

alert(trueFactorial(5));//120
alert(factorial(5));//0
```
这两个函数明显第二个更加通用


</div>
