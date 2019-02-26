####1.连接远程mysql数据库 
	sudo mysql -h 远程主机ip -P 远程主机端口 -u远程mysql用户名 -p   
  
####2.配置Django数据库为MySql 
	1. 修改项目文件夹下的settings.py文件中的DATABASES配置项
	
```python

	DATABASES = {
		'default':{
			'ENGINE':'django.db.backends.mysql', # 表示使用mysql数据库
			'NAME':'django', # 使用mysql数据库的名字,数据库必须事先手动创建
			'USER':'root',	# mysql的用户
			'PASSWORD':'mysql', # 用户对应的密码
			'HOST':'localhost', # 指定mysql数据库所在的电脑ip
			'PORT':'3306' # 指定mysql的端口号
		}
	}  
```	

	2. 修改项目文件夹下的__init__.py中添加如下内容 ：   
		import pymysql   
		pymysql.install_as_MySQLdb()    

####3.配置实时查看mysql数据库日志 
	1. 修改mysql配置文件：/etc/mysql/mysql.conf.d/mysqld.conf 
	   把68，69行前面的#去除: 
	   general_log_file		=   /var/log/mysql/mysql.log    
	   general_log		=   3   
![](https://i.imgur.com/sE4xJG3.png)   
 
	2. 重启mysql服务 
	   sudo service mysql restart 
  
    3. 用tail命令打开mysql日志文件就可以实时观察mysql日志 
	   sudo tail -f /var/log/mysql/mysql.log     

####4.迁移文件 
	1. 生成迁移文件
	   python manage.py makemigrations 
	2. 迁移
	   python manage.py migrate   

####5.Django后台管理相关
	1. 本地化:修改settings.py配置 
	   LANGUAGE_CODE = "zh-hans"
	   TIME_ZONE = 'Asia/Shanghai' 
	2. 创建管理员
	   python manage.py createsuperuser
	3. 启动服务器
	   python manage.py runserver  
	4. 指定ip和端口号启动runserver 
       python manage.py runserver ip:port 
	   注意：ip必须要在settings.py中给的ALLOWED_HOSTS中配置,且运行是必须是本机，即ip不能是远程ip
	5. 进入Django默认后台管理页面 
	   浏览器访问：localhost:8000/admin    

####6.Django 视图 
	1.定义视图函数
	2.进行url的配置     

####7.进入django的shell环境   
	python manage.py shell   
 
####8.Django模型的增删改查 
	1. 添加一条记录到数据库 :	 
		b = BookInfo()
		b.btitle = "天龙八部"
		b.bpub_date = datetime(1990,1,1) 
		b.save() 
	2. 删除一条记录 ： delete()  
		b.delete()
		b.save()  
	
	3. 改 
	    b.btitle = "倚天屠龙记" 
		b.save() 
	4. 查找
	   a.查找一条记录
		 模型类名.objects.get(条件) 
		 例： BookInfo.objects.get(id=1)  
	   b.查找所有记录 
	     模型类名.objects.all() 
	   c.查找一对多 
		 一类对象名.多类模型名小写_set.all()	   

####9.Django中的页面重定向   
	1. 方法1： 构造HttpResponseRedirect并返回
	   导入HttpResponseRedirect：from django.http import HttpResponseRedirect
	   构造HttpResponseRedirect对象并返回：return HttpResponseRedirect('/index')  
	2. 方法2： 使用djano的简写 
	   导入redirect模块：from django.shortcuts import redirect 
	   返回：return redirect('/index')  

