---
layout: post
title:  "echarts ajax 提交"
date:   2016-12-30 21:09:36 +86
tag: echarts,jax
categories: code
---
一进公司接到的任务就是做一个世界地图的显示模块。因为**的财务系统是统筹世界各国的财务交易，把财务状态分为overdue，normal，risk。在世界地图上需要显示每个国家的财务状况，并且tips中需要显示饼状图以及其他的一些文本信息。echarts自带的tips对于文本信息很好处理，但是对于饼状图实在是有些麻烦。我当时想用一个html嵌入javascript的方式来完成这个task，但是引起了javasciprt vm 的错误，没有细究其错误，就想换种方式来实现。

这篇博文一是记录自己学到的东西，二是记录自己能够提醒自己做的事还有很多完善的地方.<br/>

其实一开始接触echarts对于文件引入并不是很清楚，认真看完文档之后才知道。原来自己一直用的第二种推荐方式的单标签引入方式。不过今天在家终于把模块化引入搞清楚了。模块化引入与单标签引入在表现代码量上没什么区别。但是对于引入模块的js有不同。一个就是echarts，一个除了引入echarts之外还需要引入zrender.

  模块化包引入
  如果你熟悉模块化开发，你的项目本身就是模块化且遵循AMD规范的，那引入echarts将很简单，使用一个符合AMD规范的模块加载器，如esl.js，只需要配置好packages路径指向src即可，你将享受到图表的按需加载等最大的灵活性，由于echarts依赖底层zrender，你需要同时下载zrender到本地，可参考demo，你需要配置如下。

  需要注意的是，包引入提供了开发阶段最大的灵活性，但并不适合直接上线，减少请求的文件数量是前端性能优化中最基本但很重要的规则，务必在上线时做文件的连接压缩。


```javascript
--模块包引入
<script type="text/javascript" src="./common/echarts/jquery-1.7.2.min.js"></script>
<script type="text/javascript" src="./common/echarts/esl.js" ></script>

require.config({
			packages:[
				{  
				    name: 'zrender',  
				    location: './common/zrender2/src', // zrender与echarts在同一级目录  
				    main: 'zrender'  
				},  
				{  
				    name: 'echarts',  
				    location: './common/echarts-2.2.7/src',  
				    main: 'echarts'  
				}      
			]
		});
```

  模块化单文件引入（推荐）
  如果你使用模块化开发但并没有自己的打包合并环境，或者说你不希望在你的项目里引入第三方库的源文件，我们建议你使用单文件引入，同模块化包引入一样，你需要熟悉模块化开发。

  自2.1.8起，我们为echarts开发了专门的合并压缩工具echarts-optimizer。如你所发现的，build文件夹下已经包含了由echarts-optimizer生成的单文件：

  dist（文件夹） : 经过合并、压缩的单文件
  echarts.js : 这是包含AMD加载器的echarts主文件，需要通过script最先引入
  chart（文件夹） : echarts-optimizer通过依赖关系分析同时去除与echarts.js的重复模块后为echarts的每一个图表类型单独打包生成一个独立文件，根据应用需求可实现图表类型按需加载
  line.js : 折线图（如需折柱动态类型切换，require时还需要echarts/chart/bar）
  bar.js : 柱形图（如需折柱动态类型切换，require时还需要echarts/chart/line）
  scatter.js : 散点图
  k.js : K线图
  pie.js : 饼图（如需饼漏斗图动态类型切换，require时还需要echarts/chart/funnel）
  radar.js : 雷达图
  map.js : 地图
  force.js : 力导向布局图（如需力导和弦动态类型切换，require时还需要echarts/chart/chord）
  chord.js : 和弦图（如需力导和弦动态类型切换，require时还需要echarts/chart/force）
  funnel.js : 漏斗图（如需饼漏斗图动态类型切换，require时还需要echarts/chart/pie）
  gauge.js : 仪表盘
  eventRiver.js : 事件河流图
  treemap.js : 矩阵树图
  venn.js : 韦恩图
  source（文件夹） : 经过合并，但并没有压缩的单文件，内容同dist，可用于调试
  采用单一文件使用例子见ECharts单一文件引入，存放在example/www下，首先你需要通过script标签引入echarts主文件
