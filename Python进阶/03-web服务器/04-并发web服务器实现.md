####1.多进程web服务器实现 
注意事项：
>-在主进程关闭客户端连接才有用，因为在主进程持有的才是客户端socket连接的直接引用，子进程只是主进程的拷贝 

代码示例：

```python

	import multiprocessing
	import socket
	import re
	import logging
	
	
	def serve_client(client_socket):
	    client_data = client_socket.recv(1024).decode("utf-8")
	    print("\r\n\r\n\r\nstart>>>>>>>>>>>>>>>>>>>>")
	    print("client_data=", client_data)
	
	    # 对客户端参数进行处理，获取用户浏览器输入的参数
	    client_param = ""
	    if client_data:
	        client_data_list = client_data.splitlines()
	        if client_data_list and client_data_list[0]:
	            print("list[1]=", client_data_list[0])
	            client_param = re.search(r"/[^ ]*", client_data_list[0]).group()
	
	    # 根据是否成功获取到客户端参数，费别处理
	    response_header_ok = "HTTP/1.1 200 OK\r\n\r\n"  # 注意header 和 body之间时通过一个空行来区分的
	    response_header_fail = "HTTP/1.1 404 FAIL\r\n\r\n"
	    if client_param:
	        if client_param == "/":
	            client_param = "/index.html"
	        response_path = "./html_test"+client_param
	        print("response_path=", response_path)
	        try:
	            rf = open(response_path, "rb")
	            client_socket.send(response_header_ok.encode("utf-8"))
	            client_socket.send(rf.read())
	            rf.close()
	        except IOError as e:
	            client_socket.send(response_header_fail.encode("utf-8"))
	            client_socket.send("error----->file not found".encode("utf-8"))
	            logging.exception(e)
	    else:  # 成功获取到用户的参数
	        client_socket.send(response_header_fail.encode("utf-8"))
	        client_socket.send("error----->params error".encode("utf-8"))
	        pass
	    # 关闭socket
	    client_socket.close()  # 在子进程也关闭socket连接
	
	
	def main():
	    # 创建tcp服务端套接字
	    tcp_server_socket = socket.socket(socket.AF_INET, socket.SOCK_STREAM)
	    # 绑定服务端ip和端口
	    tcp_server_socket.bind(("10.200.202.22", 8887))
	    print("服务器（10.200.202.22)已开启，等待连接中...")
	    # 开启监听
	    tcp_server_socket.listen(1024)
	
	    while True:
	        # 接收客户端连接
	        tcp_client_socket, tcp_client_address = tcp_server_socket.accept()
	        # 创建一个进程
	        p = multiprocessing.Process(target=serve_client, args=(tcp_client_socket, ))
	        # 开启进程
	        p.start()
	        # 在主进程关闭客户端连接才有用，因为在主进程持有的才是客户端socket连接的直接引用，子进程只是主进程的拷贝
	        tcp_client_socket.close()
	    # 关闭服务端连接
	    tcp_server_socket.close()
	
	
	if __name__ == "__main__":
		main()
``` 

####2.多线程web服务器实现 
代码示例：

