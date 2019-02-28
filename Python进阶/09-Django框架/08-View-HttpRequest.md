#HttpRequest对象 
服务器接收到http协议的请求后，会根据报文创建HttpRequest对象，  
这个对象不需要我们创建，直接使用服务器构造好的对象就可以，视图的第一个参数必须是HttpRequest对象，  
一般为Django框架创建的WSGIRequest对象，在django.http模块中定义了HttpRequest对象的API。 

#属性 
>下面除非特别说明，属性都是只读的。

- path：一个字符串，表示请求的页面的完整路径，不包含域名和参数部分。
- method：一个字符串，表示请求使用的HTTP方法，常用值包括：'GET'、'POST'。
	- 在浏览器中给出地址发出请求采用get方式，如超链接。
	- 在浏览器中点击表单的提交按钮发起请求，如果表单的method设置为post则为post请求。
- encoding：一个字符串，表示提交的数据的编码方式。
	- 如果为None则表示使用浏览器的默认设置，一般为utf-8。
	- 这个属性是可写的，可以通过修改它来修改访问表单数据使用的编码，接下来对属性的任何访问将使用新的       	encoding值。
- GET：QueryDict类型对象，类似于字典，包含get请求方式的所有参数。
- POST：QueryDict类型对象，类似于字典，包含post请求方式的所有参数。
- FILES：一个类似于字典的对象，包含所有的上传文件。
- COOKIES：一个标准的Python字典，包含所有的cookie，键和值都为字符串。
- session：一个既可读又可写的类似于字典的对象，表示当前的会话，只有当Django 启用会话的支持时才可用，详细		   内容见"状态保持"。  

#QueryDict对象 
- 定义在django.http.QueryDict
- HttpRequest对象的属性GET、POST都是QueryDict类型的对象
- 与python字典不同，QueryDict类型的对象用来处理同一个键带有多个值的情况
- 方法get()：根据键获取值
- 如果一个键同时拥有多个值将获取最后一个值
- 如果键不存在则返回None值，可以设置默认值进行后续处理   

		dict.get('键',默认值)  
		可简写为  
		dict['键']  
- 方法getlist()：根据键获取值，值以列表返回，可以获取指定键的所有值
- 如果键不存在则返回空列表[]，可以设置默认值进行后续处理  

		dict.getlist('键',默认值)  

#GET属性 
请求格式：在请求地址结尾使用?，之后以"键=值"的格式拼接，多个键值对之间以&连接。

例：网址如下

	http://www.itcast.cn/?a=10&b=20&c=python
其中的请求参数为：

	a=10&b=20&c=python
- 分析请求参数，键为'a'、'b'、'c'，值为'10'、'20'、'python'。
- 在Django中可以使用HttpRequest对象的GET属性获得get方方式请求的参数。
- GET属性是一个QueryDict类型的对象，键和值都是字符串类型。
- 键是开发人员在编写代码时确定下来的。
- 值是根据数据生成的。  

#POST属性 
使用form表单请求时，method方式为post则会发起post方式的请求，需要使用HttpRequest对象的POST属性接收参数，  
POST属性是一个QueryDict类型的对象。

**问：表单form如何提交参数呢？**

答：表单控件name属性的值作为键，value属性的值为值，构成键值对提交。

- 如果表单控件没有name属性则不提交。
- 对于checkbox控件，name属性的值相同为一组，被选中的项会被提交，出现一键多值的情况。
- 键是表单控件name属性的值，是由开发人员编写的。
- 值是用户填写或选择的。   

示例：  

```python

	#接收请求参数
	def show_reqarg(request):
	    if request.method == 'GET':
	        a = request.GET.get('a') #获取请求参数a
	        b = request.GET.get('b') #获取请求参数b
	        c = request.GET.get('c') #获取请求参数c
	        return render(request, 'booktest/show_getarg.html', {'a':a, 'b':b, 'c':c})
	    else:
	        name = request.POST.get('uname') #获取name
	        gender = request.POST.get('gender') #获取gender
	        hobbys = request.POST.getlist('hobby') #获取hobby
	        return render(request, 'booktest/show_postarg.html', {'name':name, 'gender':gender, 'hobbys':hobbys})
```