####1.视图是什么？  
通俗的讲，视图就是一条SELECT语句执行后返回的结果集。     
特点： 

1. 视图是对若干张基本表的引用，查询语句执行的结果，一张虚表，不影响真实的表 
2. 不存储具体的数据（基本表数据发生了改变，视图也会跟着改变） 
3. 不实际占用硬盘空间，只有使用时才创建 
4. 视图只能用来查询，不能用来增删改

视图的作用：    

1. 提高了重用性，就像一个函数   
2. 对数据库重构，却不影响程序的运行    
3. 提高了安全性能，可以对不同的用户   
4. 让数据更加清晰     

####2.定义视图 
	create view 视图名称 as select 语句;
	例：视图名称建议以v_开头  
	    create view v_goods_info as select g.*,c.name as cate_name,b.name as brand_name from goods as g left join goods_cates as c on g.cate_id=c.id left join goods_brands as b on g.brand_id=b.id;
	
####3.查看视图 
	查看表会将所有的视图也列出来  
	例：show tables; 

####4.使用视图   
	视图的用途就是查询 
	例：select * from v_goods_info;

####5.删除视图 
	drop view 视图名称;  
	例：drop view v_goods_info;

 
	
####7.视图demo 
![](https://i.imgur.com/1WUKwHO.png)  
![](https://i.imgur.com/wQd7j9g.png)