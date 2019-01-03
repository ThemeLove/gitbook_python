####1.简版单进程、单线程、While True循环http服务器
代码示例：

```python

	import logging
	
	
	"""
	该版本为单进程、单线程、while True简单http服务器版本
	1.因为不涉及多进程多线程，所以当一个socket连接时，会阻塞其他客户端socket的连接；
	只有当一个客户端连接socket处理完成时，才能处理下一个客户端socket连接
	"""
	
	
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
	
	    # 根据是否成功获取到客户端参数，分别处理
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
	    client_socket.close()  # 需要手动关闭客户端套接字，不然浏览器不知道服务端的响应是否结束，会一直转圈圈。
	
	
	def main():
	    # 创建IPv4的tcp服务端socket
	    tcp_server_socket = socket.socket(socket.AF_INET, socket.SOCK_STREAM)
	    # 绑定服务端ip和端口
	    tcp_server_socket.bind(("10.200.202.22", 8888))
	    print("服务器（10.200.202.22)已开启，等待连接中...")
	    # 开启服务器监听
	    tcp_server_socket.listen(1024)
	    # 循环接受客户端的连接
	    while True:
	        client_socket, client_address = tcp_server_socket.accept()  # 默认阻塞状态，只有当有新的客户端连接时才能解阻塞
	        serve_client(client_socket)
	    # 关闭服务端socket
	    tcp_server_socket.close()
	
	
	if __name__ == "__main__":
		main()
```