####1.变量 
  模板变量的作用是计算并输出，变量名必须由字母、数字、下划线（不能以下划线开头）和点组成。

语法如下：

	{{变量}}
例：
当模版引擎遇到点如book.title，会按照下列顺序解析：

> 1.字典book['title']  
> 2.先属性后方法，将book当作对象，查找属性title，如果没有再查找方法title()  
> 3.如果是格式为book.0则解析为列表book[0] 

注意：   
	1.如果变量不存在则插入空字符串''   
	2.在模板中调用方法时不能传递参数    

代码示例：  

views.py文件如下： 

```python

	def temp_var(request):
	    dict={'title':'字典键值'}
	    book=BookInfo()
	    book.btitle='对象属性'
	    context={'dict':dict,'book':book}
	    return render(request,'booktest/temp_var.html',context)   

``` 

temp_var.html模板文件如下： 

```python

	<html>
	<head>
	    <title>模板变量</title>
	</head>
	<body>
	模板变量：<br/>
	{{dict.title}}<br/>
	{{book.btitle}}<br/>
	</body>
	</html>
``` 

运行效果如下：  

![](https://i.imgur.com/TX6tyNL.png)    


####2.标签  
语法如下：
  
&#123;&#37;代码段&#37;&#125;


for标签语法如下： 

>	{%for item in 列表%}    
>	循环逻辑    
>	{{forloop.counter}}表示当前是第几次循环，从1开始    
>	&#123;&#37;empty&#37;&#125;   
>	列表为空或不存在时执行此逻辑    
>	{%endfor%}    

if标签语法如下：

	{%if 条件%}
	逻辑1
	{%elif 条件%}
	逻辑2
	{%else%}
	逻辑3
	{%endif%}

比较运算符如下：

> 注意：运算符左右两侧不能紧挨变量或常量，必须有空格。

	==
	!=
	<
	>
	<=
	>= 

布尔运算符如下：

	and
	or
	not  

代码示例： 

views.py文件如下：
 
```python

	from booktest.models import BookInfo
	def temp_tags(request):
	    context={'list':BookInfo.objects.all()}
	    return render(request,'booktest/temp_tag.html',context)  
``` 

temp_tag.html模板文件如下： 

```python 
  
	<html>
	<head>
	    <title>标签</title>
	</head>
	<body>
	图书列表如下：
	<ul>
	    {%for book in list%}
	        {%if book.id <= 2%}
	            <li style="background-color: red;">{{book.btitle}}</li>
	        {%elif book.id <= 3%}
	            <li style="background-color: blue;">{{book.btitle}}</li>
	        {%else%}
	            <li style="background-color: green;">{{book.btitle}}</li>
	        {%endif%}
	    {%endfor%}
	</ul>
	</body>
	</html> 
```
  
运行效果如下：  

![](https://i.imgur.com/KpNupVh.png)  


####3.过滤器 
语法如下:  

	变量|过滤器:参数

> 使用管道符号|来应用过滤器，用于进行计算、转换操作，可以使用在变量、标签中。
> 如果过滤器需要参数，则使用冒号:传递参数。	

常用过滤器： 

* length ：返回字符串包含字符的个数，或列表、元组、字典的元素个数。

* default：如果变量不存在时则返回默认值。

* data|default:'默认值'
* date：用于对日期类型的值进行字符串格式化，常用的格式化字符如下：

		Y表示年，格式为4位，y表示两位的年
		m表示月，格式为01,02,12等
		d表示日, 格式为01,02等
		j表示日，格式为1,2等
		H表示时，24进制，h表示12进制的时
		i表示分，为0-59
		s表示秒，为0-59 
		例：
			value|date:"Y年m月j日  H时i分s秒"       

代码示例： 

views.py代码如下： 

```python 

	def temp_filter(request):
	    context={'list':BookInfo.objects.all()}
	    return render(request,'booktest/temp_filter.html',context)
```  

temp_filter.html模板文件如下：  

```python  

	<head>
	    <title>过滤器</title>
	</head>
	<body>
	图书列表如下：
	<ul>
	    {%for book in list%}
	        {%if book.btitle|length > 4%}
	            <li style="background-color: red;">
	                {{book.btitle}}
	                ---默认时间格式为：
	                {{book.bpub_date}}
	            </li>
	        {%else%}
	            <li style="background-color: green;">
	                {{book.btitle}}
	                ---格式化时间为：
	                {{ book.bpub_date|date:"Y-m-j"}}
	            </li>
	        {%endif%}
	    {%endfor%}
	</ul>
	</body>
	</html>
```  

运行效果如下： 
![](https://i.imgur.com/8IWF9S5.png)   


####4.自定义过滤器
过滤器就是python中的函数，注册后就可以在模板中当作过滤器使用，下面以求余为例开发一个自定义过滤器mod   

步骤：

> 1. 在应用目录下建立templatetags包目录
> 2. 新建模块，导入from django.template import Library模块 
> 3. 实例化一个register = Library()对象
> 4. 自定义一个过滤器方法
> 5. 用register的filter装饰器装饰过滤器方法
> 6. 在settings.py模块中添加到install_app选项中，比如配置：“booktest.templatetags”
> 7. 模板中使用过滤器时用{% load my_filter%}加载，如果有模板继承，要放到其后面  

my_filter.py代码如下： 

```python

	from django.template import Library
	
	register = Library()
	
	@register.filter
	def mod(value1, value2):
	return value1%value2 == 0 
```  
 
自定义过滤器目录位置：  
![](https://i.imgur.com/s0c2wxI.png)  
 
自定义过滤器settings配置：  
![](https://i.imgur.com/YqzP7Ll.png)  

自定义过滤器的使用： 
![](https://i.imgur.com/wNjqaai.png)       



####5.模板注释 
在模板中使用如下模板注释，这段代码不会被编译，不会输出到客户端；html注释只能注释html内容，不能注释模板语言。

1）单行注释语法如下：

	{#...#}
注释可以包含任何模版代码，有效的或者无效的都可以。

	{# { % if foo % }bar{ % else % } #}
2）多行注释使用comment标签，语法如下：

	{%comment%}
	...
	{%endcomment%}  

####6.模板继承  
模板继承和类的继承含义是一样的，主要是为了提高代码重用，减轻开发人员的工作量。

**典型应用：网站的头部、尾部信息。**

**父模板**
如果发现在多个模板中某些内容相同，那就应该把这段内容定义到父模板中。

标签block：用于在父模板中预留区域，留给子模板填充差异性的内容，名字不能相同。 为了更好的可读性，建议给endblock标签写上名字，这个名字与对应的block名字相同。父模板中也可以使用上下文中传递过来的数据。

	{%block 名称%}
	预留区域，可以编写默认内容，也可以没有默认内容
	{%endblock  名称%}

**子模板**
标签extends：继承，写在子模板文件的第一行。

	{% extends "父模板路径"%}

子模版不用填充父模版中的所有预留区域，如果子模版没有填充，则使用父模版定义的默认值。

填充父模板中指定名称的预留区域。

	{%block 名称%}
	实际填充内容
	{{block.super}}用于获取父模板中block的内容
	{%endblock 名称%}

示例：
父模板中的代码如下：  

```html

	<html>
		<head>
	    	<title>{{title}}</title>
		</head>
		<body>
			<h2>这是头</h2>
			<hr>
				{%block qu1%}
				这是区域一，有默认值
				{%endblock qu1%}
			<hr>
				{%block qu2%}
				{%endblock qu2%}
			<hr>
			<h2>这是尾</h2>
		</body>
	</html>
```    

子模板中的代码如下： 

```html

	{%extends 'booktest/inherit_base.html'%}
	{%block qu2%}
	<ul>
	    {%for book in list%}
	    <li>{{book.btitle}}</li>
	    {%endfor%}
	</ul>
	{%endblock qu2%}

```

运行效果如下： 
![](https://i.imgur.com/93DBO7Y.png)








