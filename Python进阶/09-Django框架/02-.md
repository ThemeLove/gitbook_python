####1.安装虚拟环境包（VirtualEnv）
	
	1.sudo pip install virtualenv    安装虚拟环境包 
	2.sudo pip install virtualenvwrapper	安装虚拟环境扩展包  
	3.编辑家目录下面的.bashrc文件，添加下面两行 
		export WORKON_HOME=$HOME/.virtualenvs 
		source /usr/local/bin/virtualenvwrapper.sh 
	4.使用source .bashrc使其生效一下
 
	注意：如果是python3 使用pip3 代替pip  

####2.创建虚拟环境 
	1. mkvirtualenv -p python3 虚拟环境 : 创建python3环境的虚拟环境
	   例：mkvirtualenv -p python3 test 

	2. 进入虚拟环境工作
	   workon 虚拟环境环境  
 
	3. 查看一共有多少个虚拟环境   
	   workon 空格 + 两个tab键   

	4. 退出虚拟环境   
	   deactivate    

	5. 删除虚拟环境 
	   rmvirtualenv 虚拟环境名  

####3.虚拟环境下安装Django  
	1. pip install django   
	
	注意：虚拟环境下安装python软件包，不能用sudo pip install 包名，这个命令会把包安装到真实环境中。  

####4. apt install 和 pip install的区别 
	apt install 软件名	：	是Linux系统中安装软件，例 apt install mysql     
	pip install 包名		：  是python中安装第三方依赖包，pip命令依赖python环境   

####5. 查看python中所有的软件包  
	pip list  
	pip freeze  

####6. 创建Django项目 
	1.django-admin startproject 项目名	： 创建Django项目  
	
	注意：
		可能会报错：Command 'django-admin' not found 
	解决：
		1.可以用find / -name django-admin.py 找到django-admin.py的位置，然后用python手动运行创建项目 
![](https://i.imgur.com/bWGSUzn.png)  





	