```javascript
  //from echarts example
  <body>
      <div id="main" style="height:400px;"></div>
      ...
      <script src="./js/echarts.js"></script>
  </body>

  //from echarts example
<body>
    <div id="main" style="height:400px;"></div>
    ...
    <script src="./js/echarts.js"></script>
    <script type="text/javascript">
        require.config({
            paths: {
                echarts: './js/dist'
            }
        });
        require(
            [
                'echarts',
                'echarts/chart/line',   // 按需加载所需图表，如需动态类型切换功能，别忘了同时加载相应图表
                'echarts/chart/bar'
            ],
            function (ec) {
                var myChart = ec.init(document.getElementById('main'));
                var option = {
                    ...
                }
                myChart.setOption(option);
            }
        );
    </script>
</body>
```
对于官网上的要把主文件放在最后的方式，我发现在有些时候并并不是定死的。我在Oracle ADF中就能很好的完成项目的开发，即使我以最正常的方式引入echarts.js(压缩过的)。

第三种就是傻瓜式的引入单文件，其实对于很多时候AMD模块化项目与CMD规范型项目对于我这种模块化新手来说或许还要补习补习。不过对于其引入的理解其关键点在按需引入。第三种就没有做到这种。一般来说其实引入js并不会带来什么性能上的明显下降。但是在某些时候无论是开发还是正式产品往往会带来一些你不想看到的麻烦 （例如：开发的时候Oracle ADF 在js或者html编辑模式下引入js的文件对于其自动提示有着至关重要的影响。正式产品来说，如果你不注意引入与压缩js，随着项目中js的增多，会有着逐渐明显的影响）。

