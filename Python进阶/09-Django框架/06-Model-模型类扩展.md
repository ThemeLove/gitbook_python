####1.模型实例方法  
- str()：在将对象转换成字符串时会被调用
- save()：将模型对象保存到数据表中，ORM框架会转换成对应的insert或update语句
- delete()：将模型对象从数据表中删除，ORM框架会转换成对应的delete语句  

####2.模型类的属性  
属性objects：管理器，是models.Manager类型的对象，用于与数据库进行交互  
> 当没有为模型类定义管理器时，Django会为每一个模型类生成一个名为objects的管理> > 器，自定义管理器后，Django不再生成默认管理器objects。  

为模型类BookInfo定义管理器books语法如下：  

```python 

	class BookInfo(models.Model):
	    ...
	    books = models.Manager()

```  

####3.管理器Manager   
管理器是Django的模型进行数据库操作的接口，Django应用的每个模型类都拥有至少一个管理器。Django支持自定义管理器类，继承自models.Manager  

自定义管理器类主要用于两种情况：

1. 修改原始查询集，重写all()方法  
2. 向管理器类中添加额外的方法，如向数据库中插入数据  

例1：修改原始查询集，重写all()方法
```python
 
	#图书管理器
	class BookInfoManager(models.Manager):
	    def all(self):
	        #默认查询未删除的图书信息
	        #调用父类的成员语法为：super().方法名
	        return super().all().filter(isDelete=False)

	#在模型类BookInfo中定义管理器
	class BookInfo(models.Model):
	    ...
	    books = BookInfoManager()    
```    

例2：在管理器类中定义创建对象的方法 
对模型类对应的数据表进行操作时，推荐将这些操作数据表的方法封装起来，放到模型管理器类中    

```python

	class BookInfoManager(models.Manager):
	    ...
	    #创建模型类，接收参数为属性赋值
	    def create_book(self, title, pub_date):
	        #创建模型类对象self.model可以获得模型类
	        book = self.model()
	        book.btitle = title
	        book.bpub_date = pub_date
	        book.bread=0
	        book.bcommet=0
	        book.isDelete = False
	        # 将数据插入进数据表
	        book.save()
	        return book

	#为模型类BookInfo定义管理器books语法如下
	class BookInfo(models.Model):
	    ...
	    books = BookInfoManager()

	#调用语法如下：
	调用：book=BookInfo.books.create_book("abc",date(1980,1,1))
```  

####4.元选项
在模型类中定义类Meta，用于设置元信息，如使用db_table自定义表的名字
  
	数据表的默认名称为：
	
	<app_name>_<model_name>
	例：
	booktest_bookinfo

例：指定BookInfo模型类生成的数据表名为bookinfo   

```python

	#定义图书模型类BookInfo
	class BookInfo(models.Model):
	    ...
	
	    #定义元选项
	    class Meta:
	      db_table='bookinfo' #指定BookInfo生成的数据表名为bookinfo
```