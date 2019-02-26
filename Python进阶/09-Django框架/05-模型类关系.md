####1.模型类关系 
**关系字段属性**  
- ForeignKey :一对多，将字段定义在多的一端中  
- ManyToManyField:多对多，将字段定义在任意一端中  
- OneToOneField:一对一，将字段定义在任意一端中  
- 自关联：可以维护递归的关联关系，使用'self'指定

**一对多关系**   
例：图书类和英雄类的对应关系

```python

	#定义图书模型类BookInfo
	class BookInfo(models.Model):
	    btitle = models.CharField(max_length=20)#图书名称
	    bpub_date = models.DateField()#发布日期
	    bread = models.IntegerField(default=0)#阅读量
	    bcomment = models.IntegerField(default=0)#评论量
	    isDelete = models.BooleanField(default=False)#逻辑删除
	
	#定义英雄模型类HeroInfo
	class HeroInfo(models.Model):
	    hname = models.CharField(max_length=20)#英雄姓名
	    hgender = models.BooleanField(default=True)#英雄性别
	    isDelete = models.BooleanField(default=False)#逻辑删除
	    hcomment = models.CharField(max_length=200)#英雄描述信息
	    hbook = models.ForeignKey('BookInfo')#英雄与图书表的关系为一对多，所以属性定义在英雄模型类中 
```

**多对多关系**  
例：我们下面设计一个新闻类和新闻类型类，一个新闻类型下可以用很多条新闻，一条新闻也可能归属于多种新闻类型

```python

	class TypeInfo(models.Model):
	  tname = models.CharField(max_length=20) #新闻类别
	
	class NewsInfo(models.Model):
	  ntitle = models.CharField(max_length=60) #新闻标题
	  ncontent = models.TextField() #新闻内容
	  npub_date = models.DateTimeField(auto_now_add=True) #新闻发布时间
	  ntype = models.ManyToManyField('TypeInfo') #通过ManyToManyField建立TypeInfo类和NewsInfo类之间多对多的关系
```    

**一对一关系**  
例：员工基本信息类和员工详细信息类

```python

	class EmployeeBasicInfo(models.Model):
	    '''员工基本信息类'''
	    # 姓名
	    name = models.CharField(max_length=20)
	    # 性别
	    gender = models.BooleanField(default=False)
	    # 年龄
	    age = models.IntegerField()
	
	
	class EmployeeDetailInfo(models.Model):
	    # 联系地址
	    addr = models.CharField(max_length=256)
	    # 关系属性，代表员工基本信息
		employee_basic = models.OneToOneField('EmployeeBasicInfo', on_delete=models.CASCADE) 
```

####2.关联查询   
**通过对象执行关联查询**  
> 1 由一到多的访问语法：

语法：一对应的模型类对象.多对应的模型类名小写_set

```python

	b = BookInfo.objects.get(id=1)
	b.heroinfo_set.all()  
```

> 2 由多到一的访问语法:

语法：多对应的模型类对象.多对应的模型类中的关系类属性名

```python

	h = HeroInfo.objects.get(id=1)
	h.hbook
```

> 3 访问一对应的模型类关联对象的id语法:

多对应的模型类对象.关联类属性_id

```python

	h = HeroInfo.objects.get(id=1)
	h.book_id
```

**通过模型类执行关联查询**  
> 1  由多模型类条件查询一模型类数据  
>    语法如下：关联模型类名小写__属性名__条件运算符=值  
>    注意：如果没有"__运算符"部分，表示等于，结果和sql中的inner join相同。

例：查询图书，要求图书中英雄的描述包含'八'。

```python

	list = BookInfo.objects.filter(heroinfo__hcontent__contains='八')
```  
   
> 2  由一模型类条件查询多模型类数据  
>    语法如下：一模型类关联属性名__一模型类属性名__条件运算符=值  

例：查询书名为“天龙八部”的所有英雄。

```python

	list = HeroInfo.objects.filter(hbook__btitle='天龙八部')
```   

####3.自关联  
	分析：  
		对于地区信息、分类信息等数据，表结构非常类似，每个表的数据量十分有限，  
		为了充分利用数据表的大量数据存储功能，可以设计成一张表，  
		内部的关系字段指向本表的主键，这就是自关联的表结构。  
> 说明：关系属性使用self指向本类，要求null和blank允许为空，因为一级数据是没有父级的 

```python

	#定义地区模型类，存储省、市、区县信息
	class AreaInfo(models.Model):
	    atitle=models.CharField(max_length=30)#名称
	    aParent=models.ForeignKey('self',null=True,blank=True)#关系
```