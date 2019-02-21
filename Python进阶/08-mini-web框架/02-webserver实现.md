####1.webserver实（webserver.py） 
	主要功能： 
		1.支持web框架可配置(和web框架解耦合)
		2.支持静态资源目录可配置 
	
	知识点： 
		1.动态添加系统路径 
			sys.path.append(dynamic_path)
		2.动态导入web框架模块 
			frame = __import__(frame_name)
		    frame_application = getattr(frame, app_name)



```python

	import socket
	import multiprocessing
	import re
	import logging
	import sys
	
	
	class WSGIServer:
	    def __init__(self, port, application, static_path):
	        # 创建tcp server socket对象
	        self.server_socket = socket.socket(socket.AF_INET, socket.SOCK_STREAM)
	        self.port = port
	        self.application = application
	        self.static_path = static_path
	
	    def run(self):
	        # 绑定端口,ip穿“”表示默认绑定本地ip,可以通过ifconfig命令来查看
	        self.server_socket.bind(("", self.port))
	        # 开启监听
	        self.server_socket.listen(1024)
	        print("服务端已开启(port:%s)" % self.port)
	        # 循环接受客户端连接
	        while True:
	            client_socket, client_addr = self.server_socket.accept()
	            print("客户端已连接(ip：%s,port:%s)" % (client_addr[0], client_addr[1]))
	            p = multiprocessing.Process(target=self.server_client, args=(client_socket,))
	            p.start()
	            client_socket.close()
	        self.server_socket.close()
	
	    @staticmethod
	    def get_response_status_line_and_header(status, response_header_params):
	        """设置http响应状态行和响应头"""
	
	        if status == "200":
	            response_status_line = "HTTP/1.1 %s OK\r\n" % status
	        elif status == "404":
	            response_status_line = "HTTP/1.1 %s Fail\r\n" % status
	        else:
	            response_status_line = "HTTP/1.1 %s Fail\r\n" % status
	
	        response_header = ""
	        #  拼接response_header
	        for temp in response_header_params:
	            response_header += "%s:%s\r\n" % (temp[0], temp[1])
	
	        return response_status_line + response_header + "\r\n"
	
	    def server_client(self, client_socket):
	        client_data = client_socket.recv(1024).decode("utf-8")
	        print("\r\n\r\nstart>>>>>>>>>>>>>>>>>>>>")
	        print("client_data=", client_data)
	
	        # 对客户端参数进行处理，获取用户浏览器输入的参数
	        client_param = ""
	        if client_data:
	            client_data_list = client_data.splitlines()
	            if client_data_list and client_data_list[0]:
	                print("list[1]=", client_data_list[0])
	                client_param = re.search(r"/[^ ]*", client_data_list[0]).group()
	
	        # 根据是否成功获取到客户端参数，分别处理
	        # 注意header 和 body之间时通过一个空行来区分的
	        response_status_line_and_header_ok = self.get_response_status_line_and_header("200", [("Server", "webserver")])
	        response_status_line_and_header_fail = self.get_response_status_line_and_header("404", [("Server", "webserver")])
	
	        if client_param and (not client_param.endswith(".html")):  # 说明请求的是静态资源
	            if client_param != "/":  # 排除没有参数和“/”，因为这2中情况按/index.html处理
	                response_path = self.static_path + client_param
	                print("response_path=", response_path)
	                try:
	                    rf = open(response_path, "rb")
	                    client_socket.send(response_status_line_and_header_ok.encode("utf-8"))
	                    client_socket.send(rf.read())
	                    print("end>>>>>>>>>>>>>>>>>>>>\r\n\r\n")
	                    rf.close()
	                except IOError as e:
	                    client_socket.send(response_status_line_and_header_fail.encode("utf-8"))
	                    client_socket.send("error----->file not found".encode("utf-8"))
	                    print("end>>>>>>>>>>>>>>>>>>>>\r\n\r\n")
	                    logging.exception(e)
	
	        if client_param:
	            if client_param == "/":
	                client_param = "/index.html"
	
	            # 动态返回的结果
	            if client_param.endswith(".html"):
	                client_params = dict()
	                client_params["path"] = client_param
	
	                response = self.application(client_params, self.get_response_status_line_and_header)
	                # print("response:", response)
	                client_socket.send(response.encode("utf-8"))
	                print("end>>>>>>>>>>>>>>>>>>>>\r\n\r\n")
	
	        # 关闭socket
	        client_socket.close()  # 在子进程也关闭socket连接
	
	
	def main():
	    args = sys.argv
	    if len(args) == 3:
	        try:
	            port = int(args[1])
	            frame_app_name = args[2]
	        except ValueError as e:
	            logging.exception(e)
	            print("参数输入错误！")
	            return
	
	        ret = re.match("([^:]+):(.*)", frame_app_name)
	        if ret:
	            frame_name = ret.group(1)
	            app_name = ret.group(2)
	        else:
	            print("参数输入错误")
	            return
	    else:
	        print("请传入正确的参数个数")
	        return
	
	    with open("./webserver.conf") as f:
	        server_conf = f.read()
	    print("type of server_conf", type(server_conf))
	    conf_dict = eval(server_conf)
	    print("conf_dict", conf_dict)
	    static_path = conf_dict["static_path"]
	    dynamic_path = conf_dict["dynamic_path"]
	
	    sys.path.append(dynamic_path)
	
	    # 动态导入
	    frame = __import__(frame_name)
	    frame_application = getattr(frame, app_name)
	
	    server = WSGIServer(port, frame_application, static_path)
	    server.run()
	
	
	if __name__ == "__main__":
	main()
```