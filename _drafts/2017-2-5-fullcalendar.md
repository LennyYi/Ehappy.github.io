---
layout: post
title:  "对日历模块的思考"
date:   2017-02-05 11:13:36 +86
tag: calendar
categories: coderoad
---
公司要做个日历模块来创建schedule，由于领导选择了fullcalendar，我想可能是为了以后的需求以及扩展。对于现在的需求来说这个插件有些多了。不过感觉以后的日历发展方向多半就是自己能够编辑事件和一些富应用操作。不过对于我们的BI的有些细节要求我实在是不敢苟同。

1.日历的字必须放中间？！
我：我觉得日历字放中间根本是不合时宜的，以后需要做写编辑的时候又要改样式，而且我觉得日历模块不单单作为一个日历，而是要做半个记事本功能的模块。所以这一点我感觉还不如就用默认模式。
2.由于fullcalendar有三种week模式，随着header动，不随header动，以及fix。
我们BI说对于日历显示上个月与下个月时间太不理想了。
我：与其让日历动来动去还不如用上个月与下个月的日期来填充，本来就可以灰化。
（突然记起一件事情，有关旅游行业的发展，我想到我以后如果向爬山，并要爬到山顶去，我又不想带行李，于是我就有这种需求，我从下山租个车把我的行李直接快递到我要落脚的低点（帐篷什么的），然后我就可以慢慢的去旅游了）



调整后的代码如下：
``` javasciprt
	$(document).ready(function() {

		$('#calendar').fullCalendar({
			theme: true,
			header: {
				left: 'today',
				center: 'prevYear prev title next nextYear',
				right: ''
			},
			buttonIcons:{ prevYear :'circle-triangle-w',prev: 'circle-triangle-w', next: 'circle-triangle-e',nextYear :'circle-triangle-e' },
			navLinks: false, // can click day/week names to navigate views
			editable: false,
			eventLimit: true, // allow "more" link when too many events
			fixedWeekCount:false,
			//height: 650,
			contentHeight: 600,
			handleWindowResize:true,
			events: [

			]
		});
		//$(".fc-other-month").
	});


<style>

	body {
		margin: 40px 10px;
		padding: 0;
		font-family: "Lucida Grande",Helvetica,Arial,Verdana,sans-serif;
		font-size: 14px;
	}

	#calendar {
		max-width: 900px;
		margin: 0 auto;
	}

		.fc-day-number{
	  font-size:20px;
	display:block;

text-align:center;
display:block;
top:30px;
left:100px;

	}

</style>

	$('#calendar').fullCalendar('option', 'height', 700); 设置高度
	//设置隐藏非当月日期；
	.fc-ltr .fc-basic-view .fc-other-month {
    <!--//visibility:hidden;-->
}
```
