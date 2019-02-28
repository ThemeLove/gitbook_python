#HttpResponse对象 
视图在接收请求并处理后，必须返回HttpResponse对象或子对象。在django.http模块中定义了HttpResponse对象的API。   
HttpRequest对象由Django创建，HttpResponse对象由开发人员创建  

#子类JsonResponse 
	在浏览器中使用javascript发起ajax请求时，返回json格式的数据，此处以jquery的get()方法为例。  
	类JsonResponse继承自HttpResponse对象，被定义在django.http模块中，创建对象时接收字典作为参数。  

#子类HttpResponseRedirect  
	当一个逻辑处理完成后，不需要向客户端呈现数据，而是转回到其它页面，  
	如添加成功、修改成功、删除成功后显示数据列表，而数据的列表视图已经开发完成，  
	此时不需要重新编写列表的代码，而是转到这个视图就可以，此时就需要模拟一个用户请求的效果，  
	从一个视图转到另外一个视图，就称为重定向。 

	Django中提供了HttpResponseRedirect对象实现重定向功能，这个类继承自HttpResponse，  
	被定义在django.http模块中，返回的状态码为302。   

> 重定向简写函数redirect在django.shortcuts模块中为重定向类提供了简写函数redirect。 

```python 

	1）修改booktest/views.py文件中red1视图，代码如下：
	
	from django.shortcuts import redirect
	...
	def red1(request):
	    return redirect('/') 
```