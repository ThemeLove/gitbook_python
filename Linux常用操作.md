###1.Ubuntu上修改计算机名称 
	步骤：
		1.打开终端，输入sudo gedit /etc/hostname,输入密码，打开hostname文件，修改计算机名称，之后保存退出。 
		2./etc/hosts中也用到了主机名，修改计算机名的时候最好一并修改，输入sudo gedit /etc/hosts,打开hosts文件， 
		修改计算机名称，之后保存退出。 
		3.重启系统之后才能生效。 
###2.设置终端Terminal的光标形状
	在终端里右键----->Preferences----->Unnamed找到Cursor更改Cursor shape即可 
###3.Linux终端命令格式 
	 command [-options] [parameter] 
	 command:命令名，相应功能的英文单词或单词的缩写 
	 [-options]:选项，可用来对命令进行控制，也可以省略 
	 [parameter]:传给命令的参数，可以是0个、一个或者多个 
	
	 特别：[]:代表可选  		
###4.查看命令的帮助信息 
	 command --help 		可以快速查看命令的帮助信息 
 
	 man command			查看command的使用手册 
使用man是的操作键  

|  操作键	|	功能     
|  :---:	| :----- 
|  空格键 	|  显示手册的下一屏 
|  Enter键	|  一次滚动手册页的一行 
|  b		|  回滚一屏 
|  f	    |  前滚一屏 
|  q		|  退出 
|  /word	|  搜索word字符串	,word为搜索的内容 
	 