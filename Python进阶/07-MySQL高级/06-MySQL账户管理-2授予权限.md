##授予权限 
需要使用实例级账户登录后操作，以root为例

主要操作包括：

* 查看所有用户
* 修改密码
* 删除用户  

####1. 查看所有用户
* 所有用户及权限信息存储在mysql数据库的user表中  
* 查看user表的结构    
  desc user;   
 
* 主要字段说明：  
	* Host表示允许访问的主机   
	* User表示用户名   
    * authentication_string表示密码，为加密后的值      
    
查看所有用户

```sql

	mysql> select host,user,authentication_string from user;
	+-----------+------------------+-------------------------------------------+
	| host      | user             | authentication_string                     |
	+-----------+------------------+-------------------------------------------+
	| localhost | root             | *E74858DB86EBA20BC33D0AECAE8A8108C56B17FA |
	| localhost | mysql.sys        | *THISISNOTAVALIDPASSWORDTHATCANBEUSEDHERE |
	| localhost | debian-sys-maint | *EFED9C764966EDB33BB7318E1CBD122C0DFE4827 |
	+-----------+------------------+-------------------------------------------+
	3 rows in set (0.00 sec) 
```   

####2. 给用户授权  
* 需要使用超级账户登录后操作，以root为例
* 常用权限主要包括：create、alter、drop、insert、update、delete、select
* 如果分配所有权限，可以使用all privileges   

授权   
   
	grant 权限列表 on 数据库 to '用户名'@'访问主机' identified by '密码';  

	mysql 8.0 之后以上会报错，可执行
	grant 权限列表 on 数据库 to '用户名'@'访问主机';  

	注意：all privileages 表示所有权限  

---    
示例1:   

```sql
 
     创建一个laowang的账号，密码为123456，只能通过本地访问,      
	 并且只能对jing_dong数据库中的所有表进行读操作
	    
	 step1：使用root登录
	       mysql -uroot -p
	       回车后写密码，然后回车  
    
     step2：创建账户并授予所有权限
    	   grant select on jing_dong.* to 'laowang'@'localhost' identified by '123456'; 
    
	       说明
	    
	      * 可以操作python数据库的所有表，方式为:jing_dong.*
	      * 访问主机通常使用 百分号% 表示此账户可以使用任何ip的主机登录访问此数据库
	      * 访问主机可以设置成 localhost或具体的ip，表示只允许本机或特定主机访问 
    
    	  查看用户有哪些权限
          show grants for laowang@localhost;  
    
     step3：退出root的登录
    	    quit 
    
     step4：使用laowang账户登录 
	        mysql -ulaowang -p 
	        回车后写密码，然后回车   
```
   
登录后效果如下：  
![](https://i.imgur.com/1WNl0vl.png)   

---
示例2：   
	创建一个laoli的账号，密码为12345678，可以任意电脑进行链接访问,   
	并且对jing_dong数据库中的所有表拥有所有权限   

	grant all privileges on jing_dong.* to "laoli"@"%" identified by "12345678"   
   
效果如下：
![](https://i.imgur.com/9KZLFXA.png)   
![](https://i.imgur.com/TUMHK6x.png)