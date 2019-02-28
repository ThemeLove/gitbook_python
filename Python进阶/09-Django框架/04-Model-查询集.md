####1.查询集 QuerySet 
	查询集表示从数据库中获取的对象集合，在管理器上调用某些过滤器方法会返回查询集，  
	查询集可以含有零个、一个或多个过滤器。过滤器基于所给的参数限制查询的结果，  
	从Sql的角度，查询集和select语句等价，过滤器像where和limit子句。

**返回查询集的过滤器如下：**  
 
- all()：返回所有数据  
- filter()：返回满足条件的数据。
- exclude()：返回满足条件之外的数据，相当于sql语句中where部分的not关键字
- order_by()：对结果进行排序  

**返回单个值的过滤器如下：**

- get()：返回单个满足条件的对象  
     	1. 如果未找到会引发"模型类.DoesNotExist"异常  
     	2. 如果多条被返回，会引发"模型类.MultipleObjectsReturned"异常 
	 	   BookInfo.objects.all().get(id__gt=3) 就会抛出MultipleObjectsReturned异常
- count()：返回当前查询结果的总条数
- aggregate()：聚合，返回一个字典

**判断某一个查询集中是否有数据：**

- exists()：判断查询集中是否有数据，如果有则返回True，没有则返回False  

**两大特性：** 

- 惰性执行：创建查询集不会访问数据库，直到调用数据时，才会访问数据库，调用数据的情况包括迭代、序列化、与if合用。  

- 缓存：使用同一个查询集，第一次使用时会发生数据库的查询，然后把结果缓存下来，再次使用这个查询集时会使用缓存的数据。 

示例：查询所有的图书：  

```python 

	# 执行这行代码时Django并不会真正的去查询数据库
	books = BookInfo.objects.all()
	# 只有用到查询结果时Django才会真正的去查询数据库 
	for book in books:
		print("book.btitle=%s" %(book.btitle,))
```  

**限制查询集：**

- 可以对查询集进行取下标或切片操作，等同于sql中的limit和offset子句
- 对查询集进行切片后返回一个新的查询集，不会立即执行查询,当用到查询结果时才会实际查询

注意：
> 1. 不支持负数索引  
> 2. 如果获取一个对象，直接使用[0]，等同于[0:1].get()，   
> 但是如果没有数据，[0]引发IndexError异常，  
> [0:1].get()如果没有数据引发DoesNotExist异常。

示例：获取第1、2项，运行查看。

```python 

	list=BookInfo.objects.all()[0:2]
```