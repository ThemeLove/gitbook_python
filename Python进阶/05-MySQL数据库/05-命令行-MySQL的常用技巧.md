####1.命令行下更好的显示mysql查询结果  
	缘由：直接进行mysql查询时，有时查询结果字段较多，命名行下显示效果很不友好；   
		 可以直接以 \G 结束sql语句，例如：select user,host from user\G
	


```sql  

	mysql> select user,host from user\G
	*************************** 1. row ***************************
	user: root
	host: %
	*************************** 2. row ***************************
	user: mysql.infoschema
	host: localhost
	*************************** 3. row ***************************
	user: mysql.session
	host: localhost
	*************************** 4. row ***************************
	user: mysql.sys
	host: localhost
	*************************** 5. row ***************************
	user: root
	host: localhost
	5 rows in set (0.00 sec)
	
	mysql>

```