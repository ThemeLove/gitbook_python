####1.HTML转义    
模板对上下文传递的字符串进行输出时，会对以下字符自动转义。   
 
	小于号< 转换为 &lt;
	
	大于号> 转换为 &gt;
	
	单引号' 转换为 &#39;
	
	双引号" 转换为 &quot;
	
	与符号& 转换为 &amp;    

> 注意：过滤器escape可以实现对变量的html转义，默认模板就会转义，一般省略。

	{{t1|escape}}   

**关闭转义**  


> **1. 过滤器safe：禁用转义，告诉模板这个变量是安全的，可以解释执行**。

	{{data|safe}}   

> **2. 标签autoescape：设置一段代码都禁用转义，接受on、off参数。**
	
	{%autoescape off%}
	...
	{%endautoescape%}
  
代码示例： 

views.py代码如下： 

```python

	def escape_test(request):
	    book = BookInfo.objects.all().get(id=1)
	return render(request, "booktest/escape_test.html", {"title": "<h1>hello django</h1>"})
```

模板代码如下：

```html

	<!DOCTYPE html>
	<html lang="en">
	<head>
	    <meta charset="UTF-8">
	    <title>escape_test</title>
	</head>
	<body>
	1.djano模板变量默认自动开启转义,html类型的字符串会原样显示：<br>
	django：{{title}} <br><br><br>
	
	2.html文件中的硬编码和django模板硬编码都会被浏览器解析: <br>
	html硬编码：<h1>hello django</h1>
	django模板硬编码：{{no_title|default:"<h1>hello django</h1>"}}
	
	3.django模板变量可以用safe过滤器来关闭某个模板变量的转义：<br>
	django-safe:{{title|safe}} <br>
	
	
	4.django中也可以用模板标签autoescape来控制标签内的内容是否转义：<br>
	django-autoescape标签：<br>
	
	{%autoescape on%}
	    {{title}}
	{%endautoescape%}
	
	</body>
	</html>
```