```python

	import threading
	import socket
	import logging
	import re
	
	
	def serve_client(client_socket):
	    client_data = client_socket.recv(1024).decode("utf-8")
	    print("\r\n\r\n\r\nstart>>>>>>>>>>>>>>>>>>>>")
	    print("client_data=", client_data)
	
	    # 对客户端参数进行处理，获取用户浏览器输入的参数
	    client_param = ""
	    if client_data:
	        client_data_list = client_data.splitlines()
	        if client_data_list and client_data_list[0]:
	            print("list[1]=", client_data_list[0])
	            client_param = re.search(r"/[^ ]*", client_data_list[0]).group()
	
	    # 根据是否成功获取到客户端参数，费别处理
	    response_header_ok = "HTTP/1.1 200 OK\r\n\r\n"  # 注意header 和 body之间时通过一个空行来区分的
	    response_header_fail = "HTTP/1.1 404 FAIL\r\n\r\n"
	    if client_param:
	        if client_param == "/":
	            client_param = "/index.html"
	        response_path = "./html_test"+client_param
	        print("response_path=", response_path)
	        try:
	            rf = open(response_path, "rb")
	            client_socket.send(response_header_ok.encode("utf-8"))
	            client_socket.send(rf.read())
	            rf.close()
	        except IOError as e:
	            client_socket.send(response_header_fail.encode("utf-8"))
	            client_socket.send("error----->file not found".encode("utf-8"))
	            logging.exception(e)
	    else:  # 成功获取到用户的参数
	        client_socket.send(response_header_fail.encode("utf-8"))
	        client_socket.send("error----->params error".encode("utf-8"))
	        pass
	    # 关闭socket
	    client_socket.close()
	
	
	def main():
	    # 创建tcp服务端socket
	    tcp_server_socket = socket.socket(socket.AF_INET, socket.SOCK_STREAM)
	    # 绑定服务端端口
	    tcp_server_socket.bind(("10.200.202.22", 8889))
	    print("服务器（10.200.202.22)已开启，等待连接中...")
	    # 开启服务端监听
	    tcp_server_socket.listen(1024)
	    while True:
	        # 接收客户端连接
	        tcp_client_socket, tcp_client_address = tcp_server_socket.accept()
	        # 创建线程
	        new_thread = threading.Thread(target=serve_client, args=(tcp_client_socket,))
	        # 开启线程
	        new_thread.start()
	
	    tcp_server_socket.close()
	
	
	if __name__ == "__main__":
		main()
```  

####3.gevent实现web服务器
代码示例：

```python

	import gevent
	from gevent import monkey
	import socket
	import re
	import logging
	
	
	def serve_client(client_socket):
	    client_data = client_socket.recv(1024).decode("utf-8")
	    print("\r\n\r\n\r\nstart>>>>>>>>>>>>>>>>>>>>")
	    print("client_data=", client_data)
	
	    # 对客户端参数进行处理，获取用户浏览器输入的参数
	    client_param = ""
	    if client_data:
	        client_data_list = client_data.splitlines()
	        if client_data_list and client_data_list[0]:
	            print("list[1]=", client_data_list[0])
	            client_param = re.search(r"/[^ ]*", client_data_list[0]).group()
	
	    # 根据是否成功获取到客户端参数，费别处理
	    response_header_ok = "HTTP/1.1 200 OK\r\n\r\n"  # 注意header 和 body之间时通过一个空行来区分的
	    response_header_fail = "HTTP/1.1 404 FAIL\r\n\r\n"
	    if client_param:
	        if client_param == "/":
	            client_param = "/index.html"
	        response_path = "./html_test"+client_param
	        print("response_path=", response_path)
	        try:
	            rf = open(response_path, "rb")
	            client_socket.send(response_header_ok.encode("utf-8"))
	            client_socket.send(rf.read())
	            rf.close()
	        except IOError as e:
	            client_socket.send(response_header_fail.encode("utf-8"))
	            client_socket.send("error----->file not found".encode("utf-8"))
	            logging.exception(e)
	    else:  # 成功获取到用户的参数
	        client_socket.send(response_header_fail.encode("utf-8"))
	        client_socket.send("error----->params error".encode("utf-8"))
	        pass
	    # 关闭socket
	    client_socket.close()
	
	
	def main():
	    # 创建tcp服务端socket
	    tcp_server_socket = socket.socket(socket.AF_INET, socket.SOCK_STREAM)
	    # 绑定服务端ip和端口
	    tcp_server_socket.bind(("10.200.202.22", 8890))
	    print("服务器（10.200.202.22)已开启，等待连接中...")
	    # 开启监听
	    tcp_server_socket.listen(1024)
	    # 将程序中用到的耗时操作的代码，切换为gevent中自己实现的模块,即打补丁
	    monkey.patch_all()
	    while True:
	        tcp_client_socket, tcp_client_address = tcp_server_socket.accept()
	        gevent.joinall([ # 将协程统一管理
	            gevent.spawn(serve_client, tcp_client_socket)
	        ])
	
	
	if __name__ == "__main__":
		main()
```

