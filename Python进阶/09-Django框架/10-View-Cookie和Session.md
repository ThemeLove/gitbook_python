#状态保持   
浏览器请求服务器是无状态的。无状态指一次用户请求时，浏览器、服务器无法知道之前这个用户做过什么，  
每次请求都是一次新的请求。无状态的应用层面的原因是：浏览器和服务器之间的通信都遵守HTTP协议。  
根本原因是：浏览器与服务器是使用Socket套接字进行通信的，服务器将请求结果返回给浏览器之后，  
会关闭当前的Socket连接，而且服务器也会在处理页面完毕之后销毁页面对象。

有时需要保存下来用户浏览的状态，比如用户是否登录过，浏览过哪些商品等。 实现状态保持主要有两种方式：

- 在客户端存储信息使用Cookie。
- 在服务器端存储信息使用Session。  

#Cookie   
Cookie，有时也用其复数形式Cookies，指某些网站为了辨别用户身份、进行session跟踪而储存在用户本地终端上的   
数据（通常经过加密）。Cookie最早是网景公司的前雇员Lou Montulli在1993年3月的发明.Cookie是由服务器端生成，  
发送给User-Agent（一般是浏览器），浏览器会将Cookie的key/value保存到某个目录下的文本文件内，下次请求  
同一网站时就发送该Cookie给服务器（前提是浏览器设置为启用cookie）。Cookie名称和值可以由服务器端开发自己定义，  
这样服务器可以知道该用户是否是合法用户以及是否需要重新登录等。服务器可以利用Cookies包含信息的任意性来筛选并  
经常性维护这些信息，以判断在HTTP传输中的状态。Cookies最典型记住用户名。

Cookie是存储在浏览器中的一段纯文本信息，建议不要存储敏感信息如密码，因为电脑上的浏览器可能被其它人使用。  

#Cookie的特点   
- Cookie以键值对的格式进行信息的存储。
- Cookie基于域名安全，不同域名的Cookie是不能互相访问的，如访问itcast.cn时向浏览器中写了Cookie信息，  
	使用同一浏览器访问baidu.com时，无法访问到itcast.cn写的Cookie信息。
- 当浏览器请求某网站时，会将浏览器存储的跟网站相关的所有Cookie信息提交给网站服务器

#设置Cookie    

**HttpResponse.set_cookie  **   

	set_cookie：设置Cookie信息。
	set_cookie(key, value='', max_age=None, expires=None)  

- cookie是网站以键值对格式存储在浏览器中的一段纯文本信息，用于实现用户跟踪。
	- max_age是一个整数，表示在指定秒数后过期。
	- expires是一个datetime或timedelta对象，会话将在这个指定的日期/时间过期。
	- max_age与expires二选一。
	- 如果不指定过期时间，在关闭浏览器时cookie会过期。
- delete_cookie(key)：删除指定的key的Cookie，如果key不存在则什么也不发生。
- write：向响应体中写数据。

```python

	def cookie_set(request):
	    response = HttpResponse("<h1>设置Cookie，请查看响应报文头</h1>")
	    response.set_cookie('h1', '你好')
	    return response
```  

#获取Cookie  
**HttpRequest.COOKIES** 

```python  

	def cookie_get(request):
	    response = HttpResponse("读取Cookie，数据如下：<br>")
	    if 'h1' in request.COOKIES:
	        response.write('<h1>' + request.COOKIES['h1'] + '</h1>')
	    return response
```   


#Sessoin 
对于敏感、重要的信息，建议要储在服务器端，不能存储在浏览器中，如用户名、余额、等级、验证码等信息。  

#Session的特点 
**所有请求者的Session都会存储在服务器中，服务器如何区分请求者和Session数据的对应关系呢？**

答：在使用Session后，会在Cookie中存储一个sessionid的数据，每次请求时浏览器都会将这个数据发给服务器，服务器在接收到sessionid后，会根据这个值找出这个请求者的Session。

结果：如果想使用Session，浏览器必须支持Cookie，否则就无法使用Session了。

存储Session时，键与Cookie中的sessionid相同，值是开发人员设置的键值对信息，进行了base64编码，过期时间由开发人员设置。 

#启用Session   
Django项目默认启用Session，开启或禁用需要修改settings.py模块配置  

![](https://i.imgur.com/j6aCUlZ.png)      


#存储方式   
settings.py文件，设置SESSION_ENGINE项指定Session数据存储的方式，可以存储在数据库、缓存、Redis等。 

1）存储在数据库中，如下设置可以写，也可以不写，这是默认存储方式   
 
	SESSION_ENGINE='django.contrib.sessions.backends.db'  

2）存储在缓存中：存储在本机内存中，如果丢失则不能找回，比数据库的方式读写更快。  

	SESSION_ENGINE='django.contrib.sessions.backends.cache'  

3）混合存储：优先从本机内存中存取，如果没有则从数据库中存取。  
 
	SESSION_ENGINE='django.contrib.sessions.backends.cached_db'   

4）如果存储在数据库中，需要在项INSTALLED_APPS中安装Session应用。  
![](https://i.imgur.com/ro3PeyW.png)  

5）迁移后会在数据库中创建出存储Session的表，表结构如下： 
![](https://i.imgur.com/F1XFP2S.png)    
由表结构可知，操作Session包括三个数据：键，值，过期时间   

#对象及方法
通过HttpRequest对象的session属性进行会话的读写操作。

1） 以键值对的格式写session。

	request.session['键']=值

2）根据键读取值。

	request.session.get('键',默认值)

3）清除所有session，在存储中删除值部分。

	request.session.clear()

4）清除session数据，在存储中删除session的整条数据。

	request.session.flush()

5）删除session中的指定键及值，在存储中只删除某个键及对应的值。

	del request.session['键']

6）设置会话的超时时间，如果没有指定过期时间则两个星期后过期。

	request.session.set_expiry(value)
- 如果value是一个整数，会话将在value秒没有活动后过期。
- 如果value为0，那么用户会话的Cookie将在用户的浏览器关闭时过期。
- 如果value为None，那么会话永不过期。

示例截图：    

![](https://i.imgur.com/mGEi0V8.png)
	