运行效果如下：  
![](https://i.imgur.com/bXbS7nR.png)   

####2.CSRF
CSRF全拼为Cross Site Request Forgery，译为跨站请求伪造。CSRF指攻击者盗用了你的身份，   
以你的名义发送恶意请求。CSRF能够做的事情包括：以你名义发送邮件，发消息，盗取你的账号，   
甚至于购买商品，虚拟货币转账......造成的问题包括：个人隐私泄露以及财产安全。

CSRF示意图： 
![](https://i.imgur.com/dZYtxI2.png) 

如果想防止CSRF，首先是重要的信息传递都采用POST方式而不是GET方式，接下来就说POST请求的攻击方式以及在Django中的避免。  

**Django中的的CSRF**  
> 1. Django项目中默认启用了csrf保护，在settings.py的MIDDLEWARE_CLASSES中可查看配置  
> 2. 使用标签csrf_token来给模板文件添加隐藏域  
> 3. 说明：当启用中间件并加入标签csrf_token后，会向客户端浏览器中写入一条Cookie信息，  
> 这条信息的值与隐藏域input元素的value属性是一致的，提交到服务器后会先由csrf中间件进行验证， 
> 如果对比失败则返回403页面，而不会进行后续的处理。      

####3.验证码  
1）安装包Pillow3.4.1。

	pip install Pillow==3.4.1
	点击查看PIL模块API，以下代码中用到了Image、ImageDraw、ImageFont对象及方法。   
2）手动实现验证码  
> 1. 随机生成字符串后存入session中，用于后续判断。  
> 2. 视图返回mime-type为image/png。

```python
 
	from PIL import Image, ImageDraw, ImageFont
	from django.utils.six import BytesIO
	
	def verify_code(request):
	    #引入随机函数模块
	    import random
	    #定义变量，用于画面的背景色、宽、高
	    bgcolor = (random.randrange(20, 100), random.randrange(
	        20, 100), 255)
	    width = 100
	    height = 25
	    #创建画面对象
	    im = Image.new('RGB', (width, height), bgcolor)
	    #创建画笔对象
	    draw = ImageDraw.Draw(im)
	    #调用画笔的point()函数绘制噪点
	    for i in range(0, 100):
	        xy = (random.randrange(0, width), random.randrange(0, height))
	        fill = (random.randrange(0, 255), 255, random.randrange(0, 255))
	        draw.point(xy, fill=fill)
	    #定义验证码的备选值
	    str1 = 'ABCD123EFGHIJK456LMNOPQRS789TUVWXYZ0'
	    #随机选取4个值作为验证码
	    rand_str = ''
	    for i in range(0, 4):
	        rand_str += str1[random.randrange(0, len(str1))]
	    #构造字体对象，ubuntu的字体路径为“/usr/share/fonts/truetype/freefont”
	    font = ImageFont.truetype('FreeMono.ttf', 23)
	    #构造字体颜色
	    fontcolor = (255, random.randrange(0, 255), random.randrange(0, 255))
	    #绘制4个字
	    draw.text((5, 2), rand_str[0], font=font, fill=fontcolor)
	    draw.text((25, 2), rand_str[1], font=font, fill=fontcolor)
	    draw.text((50, 2), rand_str[2], font=font, fill=fontcolor)
	    draw.text((75, 2), rand_str[3], font=font, fill=fontcolor)
	    #释放画笔
	    del draw
	    #存入session，用于做进一步验证
	    request.session['verifycode'] = rand_str
	    #内存文件操作
	    buf = BytesIO()
	    #将图片保存在内存中，文件类型为png
	    im.save(buf, 'png')
	    #将内存中的图片数据返回给客户端，MIME类型为图片png
	    return HttpResponse(buf.getvalue(), 'image/png')
```   

注意：一般前端html中点击"看不清，换一张"刷新验证码的处理： 
![](https://i.imgur.com/WH36MbT.png)  

####5.反向解析  
**问题：**   
	开发时可能之前配置urls.py中正则表达式不够准确，于是就要修改正则表达式，但是正则表达式一旦修改了，  
	之前所有对应的超链接都要修改，真是一件麻烦的事情，而且可能还会漏掉一些超链接忘记修改，  
	有办法让链接根据正则表达式动态生成吗？ 答：反向解析。

> 反向解析应用在两个地方：模板中的超链接，视图中的重定向。  

**Django中使用步骤：**  
> 1.在项目的urls.py中配置应用的namespace属性   
 
```python  

	urlpatterns = [
	    path('admin/', admin.site.urls),
	    url(r'^', include('booktest.urls', 'booktest'))
	]
```   
> 2.在具体应用的urls.py中添加name属性   

```python

 	url(r'^index[/]$', views.show_books, name="index") 
```  
> 3.在模板中使用  

	反向解析配置：<a href="{%url 'booktest:index'%}">反向代理配置index页面</a>  
> 4.在重定向中使用  
	return redirect(reverse('booktest:index'))   

**反向解析URL中的参数：**   

有些url配置项正则表达式中是有参数的，接下来讲解如何传递参数。

**情况一：位置参数**  
1）在booktest/urls.py中，修改fan2如下：

    url(r'^fan(\d+)_(\d+)/$', views.fan3,name='fan2'),    
2）在booktest/views中，定义视图fan3如下:

	def fan3(request, a, b):
	    return HttpResponse(a+b)  

3）修改templates/booktest/fan1.html文件如下：

	<html>
	<head>
	    <title>反向解析</title>
	</head>
	<body>
	普通链接：<a href="/fan2_3/">fan2</a>
	<hr>
	反向解析：<a href="{%url 'booktest:fan2' 2 3%}">fan2</a>
	</body>
	</html>

4）使用重定向传递位置参数格式如下：

	return redirect(reverse('booktest:fan2', args=(2,3)))    

**情况二：关键字参数**
1）在booktest/urls.py中，修改fan2如下：

    url(r'^fan(?P<id>\d+)_(?P<age>\d+)/$', views.fan4,name='fan2'),
2）在booktest/views中，定义视图fan4如下:
	
	def fan4(request, id, age):
	    return HttpResponse(id+age)

3）修改templates/booktest/fan1.html文件如下：

	<html>
	<head>
	    <title>反向解析</title>
	</head>
	<body>
	普通链接：<a href="/fan100_18/">fan2</a>
	<hr>
	反向解析：<a href="{%url 'booktest:fan2' id=100 age=18%}">fan2</a>
	</body>
	</html>  

4）使用重定向传递关键字参数格式如下：

	return redirect(reverse('booktest:fan2', kwargs={'id':100,'age':18}))
