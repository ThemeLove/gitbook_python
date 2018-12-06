# linux的常用知识

#####1.linux中目录或文件名称最长不超过256个字符  
#####2.linux中以.开头的为隐藏文件，需要用-a才能显示。  
#####3.linux中.代表当前目录;..代表上一级目录；例如cd .  和cd ..  
#####4.linux中目录和文件在终端中的显示颜色不一样，通常目录显示蓝色，文件显示白色。显示详细信息时目录以d开头  
#####5.linux通配符\(通常与其他命令配合使用\)

| 常用参数 | 功能 |
| :---: | :--- |
| \* | 代表任意个数字符 |
| ？ | 代表任意一个字符，至少1个 |
| \[\] | 代表可以匹配字符组中的任一一个 |
| \[abc\] | 匹配a、b、c中的任意一个 |
| \[a-f\] | 匹配从a到f范围内的任意一个字符 |

#####6.Linux终端命令格式

```
 command [-options] [parameter] 
 command:命令名，相应功能的英文单词或单词的缩写 
 特别：[]:代表可选
```

#####7.查看命令的帮助信息

```
 command --help         可以快速查看命令的帮助信息   
 man command            查看command的使用手册
```

使用man命令时配合使用的操作键

| 操作键 | 功能 |
| :---: | :--- |
| 空格键 | 显示手册的下一屏 |
| Enter键 | 一次滚动手册页的一行 |
| b | 回滚一屏 |
| f | 前滚一屏 |
| q | 退出 |
| /word | 搜索word字符串    ,word为搜索的内容 |

#####8.相对路路径和绝对路径  
    相对路径：在输入路径时，最前面不是`/`或者`~`:即表示相对`当前目录`所在的目录位置  
    绝对路径：在输入路径时，最前面是`/`或者`~`：即表示从`根目录`/`家目录`开始的具体目录位置  
    在linux中`/`表示根目录；`~`表示家目录  
#####9.touch 命令 ：创建文件或者修改文件时间

```
如果文件不存在，可以创建一个空白文件
如果文件已经存在，可以修改文件的末次修改时间
```

#####10.管道 \|  
     linux允许将`一个命名的输出`可以`通过管道`作为`另一个命令的输入`

```
 例如：ls -lha ~ | more  表示将查找家目录下所有文件的结果用more命令查看
```

#####11.ping 127.0.0.1 :可以检测本地网卡是否工作正常

#####12.常见服务器默认端口号：

| 服务器类型 | 端口号 |
| :--- | :--- |
| SSH服务器 | 22 |
| Web服务器 | 80 |
| HTTPS服务器 | 445 |
| FTP服务器 | 21 |

#####13.FileZilla 是一款免费开源的FTP软件，分为客户端版本和服务器版本，具备所有的FTP软件功能。

```
1.可以用图像化界面的方式来管理本地和远程服务器文件，window上比较好用。
2.FileZilla在传输文件时，使用的是FTP服务，而不是SSH服务，因此端口号应该设置为21
```

#####14.免密码登录

```
步骤
    生成公钥：执行ssh-keyten 即可生成SSH钥匙，一路回车即可 
    上传公钥到服务器：执行ssh-copy-id -p port user@remote ,可以让远程服务器记住我们的公钥
```

#####15.非对称加密算法

* `本地`使用`私钥`对数据进行`加密/解密`
* `服务器`使用`公钥`对数据进行`加密/解密`

#####16.配置远程计算机的别名

```
可以在本地~/.ssh/config 里面追加以下内容：
    Host alias
        HostName ip地址
        User     itheima 
        Port     22 
保存之后即可使用 ssh alias 实现远程登录了，scp 中同样可以使用。例如：scp -r ~/Desktop alias:Desktop/demo
```

#####17.Linux 文件或目录权限说明

![](/assets/Linux文件权限说明.png)

#####18.passwd 文件

`/ect/passwd` 文件存放的是用户的信息，由6个分号组成的7个信息，分别是：

```
    用户名 
    密码（x 表示加密的密码）
    UID (用户标示)
    GID(组标示)
    用户全名或者本地账号
    家目录
    登录使用的shell,就是登录之后，使用的终端命令，ubuntu默认是dash,dash下会显示目录和文件没哟颜色区分，通常bash显示效果会更好
```

#####19.usermod

> 1. `usermod 可以用来设置用户的主组/附加组和登录shell`
> 2. `默认使用useradd添加的用户是没有权限使用sudo 以root身份执行命令的，可以使用一下命令，将用户添加到sudo的附加组中：usermod -G sudo 用户名`

#####20.在linux中，绝大多数可执行文件都是保存在`/bin`;`/sbin`;`/usr/bin`;`/usr/sbin`中

