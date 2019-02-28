####1.视图函数 
> 1. Django中MVT中的V,指的就是Django中的视图函数，一般定义在应用的views.py模块中
> 2. 视图函数的第一个参数必须为HttpRequest实例，可以是其子类，比如WSGIHttpRequest,还可以包含参数如：  
	- 通过正则表达式组获得的关键字参数
	- 通过正则表达式组获取的位置参数  
> 3.视图函数必须返回一个HttpResponse实例，JsonResponse和HttpResponseRedirect是其常用的2个子类 

####2.URL配置 
> 当用户在浏览器输入url到Django成功返回需要完成的配置：  

	1. 首先要在项目的urls.py中添加包含应用的urls配置  
![](https://i.imgur.com/y8x9ChR.png) 

	2. 在具体应用urls.py中配置具体的url和对应的视图函数  
![](https://i.imgur.com/EGgYg6F.png)

####3.内置错误视图   
> 默认的错误视图开发模式下是看不到的，需要修改如下：
 
	修改settings.py中的DEBUG=False 和 ALLOWED_HOSTS=['*',]

> Django内置处理Http错误的视图，主要错误及视图包括：
  
**404错误**：page not found视图   

    当请求地址经过django框架url匹配后，没有找到匹配的正则表达式，Django默认404视图函数，  
	这个视图函数会调用404.html模板进行渲染，视图传递变量request_path给模板，表示导致错误的URL。
	不想用Django默认404.html模板，也可以在项目目录自定义404.html模板 

![](https://i.imgur.com/W0Up8Cy.png) 

**500错误**：server error视图    
	
	当视图函数中的代码运行发生错误，Django会默认调用500视图函数， 
	这个视图函数会调用500.html模板进行渲染， 
	不想用Django默认404.html模板，也可以在项目目录自定义500.html模板     

![](https://i.imgur.com/tYZxjn4.png)   




 
		
