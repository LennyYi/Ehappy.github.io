---
layout: post
title:  "javascript  看书经验"
date:   2017-1-8 21:09:36 +86
tag: javasciprt,experience
categories: coderoad
---
```
1.
遇到浮点数在对于java，javasciprt，c，c#来说都要小心计算小数后面的数。
超过小数位后的一些值是一定不精确的。


C语言一直搞错的一个地方。
二进制表达方式：
正数的二进制， 首位是0 其他位按2的位来算。
复数的二进制， 首位是1 其他位是根据正数取反后的补码来确定的，其表达方式不能在按2的位次方来算了。 以前一直蠢的认为负数也要按2的位次方来算。所有的数值在计算机中的二进制码是固定的。如果是负数，我们要看其绝对值就需要把他先减一，然后取反得到其绝对值。9 = 0000 0000 0000 1001  如果要知道-9的二进制码就是 0000 0000 0000 1001 去反 1111 1111 1111 0110 +1 = 1111 1111 1111 0111 （即计算机中如果遇到了这个就能够解析成-9）。如果我们知道1111 1111 1111 0111 的机器码，要知道其绝对指 就要先-1 1111 1111 1111 0110.获得其反码，然后按位取反。0000 0000 0000 1001  然后再按二进制的表达方式来算。不过我们在很多时候，出现一个整数的二进制数溢出的情况就是把符号位充当了算术位。把1111 1111 1111 0110 当作正数来解析就会出现错误。或者我们对其二进制码进行二进制操作，直接改掉其符号位，机器马上就会把这个当作正数来解释给我们看。
2.
javasciprt 或者说 ECMAScript中 && 与 || 绝非我们认知的那么简单，以及包括!!与~,^,&，|等操作符都可以用在我们意想不到的地方那个。
 !!可以把一般对象转化为Boolean
 &&与|| 可以用在判断赋值操作中。
 因为其遵循赋值判断的几大规律：以下就是蹩脚翻译的结果，其真实意义绝对不是这样。
 &&操作有用
 1.如果第一个操作数是对象，则返回第一个操作符。
 2.如果第一个操作数求值结果为false，则返回第二个操作数。
 3.如果两个操作数都是对象那么返回第一个操作数。
 4.如果两个操作数都是null就返回null
 5.如果两个操作数都是NaN则返回NaN
 6.如果两个操作数都是undefined 则返回undefined。

 ||操作有用
 1.如果第一个操作数是对象，则返回第一个操作符。
 2.如果第一个操作数求值结果为false，则返回第二个操作数。
 3.如果两个操作数都是对象那么返回第一个操作数。
 4.如果两个操作数都是null就返回null
 5.如果两个操作数都是NaN则返回NaN
 6.如果两个操作数都是undefined 则返回undefined。

 （没有定义的变量会发生错误，虽然他也是undefined）


 javascript没有块级作用域。
 所以 for语句 以及 if语句中定义的变量都会超出其大括号范围。

 对于let 严格模式下使用let更加符合习惯

```
详情请看[经典博客](http://blog.csdn.net/nfer_zhuang/article/details/48781671,"csdn")