所以，在如今模块的时代，我们能够做的就是把模块化能够在能够践行的情况下尽量实施。无论是css 还是 js 按需引入总比囫囵吞枣式的让服务器解析要好的多。
![Alt text](http://ogu9js0qs.bkt.clouddn.com/echartsMap.png "echarts")这是模块话的目录结构。
![Alt text](http://ogu9js0qs.bkt.clouddn.com/echartsMap2.png "echarts detail")这是模块话的目录结构的细节。

这里先吐槽下 zrender的作者，因为echarts 2.2.7必须依赖2.1.1+的zrender，可是我找半天都没找到他分支下的2.1.1。放入3+ zrender又发现目录结构发生了变化，路径搞的我醉醉的。其实放2.0+的zrender应该没什么问题，至少在我开发的map过程中是没有什么error出现的。

还好是国产，不然......。

其实，今天开发中遇到唯一不能满意的就是echarts中tiptool中不能锲入饼图，我想用qitps2看下有没有可能实现的方案，其中的formatter的回调函数或许可以利用下，不过我发现官网上所说的toHTML在2.2.7 echarts 中并不能用。在js中写入html字符串在head上加script的方式并不可行，具体原因为止。可能是vm不支持这种层层嵌套的方式来加载js解释器。

```html
<!DOCTYPE html PUBLIC "-//W3C//DTD HTML 4.01 Transitional//EN" "http://www.w3.org/TR/html4/loose.dtd">
<html>
<head>
<meta http-equiv="Content-Type" content="text/html; charset=UTF-8">
<title>Insert title here</title>
<script type="text/javascript" src="./common/echarts/jquery-1.7.2.min.js"></script>
<script type="text/javascript" src="./common/echarts/esl.js" ></script>
<script type="text/javascript">
var option1;
var option2;
var chart1;
var echart1;
		require.config({
			packages:[
				{  
				    name: 'zrender',  
				    location: './common/zrender2/src', // zrender与echarts在同一级目录  
				    main: 'zrender'  
				},  
				{  
				    name: 'echarts',  
				    location: './common/echarts-2.2.7/src',  
				    main: 'echarts'  
				}      
			]
		});

		// 按需加载(需要用什么图标就引入包，如柱状图：'echarts/chart/bar',)
		require(
			[
			 	'echarts',
			 	'echarts/config',
			 	'echarts/chart/bar',
			 	'echarts/chart/pie',
			 	'echarts/chart/map'
			],
			function (ec) {
				chart1=document.getElementById('main');
				echart1 = ec.init(chart1);
				var chart2  = document.getElementById('chart2');
				var echart2 = ec.init(chart2);
				var categories = [];
				var values = [];
				var orgs=[];
				var orgVal=[];
				var mapData=[];
				var pieData=[];
				var nameMap={};
				 option1 = {
						backgroundColor: 'rgba(187,203,243,0.5)',
					    tooltip : {
					    	backgroundColor:'rgba(187,203,243,0.5)',
					    	borderWidth:0,
					    	borderColor:'#000',
					    	transitionDuration:0.5,
					        trigger: 'item',
					        formatter :function (params, ticket, callback) {
					            $.get('map?country=' + params.name, function (content) {
					                callback(ticket, "<div style='border:0px;height:250px;width:300px;background-color:rgba(187,203,243,0.5)'></div>");
					            });
					            return 'Loading';
					        }
					    },
					    legend: {
					        orient : 'vertical',
					        x : 'left',
					        data:[{
						                name:'normal',
						                textStyle:{
						                    fontSize:12,
						                    fontWeight:'bolder',
						                    color:'#7CFC00'
						                },
						                icon:'stack'
						            },
						            {
						                name:'risk',
						                textStyle:{
						                    fontSize:12,
						                    fontWeight:'bolder',
						                    color:'#EEEE00'
						                },
						                icon:'stack'
						            },
						            {
						                name:'overdue',
						                textStyle:{
						                    fontSize:12,
						                    fontWeight:'bolder',
						                    color:'#FF0000'
						                },
						                icon:'stack'
						            }]
					    },
					    dataRange: {
					        x: 'left',
					        y: 'bottom',
					        splitList: [
					            {start:1, end: 1, label: 'normal',color: 'green'},
					            {start: 0, end: 0, label: 'risk', color: 'yellow'},
					            {start: -1, end: -1, label: 'overdue', color: 'red'}
					        ],
					        color:['green','yellow','red']  
					    },
					    series : [
					        {
					            name: '世界地图',
					            type: 'map',
					            mapType: 'world',
					            roam: true,
					            selectedMode : 'single',
					            itemStyle:{
					                normal:{label:{show:false}},
					                emphasis:{label:{show:true}}
					            },
					            data:mapData,
					            // 自定义名称
					            nameMap : nameMap
					        },
					        {
					            name:'该国财务概况',
					            type:'pie',
					            roseType : 'area',
					            tooltip: {
					                trigger: 'item',
					                formatter: "{a} <br/>{b} : {c} ({d}%)"
					            },
					            center: [document.getElementById('main').offsetWidth - 1200, 225],
					            radius: [20, 60],
					            data:pieData // select data from table where country_name -以国家为条件实时读取
					        },
					    ],
					    animation: false,
					    color:['green','yellow','red']
					};
				 option2 = {
						backgroundColor: 'rgba(223,230,249,0.5)',
					    title : {
					        text: 'World Population (2010)',
					        subtext: 'from United Nations, Total population, both sexes combined, as of 1 July (thousands)',
					        x:'center',
					        y:'top'
					    },
					    tooltip : {
					        trigger: 'item',
					        backgroundColor:'rgba(187,203,243,0.5)',
					    	borderWidth:0,
					    	borderColor:'#000',
					    	transitionDuration:0.8,
					        formatter : function (params,ticket,callback) {
					        	 $.get('map?country=' + params.name, function (content) {
					        			var total=0;
							        	var percent;
							        	var risk=0;
							        	var normal=0;
							        	var overdue=0;
							        	var pieData = content.pieData;
							           var str = "<div style='border:0px;height:250px;width:300px;background-color:rgba(187,203,243,0.5)'>"
							           +"<div style='float:left; font-style:italic'>";
							           for(var i = 0; i<pieData.length; i++){
							        	   if(pieData[i].name=='normal'){
							        		   risk = parseInt(pieData[i].value,10);
							        	   }
							        	   if(pieData[i].name=='risk'){
							        		   normal = parseInt(pieData[i].value,10);
							        	   }
							        	   if(pieData[i].name=='overdue'){
							        		   overdue = parseInt(pieData[i].value,10);
							        	   }
							        	   total += parseInt(pieData[i].value,10);
							           }

							           str +=(risk/total * 100).toFixed(1)+" %<br/>"+(normal/total * 100).toFixed(1)+"%<br/>"+(overdue/total * 100).toFixed(1)+" %";
							           str +="</div><div><ul>";
							           for(var i = 0;i<pieData.length; i++){
							        	   str +="<li>"+pieData[i].name+" : "+pieData[i].value+"</li>";
							           }
							           str = str+"</ul></div></div>"
					                 callback(ticket, str);
					             });
					             return 'Loading';
					        }
					    },
					    toolbox: {
					        show : true,
					        orient : 'vertical',
					        x: 'right',
					        y: 'center',
					        feature : {
					            mark : {show: true},
					            dataView : {show: true, readOnly: false},
					            restore : {show: true},
					            saveAsImage : {show: true}
					        }
					    },
					    legend: {
					        orient : 'vertical',
					        x : 'left',
					        data:[{
						                name:'normal',
						                textStyle:{
						                    fontSize:12,
						                    fontWeight:'bolder',
						                    color:'#7CFC00'
						                },
						                icon:'stack'
						            },
						            {
						                name:'risk',
						                textStyle:{
						                    fontSize:12,
						                    fontWeight:'bolder',
						                    color:'#EEEE00'
						                },
						                icon:'stack'
						            },
						            {
						                name:'overdue',
						                textStyle:{
						                    fontSize:12,
						                    fontWeight:'bolder',
						                    color:'#FF0000'
						                },
						                icon:'stack'
						            }]
					    },
					    dataRange: {
					        x: 'left',
					        y: 'bottom',
					        splitList: [
					            {start:1, end: 1, label: 'normal',color: 'green'},
					            {start: 0, end: 0, label: 'risk', color: 'yellow'},
					            {start: -1, end: -1, label: 'overdue', color: 'red'}
					        ],
					        color:['green','yellow','red']  
					    },
					    series : [
					        {
					            name: 'World Population (2010)',
					            type: 'map',
					            mapType: 'world',
					            roam: true,
					            mapLocation: {
					                y : 60
					            },
					            itemStyle:{
					                emphasis:{label:{show:true}}
					            },
					            showLegendSymbol:true,
					            markPoint:{
					            	clickable:true,
					            	symbol:'emptyCircle',
					            	data:mapData
					            },
					            data:mapData
					        },
					        {
					            name:'该国财务概况',
					            type:'pie',
					            roseType : 'area',
					            tooltip: {
					                trigger: 'item',
					                formatter: "{a} <br/>{b} : {c} ({d}%)"
					            },
					            center: [document.getElementById('main').offsetWidth - 1200, 225],
					            radius: [20, 60],
					            data:pieData
					        }
					    ],
					    animation: false,
					    color:['green','yellow','red']  
					};

				/*
				加事件的两种方法
				var ecConfig= require('echarts/config');  
				chart1.on(ecConfig.EVENT.MAP_SELECTED, function (param){

                          alert("a");
						});

				chart1.on('mapSelected', function (param){
					   alert("a");

				}); */


				echart1.setOption(option1);
				echart2.setOption(option2);
				 var ecConfig = require('echarts/config');  
				 echart1.on(ecConfig.EVENT.MAP_SELECTED, function (param) {  
				        var selected = param.selected;
		                for (var p in selected) {
		                    if (selected[p]) {
		                    	//从这里开始掉用服务。或者先初始化重新渲染一个地图    点击跳转  第一种可以自定义map 那个国家的URL可以预先写在json数据中传过来，第二种 ajax 点击的时候再去数据库读取一次，或者从后台读取一次
		                        alert(p);
		                    }
		                }

				    }
				 )  
			}
		);


function drawMap(){
	debugger;
	/*
	$.ajaxSettings.async = false;
	// 加载数据
	$.getJSON("map", function (json) {
		console.log(json)
		//mapData = json.mapData;
		//pieData = json.pieData;
		/*
		categories = json.mapData;
		values = json.values;
		orgs=json.orgs;
		orgVal=json.orgVal;
		});
	*/
	 $.ajax({  
         url : "map",//springmvc的controller的请求路径
         type : "post",  
         async : false, //同步执行  
         data : {},  
         dataType : "json", //返回数据形式为json  
         success : function(data) {  
             if (data) {  
                 //请求成功将数据设置到相应的位置上
                 option1.series[0].data = data.mapData;  
                 option1.series[1].data = data.pieData;  
                 echart1.setOption(option1);  
             }  
         },  
         error : function(xmlHttpRequest,errorMsg,exceptionObject) {  
             alert("requestError");  
             alert('xmlHttpRequest>>>>>' + xmlHttpRequest + ' errorMsg>>>>>' + errorMsg + ' exceptionObject>>>>>' + exceptionObject);
             chart1.hideLoading();  
         }  
     });  
 }


	</script>
</head>
<body>
    <div style="position: center"><label>search1</label><input id="search_map_1" type="text" /><input type="submit" value="Search" onclick="drawMap()"/></div>
	<div id="main" style="height:500px;">
	</div><br/><hr>
	<div style="position: center"><label >search2</label><input id="search_map_2" type="text" /><input type="submit" value="Search" onclick="drawMap()"/></div>
	<div id="chart2" style="height:500px;">
	</div>
</body>
</html>

```
由于是自己实验，所以很多变量都没有自己推敲，等到有时间会把一个完整版本上上去，当然也会带绚丽的tips。这段代码只是把有台交互和一些map上简易的功能配置完成了。接下来的时间需要完善tips，优化前台全局变量（仔细推敲），完善后台逻辑（加密解密，权限控制，模块化，性能，数据组装的工具化等等）。
