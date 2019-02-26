####1. 条件运算符 
>1.1 查询等

**exact:表示判等**  
例：查询编号为1的图书  

```python

	list = BookInfo.objects.filter(id__exact=1)
	可简写为：
	list = BookInfo.objects.filter(id=1)
```    

>1.2 模糊查询   

**contains:是否包含**：如果要包含%无需转义，直接写即可   
例：查询书名包含'传'的图书  

```python

	list = BookInfo.objects.filter(btitle__contains='传')
``` 

**startswith、endswith:以指定值开头或结尾**  
例：查询书名以'部'结尾的图书 

```python

	list = BookInfo.objects.filter(btitle__endswith='部')
```

>1.3 空查询  

**isnull:是否为null**   
例：查询书名不能为空的图书  

```python

	list = BookInfo.objects.filter(btitle__isnull=False)
```          

>1.4 范围查询  
 
**in:是否包含在范围内**   
例：查询编号为1或者3或者5的图书   
 
```python

	list = BookInfo.objects.filter(id__in=[1,3,5])
```    

>1.5 比较查询
 
**gt：大于  
gte：大于等于  
lt:小于  
lte:小于等于**  
例：查询编号大于3的图书   

```python

	list = BookInfo.objects.filter(id__gt=3)
```   

**不等于的运算符，使用exclude()过滤器**  
例：查询编号不等于3的图书  

```python

	list = BookInfo.objects.exclude(id=3)
```    

>1.6 日期查询   

**year、month、day、week_day、hour、minute、second:对日期时间类型的属性进行运算**   
例:查询1980年发表的图书  

```python

	list = BookInfo.objects.filter(bpub_date__year=1980)
```   
例：查询1980年1月1日后发表的图书  

```python 

	list = BookInfo.objects.filter(bpub_date__gt=datetime(1980, 1, 1))
```  

####2. F对象  
之前的查询都是对象的属性与常量值的比较，两个属性怎么比较呢？答：使用F对象，被定义在django.db.models中。   
**语法： F(属性名)**   
例：查询阅读量大于等于评论量的图书  
  
```python

	from django.db.models import F
	
	list = BookInfo.objects.filter(bread_gt=F('bcomment'))
```  
也可以在F对象上使用算数运算符  
例：查询阅读量大于2倍评论量的图书
 
```python 
	
	list = BookInfo.objects.filter(bread_gt=F('bcomment')*2)
``` 

####3. Q对象 
多个过滤器逐个调用标示逻辑与关系，同sql语句中的where部分的and关键字  
例：查询阅读量大于20，并且编号小于3的图书   

```python

	list = BookInfo.objects.filter(bread__gt=20,id__lt=3) 
	或 
	list = BookInfo.objects.filter(bread__gt=20).filter(id__lt=3)
```    

如果需要实现逻辑or的查询，需要使用Q()对象结合|运算符，Q对象被定义在django.db.models中。  
**语法：Q(属性名__运算符=值)** :   

	Q对象可以使用逻辑运算连接：  
		&（逻辑与）   
		|（逻辑或）  
		~ (逻辑非)     

例：查询阅读量大于20，或编号小于3的图书，只能使用Q对象实现
 
```python

	from django.db.models import Q
	list = BookInfo.objects.filter(Q(bread__gt=20)|Q(id__lt=3))
```  
例：查询编号不等于3的图书
  
```python

	list = BookInfo.objects.filter(~Q(id=3))
```  

####4. 聚合函数  
使用aggregate()过滤器调用聚合函数，聚合函数包括：Avg、Count、Max、Min、Sum 被定义在django.db.models中  
例：查询图书的总阅读量

```python

	from django.db.models import Sum 
	list = BookInfo.objects.aggregate(Sum('bread'))
```   
注意aggregate的返回值是一个字典类型，格式如下： 

```python

	{'聚合类小写__属性名'：值}   
	如：{'sum__bread':3}
```   
 
计算count时一般不使用aggregate()过滤器，而直接使用count()函数，注意count()函数的返回值是一个数字     
例：查询图书总数  
 
```python
	
	list = BookInfo.objects.count()
```  