####4.单线程非阻塞实现并发的原理

	实现原理：
	    socket.setblocking(False)的作用是：设置当前sokcet为非阻塞，即当调用socket的阻塞api时，不会去等待，会立刻尝试获得结果；
	    当没有成功时会报错，编写代码时需要进行异常处理
	    比如：socket.accept()会尝试立刻获得一个客户端连接，如果此时没有客户端连接的话会报错；
	         socket.recv()会尝试立刻获得一个客户端数据，如果客户端只是连接上，但是没有立刻发送数据，就会报错。 
代码示例：

```python
	
	import socket
	
	
	def main():
	    # 创建tcp服务器套接字
	    tcp_server_socket = socket.socket(socket.AF_INET, socket.SOCK_STREAM)
	    # 绑定服务起ip和端口，ip为空默认为当前本机ip
	    tcp_server_socket.bind(("", 8888))
	    print("服务器（10.200.202.22)已开启，等待连接中...")
	    # 开启服务器监听
	    tcp_server_socket.listen(1024)
	    # 设置tcp服务器套接字为非阻塞
	    tcp_server_socket.setblocking(False)
	    # 创建一个空列表，用于存放接收的客户端连接
	    tcp_client_socket_list = list()
	
	    while True:
	        try:
	            # 接收客户端套接字
	            tcp_client_socket, tcp_client_address = tcp_server_socket.accept()
	            # 设置客户端套接字为非阻塞，并添加到客户端socket列表中
	            tcp_client_socket.setblocking(False)
	            # 将接收的连接添加到列表中
	            tcp_client_socket_list.append(tcp_client_socket)
	        except Exception as e:  # 捕获异常，说明没有客户端连接
	            pass
	            # print("当前没有客户端的连接...")
	            # logging.exception(e)
	
	        for tcp_client_socket in tcp_client_socket_list:
	            try:
	                client_data = tcp_client_socket.recv(1024)
	                if client_data:
	                    print("成功获取到客户端数据 data=", client_data.decode("utf-8"))
	                    tcp_client_socket.send("hello~client:num =%d".encode("utf-8") % (tcp_client_socket.fileno(),))
	                    tcp_client_socket_list.remove(tcp_client_socket)
	                else:
	                    print("数据为空，客户端关闭了连接")
	                    tcp_client_socket_list.remove(tcp_client_socket)
	            except Exception as e:  # 捕获到异常，说明该客户端socket连接暂时没有发送数据
	                pass
	                # logging.exception(e)
	
	
	if __name__ == "__main__":
		main()
``` 

####5.单进程、单线程、非阻塞、长连接web服务器实现
    长连接的实现原理
	1.HTTP1.1协议默认支持长连接，服务端循环接受客户端连接数据；
	  当服务端尝试接收数据没有报错时，说明客户端接收到了数据，
	  当数据不为空时说明客户端正常请求；数据为空时说明客户端关闭了连接，
	  这时服务端才主动close分配给该客户端的socket 
	2.利用Http协议头中的Content-Length，Content-Length告知客户端本次服务端响应体response-body的大小， 
	客户端根据Content-Length获取内容并显示。 因为在不设置Content-Length和关闭socket连接的情况下，客户端会以为本次请求服务端响应还没有结束，会一直等待，变现为浏览器一直转圈，知道其他行为解阻塞为止。

代码示例：

