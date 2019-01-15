####1.Python与MySQL交互流程  
![](https://i.imgur.com/SjDnLOx.png)   

####2.Python与MySQL交互基本步骤 
	1. 引入pymysql模块，该模块python解释器不自带，需要单独安装:pip3 install pymysql
		   代码中引入：
		   from pymysql import * 

	2. 创建Connection对象，用于建立与数据库的连接
		   创建数据库连接   
		   self.conn = connect(host='localhost', port=3306, database='jing_dong', user='root', password='themelove', charset='utf8')    

	3. 通过connection对象获取cursor对象 
	   	   conn.cursor()   

	4. cursor.execute(sql, agrs) 执行sql语句，返回值为执行语句影响的记录数 
	   	   例：
           count = cursor.execute("""select * from goods where brand_id=2""") 
		   cursor.fetchall()   
	
	5. 通过cursor.fetchone()、cursor.fetchmany(count)、cursor.fetchall()获取执行结果   
		   cursor.fetchone():获取一条记录，返回值为元祖
		   cursor.fetchmany(count):获取count条记录，返回值为元祖，每条记录也对应一个元祖       
		   cursor.fetchall():获取所有记录，返回值为元祖，每条记录也对应一个元祖		

	6. 使用完毕关闭cursor和connection对象  
		   cursor.close() 
		   conn.close()      

####3.Connection 对象  
用于建立与数据库的连接

	创建对象：调用connect()方法  
		conn=connect(参数列表)    
 
	参数解释：  
		参数host：连接的mysql主机，如果本机是'localhost'  
		参数port：连接的mysql主机的端口，默认是3306    
		参数database：数据库的名称    
		参数user：连接的用户名    
		参数password：连接的密码     
		参数charset：通信采用的编码方式，推荐使用utf8       
	
	对象的方法：     
		close()关闭连接     
		commit()提交       
		cursor()返回Cursor对象，用于执行sql语句并获得结果      

####4.Cursor对象
用于执行sql语句，使用频度最高的语句为select、insert、update、delete   

	获取Cursor对象：调用Connection对象的cursor()方法
		cursor = conn.cursor() 

	对象的方法
		close()关闭    
		execute(operation [, parameters ])执行语句，返回受影响的行数，主要用于执行insert、update、delete语句，   
				  也可以执行create、alter、drop等语句   		
		fetchone()执行查询语句时，获取查询结果集的第一个行数据，返回一个元组		
		fetchall()执行查询时，获取结果集的所有行，一行构成一个元组，再将这些元组装入一个元组返回   

	对象的属性
		rowcount只读属性，表示最近一次execute()执行后受影响的行数
		connection获得当前连接对象   

####5.SQL语句的注入  
	概念：就是通过把SQL命令插入到Web表单提交或输入域名或页面请求的查询字符串，最终达到欺骗服务器执行恶意的SQL命令  
 
    # 不自己拼接参数，传参数给mysql让其自己拼接可以防止sql注入
    self.cursor.execute(sql, brand_name)
    for temp in self.cursor:
        print(temp)

    # 自己拼接参数可能发生sql语句注入：比如用户输入 '' or 1=1 ,会查询出所有品牌信息
    # sql = """select name from goods_brands %s;""" % brand_name
    # self.execute_sql(sql)
	


python与MySQL交互实现增删改查：  

```python

	from pymysql import connect
	
	
	class JD:
	    def __init__(self):
	        """1.创建数据库连接
	           2.获取数据库cursor
	        """
	        # 创建数据库连接
	        self.conn = connect(host='localhost', port=3306, database='jing_dong', user='root', password='themelove', charset='utf8')
	        # 获取cursor对象
	        self.cursor = self.conn.cursor()
	
	    def close(self):
	        if self.cursor:
	            self.cursor.close()
	        if self.conn:
	            self.conn.close()
	
	    def execute_sql(self, sql):
	        self.cursor.execute(sql)
	        for temp in self.cursor.fetchall():
	            print(temp)
	
	    def show_all_goods(self):
	        sql = """select * from goods;"""
	        self.execute_sql(sql)
	
	    def show_goods_cates(self):
	        sql = """select * from goods_cates;"""
	        self.execute_sql(sql)
	
	    def show_goods_brands(self):
	        sql = """select * from goods_brands;"""
	        self.execute_sql(sql)
	
	    def add_good_brand(self):
	        brand_name = input("请输入您要添加的品牌名称：")
	        # 通过给传参数的方式，让mysql自己拼接参数，可以防止sql语句注入
	        sql = """insert into goods_brands(name) values(%s);"""
	        self.cursor.execute(sql, (brand_name,))
	        # 如果时增删改，需要用connect进行commit
	        self.conn.commit()
	
	    def search_good_brand_by_name(self):
	        brand_name = input("请输入您要查询的品牌名称：")
	        sql = """select name from goods_brands %s;"""
	        # 不自己拼接参数，传参数给mysql让其自己拼接可以防止sql注入
	        self.cursor.execute(sql, brand_name)
	        for temp in self.cursor:
	            print(temp)
	
	        # 自己拼接参数可能发生sql语句注入：比如用户输入 '' or 1=1 ,会查询出所有品牌信息
	        # sql = """select name from goods_brands %s;""" % brand_name
	        # self.execute_sql(sql)
	
	    @staticmethod
	    def show_hint():
	        print("-----京东商城-----")
	        print("1:查看所有商品")
	        print("2.产看所有商品分类")
	        print("3.查看所有商品品牌")
	        print("4.添加一个品牌")
	        print("5.退出")
	        return input("请输入您要操作的序号：")
	
	    def run(self):
	        while True:
	            opt = self.show_hint()
	            if opt == "1":
	                self.show_all_goods()
	            elif opt == "2":
	                self.show_goods_cates()
	            elif opt == "3":
	                self.show_goods_brands()
	            elif opt == "4":
	                self.add_good_brand()
	            elif opt == "5":  # 退出循环
	                self.close()
	                break
	            else:
	                print("请重新输入")
	
	
	def main():
	    # 创建JD对象
	    jd = JD()
	    # 调用jd的run方法
	    jd.run()
	
	
	if __name__ == "__main__":
		main()
```