####1.Admin站点 
内容发布的部分由网站的管理员负责查看、添加、修改、删除数据，开发这些重复的功能是一件单调乏味、  
缺乏创造力的工作，为此，Django能够根据定义的模型类自动地生成管理模块。  
> 1.创建管理员的用户名和密码 
 	
	python manage.py createsuperuser  

> 2.在对应的应用中注册模型类  

	from django.contrib import admin
	from . import models
	
	admin.site.register(models.AreaInfo)    

> 3.进入Django自带admin管理后台    
	
	运行服务器： 
	python manage.py runserver [ip:端口] 
	
	进入管理后台： 
		1.浏览器输入：
			localhost:8000/admin   
  		2.输入账号密码：  

####2.控制管理页展示
类ModelAdmin可以控制模型在Admin界面中的展示方式，主要包括在列表页的展示方式、添加修改页的展示方式。

**管理类有两种使用方式：**   
> 1.注册参数   

	class AreaAdmin(admin.ModelAdmin):
	    pass

	admin.site.register(AreaInfo,AreaAdmin)
	  
> 2.装饰器     

	@admin.register(AreaInfo)
	class AreaAdmin(admin.ModelAdmin):
	    pass   


####3.列表页选项   
> 1. **list_per_page**:每页中显示多少条数据，默认为每页显示100条数据 
 
> 2. **action_on_top**:顶部编辑选项 
 
> 3. **action_on_bottom**:底部编译选项  
 
> 4. **list_display**:配置显示的列选项 
 
> 5. **search_fields**:配置搜索框 
 
> 6. **list_filter**:配置右侧过滤选项  
 
> 7. 模型类中的第一个参数verbose_name:配置对应字段在管理后台的列标题  

####4.编辑页选项 
> 1.fields:配置编辑页字段显示顺序    

> 2.fieldsets:分组显示   

	语法
	fieldset=(
	    ('组1标题',{'fields':('字段1','字段2')}),
	    ('组2标题',{'fields':('字段3','字段4')}),
	)
> 3.inlines: 配置管理对象显示效果：

	在一对多的关系中，可以在一端的编辑页面中编辑多端的对象，嵌入多端对象的方式包括表格、块两种。  
	类型InlineModelAdmin：表示在模型的编辑页面嵌入关联模型的编辑。   
	子类TabularInline：以表格的形式嵌入。子类StackedInline：以块的形式嵌入。  

####5.配置截图及运行效果    
> **1.应用admin.py中的配置：** 

![](https://i.imgur.com/Y6QuV8l.png) 

> **2.应用models.py中的配置**  

![](https://i.imgur.com/VYj1Kbm.png)	 

> **3.运行截图：**   
	
 列表页显示效果： 

![](https://i.imgur.com/TgeYPHp.png)  

 编辑页显示效果：   

![](https://i.imgur.com/aWnz8ad.png)    


####5.重写Django后台自带模板  
1.Django后台的模板也可以重写，具体模板在 安装目录/site-packages/django/contrib/admin/templates/admin 下  
2.在templates模板下创建admin目录，再将要重写的模板从django安装目录下copy过来，进行改动即可  

![](https://i.imgur.com/x6Z7Y0m.png)




	