> `1./bin (binary)是二进制执行文件目录，主要用于具体应用`
>
> `2./sbin (system binary) 是系统管理员专用的二进制代码存放目录，主要用于系统管理`
>
> `3./usr/bin (user commands fro application) 后期安装的一些软件`
>
> `4./usr/sbin (super user commands for application) 超级用户的一些管理程序`

#####21.cd 这个终端命令是内置在系统内核中的，没有独立的文件，因此用`which`无法找到

#####22.chmod在设置权限时，可以简单地使用三个数字分别对应`拥有者`/`组`/`其他`用户的权限
<table border="1"  cellpadding="10" style="border-collapse:collapse">
    <tr>
        <th colspan="3">拥有者</th>
        <th colspan="3">组</th>
        <th colspan="3">其他</th>
    </tr>
    <tr>
        <td>r</td>
        <td>w</td>
        <td>x</td>
        <td>r</td>
        <td>w</td>
        <td>x</td>
        <td>r</td>
        <td>w</td>
        <td>x</td>
    </tr>
    <tr>
        <td>4</td>
        <td>2</td>
        <td>1</td>
        <td>4</td>
        <td>2</td>
        <td>1</td>
        <td>4</td>
        <td>2</td>
        <td>1</td>
    </tr>
</table>

#####23.打包/解包
> `tar`是Linux中最常用的备份工具，此命令可以把`一系列文件`打包到`一个大文件中`，也可以把`一个打包的大文件`恢复成一系列文件

`tar` 的选项说明

| 选项 | 说明 |
| :--- | :--- |
| c | 生成档案文件，创建打包文件 |
| x | 解开档案文件 |
| v | 列出归档解档的详细过程，显示进度 |
| f | 指定档案文件名称，f后面一定是.tar，所以必须放在选项最后 |   
| z	| 调用gzip 进行压缩，这是文件名要以.tar.gz结尾

#####24.gzip 压缩/解压缩

    1. tar 与 gzip 命令结合可以实现文件 打包和压缩
    2. tar 只负责打包文件，不负责压缩
    3. gzip 压缩tar 打包后的文件，其扩展名一般用 xxx.tar.gz
    4. 在tar命令中有一个选项-z可以调用gzip,从而可以方便的实现压缩和解压缩的功能
    5. -C 选项可以指定解压缩的目录，注意解压缩的目录必须存在，解包的时候不指定目录默认解包到当前目录 
    	例子：
			压缩文件：tar -zcvf 打包文件.tar.gz 被压缩的文件/路径 
			解压缩文件：tar -zxvf 打包文件.tar.gz -C 解压缩的目录 
#####25.bzip2(two) 压缩/解压缩 

    1. tar 与 bzip2 命令结合可以实现文件`打包和压缩`
    2. tar 只负责打包文件，不负责压缩
    3. bzip 压缩tar ,打包后的文件，其扩展名一般用xxx.tar.bz2
    4. 在tar命令中有一个选项-j可以调用bzip,从而可以方便的实现压缩和解压缩的功能 
    5.-C 选项可以指定解压缩的目录，注意解压缩的目录必须存在，解包的时候不指定目录默认解包到当前目录 
    	例子：
			压缩文件：tar -jcvf 打包文件.tar.bz2 被压缩的文件/路径 
			解压缩文件：tar -jxvf 打包文件.tar.bz2 -C 解压缩的目录 
#####26.Linux中/opt目录通常用来放置安装的第三方软件 
#####27.Ubuntu中默认安装的火狐浏览器为国际版，不能同中国版互通同步书签等，如需需要重新安装。
#####28.Ubuntu中安装第三方软件的步骤 
    1. 先从官网中下载好Linux版本的软件安装包，一般是以.gz 或者.bz2后缀名结尾的压缩包。
       然后在终端中解压缩到/opt目录下（目录可指定）：
	   例如：	
			tar -zxvf 压缩包.tar.gz -C /opt
		或者
			tar -jxvf 压缩包.tar.bz2 -C /opt 
	2. 配置快捷方式 
		这里以firefox为例，假设firefox安装在/opt/firefox目录下
		（1）. 进入 /usr/share/applications目录下创建firefox.desktop
		   命令如下：cd /usr/share/applications
					sudo touch firefox.desktop
		（2）. 用编辑器打开firefox.desktop,编辑内容为： 
		    [Desktop Entry]
			Name=firefox
			Name[zh_CN]=火狐浏览器
			Comment=火狐浏览器
			Exec=/opt/firefox/firefox
			Icon=/opt/firefox/browser/icons/mozicon128.png #可能在其他位置
			Terminal=false
			Type=Application
			Categories=Application
			Encoding=UTF-8
			StartupNotify=true 

#####29.Linux中在终端中用 `apt install` 安装的软件都可以用 `apt remove` 来卸载   	


















