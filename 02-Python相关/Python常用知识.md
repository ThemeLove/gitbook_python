####1.IPython是一个python的交互式shell,比默认的python shell 好用的多，有如下功能
	1. 支持自动补全
	2. 自动缩进
	3. 支持bash shell 命令
	4. 内置了许多有用的功能和函数
	5. 直接输入ipython/ipython3即可进入交互式环境
	6. 输入exit退出交互式环境
####2.Python的编码转换 
	一般encode编码就是编码成计算机能够认识的字节数组；decode解码就是解码成人能够认识的字符串

	bytes ----->字符串：decode解码 
	bytes.decode(encoding="utf-8", errors="strict") 

	字符串------>bytes:encode编码
	str.encode(encoding="utf-8", errors="strict")
	
	其中的encoding是指在解码编码过程中使用的编码(此处指“编码方案”是名词)，errors是指错误的处理方案  

####3.常用函数
	type()函数能够查看一个对象的类型
	id()函数能够查看一个对象的内存地址
	hex()函数用于将10进制整数转换成16进制，以字符串形式表示 

####4.os.getpid() 可以获取当前进程的pid 
####5.multiprocessing.cpu_count()获取cpu的核心数
####6.模块名.__file__:可以获取当前模块的文件位置
例如：os.__file__  
![](https://i.imgur.com/2KZ6A9N.png) 
####7.__init__.py 文件的作用
	 1. Python中package的标识，不能删除。只有包下有该文件，才可以被外部导入
	 2. 定义__all__用来模糊导入，需要配置__init.py
	    比如import common,就必须在该common包目录下的__init__.py中配置可以模糊导入的模块，如下
![](https://i.imgur.com/dMZnASn.png) 
####8.enumerate() 函数用于将一个可遍历的数据对象(如列表、元组或字符串)组合为一个索引序列，同时列出数据和数据下标，一般用在 for 循环当中 
	语法：enumerate(sequence, [start=0])

	参数
    sequence -- 一个序列、迭代器或其他支持迭代对象。
    start -- 下标起始位置 

![](https://i.imgur.com/tFGnzYP.png)

	