```python
	import socket
	import re
	import logging
	
	
	def serve_client(client_socket, client_socket_list):
	    client_data = ""
	    try:
	        client_data = client_socket.recv(1024).decode("utf-8")
	    except Exception as e:  # socket尝试立刻接收数据失败
	        return
	
	    print("\r\n\r\n\r\nstart>>>>>>>>>>>>>>>>>>>>")
	    print("client_data=", client_data)
	
	    # 对客户端参数进行处理，获取用户浏览器输入的参数
	    client_param = ""
	    if client_data:
	        client_data_list = client_data.splitlines()
	        if client_data_list and client_data_list[0]:
	            print("list[0]=", client_data_list[0])
	            ret = re.search(r"/[^ ]*", client_data_list[0])
	            if ret:
	                client_param = ret.group()
	            # 根据是否成功获取到客户端参数，费别处理
	        response_header_ok = "HTTP/1.1 200 OK\r\n"  # 注意header 和 body之间时通过一个空行来区分的
	        response_header_ok += "Content-Type:charset=utf-8\r\n"
	        response_header_fail = "HTTP/1.1 404 FAIL\r\n"
	        response_header_fail += "Content-Type:charset=utf-8\r\n"
	        response_body = ""
	        if client_param:  # 成功获取到用户的参数
	            if client_param == "/":
	                client_param = "/index.html"
	            response_path = "./html_test" + client_param
	            print("response_path=", response_path)
	            try:
	                rf = open(response_path, "rb")
	                response_body = rf.read()
	                # 拼接Content-Length请求头，请求头用于告诉浏览器请求体的内容的长度，让浏览器识别本次返回结束，不再等待
	                # 注意header 和 body之间时通过一个空行来区分的
	                response_header_ok += "Content-Length:%d\r\n\r\n" % (len(response_body),)
	                client_socket.send(response_header_ok.encode("utf-8"))
	                client_socket.send(response_body)
	                rf.close()
	            except IOError as e:
	                response_body = "error----->没有相关资源"
	                response_header_fail += "Content-Length:%d\r\n\r\n" % (len(response_body.encode("utf-8")),)
	                client_socket.send(response_header_fail.encode("utf-8"))
	                client_socket.send(response_body.encode("utf-8"))
	                logging.exception(e)
	        else:  # 获取用户的参数失败
	            response_body = "error----->params error"
	            response_header_fail += "Content-Length:%d\r\n\r\n" % (len(response_body.encode("utf-8")),)
	            client_socket.send(response_header_fail.encode("utf-8"))
	            client_socket.send(response_body.encode("utf-8"))
	    else:  # 如果接收的数据为空，则说明客户端关闭了连接，服务端这时可以关闭该连接
	        # 处理完毕关闭socket，并将其从列表中移除
	        print("客户端断开连接...")
	        client_socket.close()
			client_socket_list.remove(client_socket)
	
	
	def main():
	    # 创建tcp服务端socket
	    tcp_server_socket = socket.socket(socket.AF_INET, socket.SOCK_STREAM)
	    # 绑定服务器socket的ip和端口号，ip传空表示绑定本地
	    tcp_server_socket.bind(("", 8111))
	    print("服务器（10.200.202.22)已开启，等待连接中...")
	    # 开启监听
	    tcp_server_socket.listen(1024)
	    # 设置为tcp服务端socket为非阻塞
	    tcp_server_socket.setblocking(False)
	    # 创建存放客户端连接的list
	    tcp_client_socket_list = list()
	    while True:
	        try:
	            # 接收客户端套接字
	            tcp_client_socket, tcp_client_address = tcp_server_socket.accept()
	            # 设置客户端套接字为非阻塞，并添加到客户端socket列表中
	            tcp_client_socket.setblocking(False)
	            # 将接收的连接添加到列表中
	            tcp_client_socket_list.append(tcp_client_socket)
	        except Exception as e:  # 服务端socket尝试立即获取客户端连接失败
	            pass
	            # print("当前没有客户端的连接...")
	            # logging.exception(e)
	
	        for tcp_client_socket in tcp_client_socket_list:
	            serve_client(tcp_client_socket, tcp_client_socket_list)
	
	
	if __name__ == "__main__":
		main()
```

####6.epoll版本web服务器实现（需要导入select模块）
	epoll实质是IO多路复用以及基于事件驱动，大大提高了效率和内存空间
	
	# 设置可以重复使用绑定的信息
	socket.setsockopt(socket.SOL_SOCKET, socket.SO_REUSEADDR,1)
代码示例：

