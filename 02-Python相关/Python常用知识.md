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
	hex()函数用于将10进制整数转换成16进制，以字符串形式表示。
