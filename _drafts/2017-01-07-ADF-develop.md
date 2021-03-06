---
layout: post
title:  "Oracle ADF 问题总结"
date:   2017-01-07 11:33:36 +86
tag: Oracle,adf,experience,develop
categories: coderoad
---
摘自 [博客](http://blog.csdn.net/nicklsq/article/details/16845095 "csnd") .
	ORACLE ADF 问题总结
	1. 现象：Lov或者页面ADF Table 数据显示有重复行，或者当前行用鼠标无法选择；
	解决：检查vo上是否有主键
	2. 现象：页面字段出现无法更新
	解决：检查VO和EO上的该字段是否设置成了不可更新
	3. 现象：VO上定义的绑定变量会在lov上出现
	解决：Bind Variable的Control Hints 里面的Display Hint 设置为：Hide
	4. 现象：控件A的PartialTriggers选择别的控件B选不上
	解决：检查B的父容器是否有ID
	5. 现象：报错PageDef Not find
	解决：检查DataBinding.cpx里面定义的PageDef是否有误！
	6. 现象：某页面PageDef Not find文件vo报红
	解决：检查DataBinding.cpx里面的BC4JDataControl，如果BC4JDataControl有误，那么坚持AM上面的Configurations是否有两项：local和shared
	7. 现象：lov数据死活出不来
	解决：先看sql，如果没问题，检查lov的vo上是否有参数，如果有参数，看看View Accessors上View Definition，然后看Row-level bind values exits是否打钩，没特殊需要，不要打钩
	8. 现象：统计vo上的条数用vo.getRowCount();
	建议：用vo.getEstimatedRowCount(),这个方法用的是select  (*) from (VOSQL),而vo.getRowCount()用的是遍历vo，会使得系统很慢
	9. 现象：页面文件出现文字
	建议：用vo上的字段名，即:定义在资源文件里面，便于国际化
	10. 现象：vo每个字段上面有资源文件的路径(ADF早期版本)
	建议：vo上每个字段上面只定义key，资源文件路径在vo源文件下面统一定义，要不页面显示时，针对每个字段做一次I/O操作，导致页面初始化巨慢！
	11. 现象：vo上的参数(绑定变量)过多
	建议：尽量减少，否则会使得vo查询变慢
	12. 现象：如何实现start  with connect by的树状查询？
	解决：在vo上做一个绑定变量，用这个绑定变量在vo的sql上拼start with ，然后在manager bean 或者am里面把输入的查询条件赋给绑定变量就ok了
	13. 现象：如何刷新页面对应的vo？
	解决：先在PageDef里面找vo的实例，然后然后在am里面调用这个vo的实例，然后利用vo. executeQuery()，查询次就ok了，查询时不用管vo的过滤情况
	14. 现象：vo里面的sql有select   RowID
	建议：没有特殊的需求（针对数据库是9i的），不要在vo的sql写select   rowid，其实可以把rowid  弄个别名的，例如row_id
	
	15. 现象： 发布时从svn上面update下来后直接部署
	建议：update后，重启下jdev(针对.2版本以前的，以后的不清楚)，然后手动编译整个项目，然后再发布
	16. 现象： 程序在别人电脑上运行没问题，在自己电脑上有问题
	建议： 首先重新手动编译下工程，如果不行的话，把工程下面的class文件夹删除了，重启jdev试试
	17. 现象： 程序在自己电脑上没问题，发布到服务器上的weblogic，就出错，或者感觉没发布成功，重启weblogic也不行
	解决： 清除domain目录下的servers/AdminServer/tmp文件里面的内容，然后重启weblogic
	18. 现象： weblogic安装adf runtime library后，部署adf应用报class not find
	解决：把adf-share-wls.jar 复制到<wls_home>\jdeveloper\modules\oracle.adf.share_11.1.1
	19. 现象： 在weblogic上定义DataSource时要注意，JNDI的名字
	建议： JNDI定义什么呢？首先看我们adf程序的Model属性里面的Business Components定义的Connection的名字，如果这里的名字为AAA，那么AM上的Configurations里面的 Configuration属性，Connection  by，我们要选为JDBC DataSource，然后DataSource Name会自动为java:comp/env/jdbc/AAADS ，webloigc上面的data source 的jndi就是jdbc/AAADS，这个一定不能弄错了，否则datasource就连不上
	20. 现象： 如果要在ManagerServer上运行adf程序
	建议： manager server不能在console里面启动，要在domain下面的bin目录用startManagedWebLogic.sh AppServer http://localhost:7101命令启动，其中AppServer是ManagerServer的名字，7101是AdminServer的端口,然后把Weblogic上面重要的3个 library的目标设置到ManagerServer，并把DataSource的目标设置到ManagerServer
	21. 现象： 如何使普通的独立版的weblogic能运行adf程序
	解决： 下载安装jdev，只装runtime 即可，然后weblogic的common/bin目录下运行./config.sh ，修改domain，然后选择adfruntime ，安装后记得增加缺省的jar包
	22. 现象：如何对ADF程序进行压力测试
	建议： 用LoadRunner
	23. 现象： ADF程序url带有一个_adf.ctrl-state的后缀，即：url是动态的，LoadRunner脚本如何改写？
	解决：利用下面的函数得到每个页面或则task_flow 的_adf.ctrl-state
	web_reg_save_param("login",
	"LB/IC=login.jspx?_adf.ctrl-state=",
	"RB/IC=\"",
	"Ord=1",
	"Search=Body",
	"RelFrameId=1",
	LAST);
	第一个引号里面的为得到的服务器返回的值
	LB/IC ：服务器返回值的起始字符串
	RB/IC ：服务器返回值的结束字符串，\为转义符
	利用上面的函数我们可以把类似login.jspx?_adf.ctrl-state=2342322232_22”里面的_adf.ctrl-state的值 2342322232_22保存在变量login里面，然后修改url即可，例如：
	web_submit_data("login.jspx_2",
	"Action={url}/expcontrol/faces/login.jspx?_adf.ctrl-state={login}",
	………………
	注意每个独立的页面和task_flow的url都 要如此的修改，负责录制的脚本没什么用，测试没法进行
	24. 现象： A服务器上ADF程序运行没问题，切换到B服务器上报：java.sql.SQLDataException, msg=ORA-01882: timezone region  not found
	原因：服务器上的时区不在数据库V$TIMEZONE_NAMES有效的time zone里面
	解决： 修改setDomainEnv.sh ： JAVA_OPTIONS="${JAVA_OPTIONS}" => JAVA_OPTIONS="${JAVA_OPTIONS} -Duser.timezone=Asia/Shanghai”
	