```python

	import select
	import re
	import socket
	import logging
	
	
	def serve_client(fd, epl, client_socket_dict):
	    client_data = ""
	    client_socket = None
	    try:
	        client_socket = client_socket_dict[fd]
	        client_data = client_socket.recv(1024).decode("utf-8")
	    except Exception as e:  # socket尝试立刻接收数据失败
	        return
	
	    print("\r\n\r\n\r\nstart>>>>>>>>>>>>>>>>>>>>")
	    print("client_data=", client_data)
	
	    # 对客户端参数进行处理，获取用户浏览器输入的参数
	    client_param = ""
	    if client_data:
	        client_data_list = client_data.splitlines()
	        if client_data_list and client_data_list[0]:
	            print("list[0]=", client_data_list[0])
	            ret = re.search(r"/[^ ]*", client_data_list[0])
	            if ret:
	                client_param = ret.group()
	            # 根据是否成功获取到客户端参数，费别处理
	        response_header_ok = "HTTP/1.1 200 OK\r\n"  # 注意header 和 body之间时通过一个空行来区分的
	        response_header_ok += "Content-Type:charset=utf-8\r\n"
	        response_header_fail = "HTTP/1.1 404 FAIL\r\n"
	        response_header_fail += "Content-Type:charset=utf-8\r\n"
	        response_body = ""
	        if client_param:  # 成功获取到用户的参数
	            if client_param == "/":
	                client_param = "/index.html"
	            response_path = "./html_test" + client_param
	            print("response_path=", response_path)
	            try:
	                rf = open(response_path, "rb")
	                response_body = rf.read()
	                # 拼接Content-Length请求头，请求头用于告诉浏览器请求体的内容的长度，让浏览器识别本次返回结束，不再等待
	                # 注意header 和 body之间时通过一个空行来区分的
	                response_header_ok += "Content-Length:%d\r\n\r\n" % (len(response_body),)
	                client_socket.send(response_header_ok.encode("utf-8"))
	                client_socket.send(response_body)
	                rf.close()
	            except IOError as e:
	                response_body = r"<meta charset='UTF-8'>error----->没有相关资源"
	                response_header_fail += "Content-Length:%d\r\n\r\n" % (len(response_body.encode("utf-8")),)
	                client_socket.send(response_header_fail.encode("utf-8"))
	                client_socket.send(response_body.encode("utf-8"))
	                logging.exception(e)
	        else:  # 获取用户的参数失败
	            response_body = "error----->params error"
	            response_header_fail += "Content-Length:%d\r\n\r\n" % (len(response_body.encode("utf-8")),)
	            client_socket.send(response_header_fail.encode("utf-8"))
	            client_socket.send(response_body.encode("utf-8"))
	    else:  # 如果接收的数据为空，则说明客户端关闭了连接，服务端这时可以关闭该连接
	        # 处理完毕关闭socket，并将其从列表中移除
	        print("客户端断开连接...")
	        client_socket.close()
	        epl.unregister(fd)
	        del client_socket_dict[fd]
	
	
	def main():
	    # 创建tcp服务端套接字
	    tcp_server_socket = socket.socket(socket.AF_INET, socket.SOCK_STREAM)
	    # 绑定服务器ip和端口号
	    tcp_server_socket.bind(("", 8888))
	    print("服务器（10.200.202.22)已开启，等待连接中...")
	    # 开启监听
	    tcp_server_socket.listen(1024)
	    # 创建一个epoll对象
	    epl = select.epoll()
	    # 将监听套接字对应的fd注册到epoll中
	    epl.register(tcp_server_socket.fileno(), select.EPOLLIN)
	    # 创建一个存放客户端套接字的和索引（fd）的字典
	    fd_event_dict = dict()
	    while True:
	        fd_event_list = epl.poll()  # 默认会阻塞，直到os检测到数据到来，通过事件通知方式告知这个程序，此时才会解堵塞
	        for fd, event in fd_event_list:
	            if fd == tcp_server_socket.fileno():
	                tcp_client_socket, tcp_client_address = tcp_server_socket.accept()
	                epl.register(tcp_client_socket.fileno(), select.EPOLLIN)
	                fd_event_dict[tcp_client_socket.fileno()] = tcp_client_socket
	            elif event == select.EPOLLIN:
	                # 判断已经连接的客户端是否有数据发送过来
	                serve_client(fd, epl, fd_event_dict)
	
	
	if __name__ == "__main__":
		main()
```