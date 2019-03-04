####1.静态文件 
项目中的CSS、图片、js都是静态文件。一般会将静态文件放到一个单独的目录中，  
以方便管理。在html页面中调用时，也需要指定静态文件的路径，  
Django中提供了一种解析的方式配置静态文件路径。静态文件可以放在项目根目录下，  
也可以放在应用的目录下，由于有些静态文件在项目中是通用的，所以推荐放在项目的根目录下，方便管理。  

**Django中的使用步骤：**  
> 1.在项目中的settings.py文件中定义静态文件的引用路径和存放的物理目录

	STATIC_URL = '/static/'   应用路径，可以隐藏今天文件的真实路径
	STATICFILES_DIRS = [      真实的存放物理目录
	    os.path.join(BASE_DIR, 'static'),
	]  
 
> 2.在项目根目录下创建static目录，再创建images、css、js等目录 
> 3.在模板文件中使用 

代码示例： 

views.py中的代码： 

```python

	def static_test(request):
	return render(request, 'booktest/static_test.html', {})

```  

模板文件中的代码： 

```html

	<html>
	<head>
	    <title>静态文件</title>
		<style>
			img{
				width:160px
				height:250px
			}
		</style>
	</head>
	<body>
	修改前：<img src="/static/images/meinv1.jpg"/>
	<hr>
	修改后：<img src="/abc/images/meinv1.jpg"/>
	<hr>
	动态配置：
	{%load static from staticfiles%}
	<img src="{%static 'images/meinv1.jpg' %}"/>
	</body>
	</html>
``` 

settings.py配置如下： 

	STATIC_URL = '/static/'   应用路径，可以隐藏今天文件的真实路径
	STATICFILES_DIRS = [      真实的存放物理目录
	    os.path.join(BASE_DIR, 'static'),
	]    


运行效果如下 ：   
![](https://i.imgur.com/90Tp2JS.png)   

右键查看页面源代码如下： 
![](https://i.imgur.com/L6YeXlJ.png)  

注意：  
1. 为了安全可以通过配置项隐藏真实图片路径，在模板中写成固定路径，后期维护太麻烦，  
可以使用static标签，根据配置项生成静态文件路径。    
2. 这种方案可以隐藏真实的静态文件路径，但是结合Nginx布署时，  
   会将所有的静态文件都交给Nginx处理，而不用转到Django部分，所以这项配置就无效了。


