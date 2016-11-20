---
layout: post
title:  "Javascript中prototype,proto,constructor 最好的讲解文章之一"
date:   2016-11-18 11:09:36 +86
tag: javascript,prototype,proto,construtor
categories: coderoad
---
<2014年的cnblog博客迁移>
开始之前普及一个知识，我们造砖的模具叫做砖模，造出来的砖头(没烧的)叫做砖坯(念 pi)。（搬砖时候学到的！！！—:-）

前几天当我同事向我说起node.js的时候，我逐渐发现原来JS是多功能的语言——我原来一直都在井底（初学者的悲哀）。当我想认认真真学习JavaScript的时候，我发现prototype与__proto__、constructor这几个家伙老是让我很有挫败感（学一门语言属性都搞不懂，还学个什么）。但我是一个不轻易服输的人，也不是钻牛角尖（其实就是）就是有这种习惯。有些事情就要去做就要去弄懂，哪怕做了弄了挫折感再多。于是，我在网上搜大神们的对这几个东西的阐述与解释。最后的结果是--还是搞不懂。“什么对象原型，什么内部原型！能不能浅显一点，搞的以为自己很专业一样”。后来去看“javascript语言精髓与编程实践精简版（自行百度）”的时候，突然对__proto__与prototype有了一层清晰的认识。
![create gh-pages branch](http://ogu9js0qs.bkt.clouddn.com/prototype.jpg)


我在这里只用比喻，只说我理解的含义，而不是给他下定义，每个人的理解是可以不同的知识不要搞的那么正式，每个人学习的目的是运用，不是把自己的理解用来显摆，更加不是拿着官方定义搬来搬去。<br/>

function能够调用的prototype一般被定义为“对象原型的引用”，有的人也说是"实例"（一开始我以Java的理念理解，真是把我搞醉了）。我对于prototype的理解就是“模具”，也就是对象的模子(就如我们生活中看到的造砖头的模具)。当我们对prototype添加属性的时候"模子"会改变它的形状，以后用这个模子造出来的实例（var a = new A;）就会是跟模子一样的东西，所以说他的实例就会拥有prototype的属性。如果我们用"A.prototype="的形式就会改变它的模子也就是换一个“模子”，所以这样做不会是一点点改变了，而是替换（就是换了一个造蜂窝煤的模子造出来的就不是砖坯）。所以说在执行这句话的时候，就会改变A（注意大小写）的造出来的砖坯的属性。如果还是不懂就弄清我给你们的代码加上自己做实验。__proto__在我的理解里是“影子”或者说父类的属性视图。用 "var A =  function (){}或者function A() {}  var a = new A();"的时候a中的__proto__属性会指向A.prototype.这是原型链中最恒等的，不管prototype改变还是__proto__改变，这条结论始终是要相等。
(我把X变成A不会看错了吧)

{% highlight js %}


 

//原文的例子，有些许错误。在他的例子上有些许改进。

function Foo()
{

}

Foo.__proto__.test="__proto__ test property found";//通过__proto__扩展
Foo.__proto__.addextend=function(){alert("Foo add extend by __proto__");}
alert(Function.prototype.test);//可以访问，这里为什么对象的是Function.prototype呢？相信Foo.__proto__是儿子，而爸爸是谁呢？Function.prototype

var foo=new Foo;
foo.__proto__.lab = "abc";
alert(foo.__proto__===Foo.prototype);

{% endhighlight %}

上面这段代码对Foo的__proto__进行添加（其实对Foo.__proto__的改变按道理说是对应Function.prototype.test，foo的__proto__是对应Foo的prototype，这条关系永远的恒等的），而__proto__是特殊的影子，你修改它是会影响到Function的prototype的，如果你在上面代码后面添加

{% highlight js %}


for(var x in foo.__proto__){
    alert(x);
}
for(var y in Foo.prototype){
    alert(Foo.prototype.lab);
}
alert(foo.lab);

{% endhighlight %}

是可以访问到lab这个属性的。所以说，就一般来说__proto__类似于数据库中的视图，也类似很奇怪的假设——"影子"反过来会影响真身。<br/>

**----------------------黄金分割线------------------------------------------------------------------------**<br/>
理解constructor的时候可以把prototype看做是一个实例(类似与Java的构造一个对象)，就如Foo的prototype（实例）的构造函数不就是指向函数本身吗！也就是说Foo.prototype.constructor的与foo.constructor的是一样的。而Foo.constructor是Function(这里可以解释为构造Foo的构造函数不就是new Function())这里其实对于prototype的理解我也不能很好的去统一，大家只能自己意会了（分开理解会更加清楚）。

总之，我认为（以我学习几天的JavaScript的立场）来说，Foo.prototype就是就是其new 出来的实例的__proto__的 "模子"或者说"真身"，而foo.__proto__的则是类的“坯子”或者是“影子”（特殊的），当模子改变的时候造出的坯子当然不同，但是坯子改变模子也会变化（特殊的地方在这里，不好找现实的例子）。而constructor就是构造对象的属性了，注意是对象（JavaScript中一切皆对象，所以function本身也是对象，也会有constructor的，所有产生function的都是大Function（通过 new Function();的方式可以看到）.小foo的构造当然是 大Foo，大Foo的(prototype)的constructor当然也是大Foo.所以才会有Foo的prototype的constructor指向他本身的说法。完毕！（PS：虽然是刚刚学习JavaScript，但像我认为的那样理解这三个东西，是我暂时能够接受的方式了，当然不排除硬刚的大神 照着 prototype是某某的实例，_proto_是某某的属性来理解）<br/>
关键还是要自己敲代码，来验证性理解，这一点不能偷懒。
