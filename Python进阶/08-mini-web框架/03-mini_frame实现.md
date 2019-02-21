####1.mini_frame实现（mini_frame.py）
	主要功能：
		1.支持路由功能（装饰器实现） 
		2.支持logger日志功能 
		3.连接数据库 
		4.正则替换模板 
		5.urllib.parse.quoto 和 urllib.parse.unquoto 完成url的encode和decode 
		6.json模块的使用
	知识点：
		1.允许数据库远程连接
	

```python

	import pymysql
	import re
	import logging
	import json
	from urllib.parse import unquote
	
	
	URL_FUNC_DICT = dict()
	DATABASE_IP = "10.200.202.16"  # 这样定义的目的是方便家里电脑远程连接公司数据库，共用一套代码
	
	
	def route(url):
	    """mini_frame路由装饰器功能"""
	    def set_func(func):
	        URL_FUNC_DICT[url] = func
	
	        def call_func(*args, **kwargs):
	            return func(*args, **kwargs)
	        return call_func
	    return set_func
	
	
	def application(client_params, get_response_status_line_and_header):
	    print("mini_frame----->application")
	    print("client_params", client_params)
	
	    # 添加log日志功能
	    # 1.创建一个logger
	    logger = logging.getLogger()
	    logger.setLevel(logging.INFO)
	
	    # 2.创建一个handler,用于写入日志文件
	    logfile = "./log.txt"
	    filehandler = logging.FileHandler(logfile, mode="a")  # mode="a" 表示追加写入
	    filehandler.setLevel(logging.INFO)
	
	    # 3.创建一个handler,用于输出到控制台
	    consolehandler = logging.StreamHandler()
	    consolehandler.setLevel(logging.INFO)
	
	    # 4.定义handler的输出格式
	    formatter = logging.Formatter("%(asctime)s - %(filename)s[line:%(lineno)d] - %(levelname)s: %(message)s")
	    filehandler.setFormatter(formatter)
	    consolehandler.setFormatter(formatter)
	
	    # 5.将logger添加到handler里面
	    logger.addHandler(filehandler)
	    logger.addHandler(consolehandler)
	
	    if client_params:
	        response_status_line_and_header = get_response_status_line_and_header("200", [("Server", "webserver"), ("Content-Type", "text/html;charset=utf8")])
	        path = client_params["path"]
	
	        logger.info("访问路径： %s" % path)
	
	        try:
	            for url, func in URL_FUNC_DICT.items():
	                ret = re.match(url, path)
	                if ret:
	                    return response_status_line_and_header + func(ret)
	            else:
	                response_status_line_and_header = get_response_status_line_and_header("500",[("Server", "webserver"), ("Content-Type","text/html;charset=utf8")])
	                response_body = "没有对应的视图函数"
	                logger.warning("没有对应的视图函数")
	        except Exception as e:
	            response_body = "产生了异常：\r\n%s" % str(e)
	            logger.warning("产生了异常：\r\n%s" % str(e))
	    else:
	        response_status_line_and_header = get_response_status_line_and_header("404", [("Server", "webserver"), ("Content-Type", "text/html;charset=utf8")])
	        response_body = "404 没有找到相关资源"
	
	    return response_status_line_and_header + response_body
	
	
	@route(r"/index\.html")
	def index(ret):
	    """首页index页面"""
	    content = ""
	    with open("./templates/index.html") as f:
	        content = f.read()
	
	    conn = pymysql.connect(host=DATABASE_IP, port=3306, user="root", password="themelove", database="stock_db", charset="utf8")
	    cursor = conn.cursor()
	
	    sql = """select * from info;"""
	    cursor.execute(sql)
	    all_stock_info = cursor.fetchall()
	
	    tr_template = """
	    <tr>
	        <td>%s</td>
	        <td>%s</td>
	        <td>%s</td>
	        <td>%s</td>
	        <td>%s</td>
	        <td>%s</td>
	        <td>%s</td>
	        <td>%s</td>
	        <td>
	            <input type="button" class="%s" value="%s" id="focus" name="focus" systemidvalue="%s">
	        </td>
	    </tr>
	    """
	
	    html_str = ""
	    for line_info in all_stock_info:
	        # 判断该只股票是否被关注
	        sql_stock_is_has_focus = """select * from info as i inner join focus as f on i.id=f.info_id where i.code=%s;"""
	        cursor.execute(sql_stock_is_has_focus, (line_info[1],))
	
	        if cursor.fetchone():
	            focus_str = "取关"
	            class_str = "focus_off"
	        else:
	            focus_str = "关注"
	            class_str = "focus_on"
	
	        html_str += tr_template % (line_info[0], line_info[1], line_info[2], line_info[3], line_info[4], line_info[5], line_info[6], line_info[7], class_str, focus_str, line_info[1])
	    cursor.close()
	    conn.close()
	
	    content = re.sub("{%content%}", html_str, content)
	
	    return content
	
	
	@route(r"/center\.html")
	def center(ret):
	    """个人中心页面"""
	    content = ""
	    with open("./templates/center.html") as f:
	        content = f.read()
	    conn = pymysql.connect(host=DATABASE_IP, port=3306, user="root", password="themelove", database="stock_db",
	                           charset="utf8")
	    cursor = conn.cursor()
	    sql = """select i.code,i.short,i.chg,i.turnover,i.price,i.highs,f.note_info from info as i inner join focus as f on i.id=f.info_id;"""
	    cursor.execute(sql)
	    all_stock_info = cursor.fetchall()
	    cursor.close()
	    conn.close()
	
	    tr_template = """
	    <tr>
	        <td>%s</td>
	        <td>%s</td>
	        <td>%s</td>
	        <td>%s</td>
	        <td>%s</td>
	        <td>%s</td>
	        <td>%s</td>
	        <td>
	            <a type="button" class="btn btn-default btn-xs" href="/show_update_page/%s.html"><span aria-hidden="true"></span> 修改 </a>
	        </td>
	        <td>
	            <input type="button" class="focus_off" value="取关" id="unfocus" name="unfocus" systemidvalue="%s">
	        </td>
	    </tr>
	    """
	
	    html_str = ""
	    for line_info in all_stock_info:
	        html_str += tr_template % (line_info[0], line_info[1], line_info[2], line_info[3], line_info[4], line_info[5], line_info[6],line_info[0], line_info[0])
	
	    content = re.sub("{%content%}", html_str, content)
	
	    return content
	
	
	@route(r"/focus/(\d+)\.html")
	def focus(ret):
	    """添加指定股票到关注列表"""
	    conn = pymysql.connect(host=DATABASE_IP, port=3306, user="root", password="themelove", database="stock_db", charset="utf8")
	    cursor = conn.cursor()
	    stock_code = ret.group(1)
	    print("stock_code=", stock_code)
	    # 1.判断是否存在该只股票
	    sql_is_has_stock="""select * from info where code=%s;"""
	    cursor.execute(sql_is_has_stock, (stock_code,))
	    response_dict = dict()
	    response_dict["status"] = 0
	    response_dict["msg"] = ""
	    response_dict["data"] = ""
	    if not cursor.fetchone():  # 没有该只股票
	        cursor.close()
	        conn.close()
	        response_dict["msg"] = "success"
	        response_dict["data"] = "没有该只股票，创业公司，请大哥手下留情"
	        return json.dumps(response_dict)
	
	    # 2.判断是否已经关注了该只股票
	    sql_is_focus_stock = """select * from info as i inner join focus as f on i.id=f.info_id where i.code=%s;"""
	    cursor.execute(sql_is_focus_stock, (stock_code,))
	    if cursor.fetchone():  # 说明已经关注过该只股票
	        cursor.close()
	        conn.close()
	        response_dict["msg"] = "success"
	        response_dict["data"] = "已经关注过该只股票，请勿重复关注"
	        return json.dumps(response_dict)
	
	    # 3.关注该只股票
	    try:
	        sql_focus_stock = """insert into focus values(default, %s, (select id from info where code=%s));"""
	        cursor.execute(sql_focus_stock, ("不错哦", stock_code))
	        conn.commit()  # 修改操作要提交，pymysql默认开启了事物
	        cursor.close()
	        conn.close()
	        response_dict["status"] = 1
	        response_dict["msg"] = "success"
	        response_dict["data"] = "关注股票(%s)成功！" % (stock_code,)
	        return json.dumps(response_dict)
	    except Exception as e:
	        logging.exception(e)
	        response_dict["status"] = 0
	        response_dict["msg"] = "success"
	        response_dict["data"] = "关注失败，请稍后再试"
	        return json.dumps(response_dict)
	
	
	@route(r"/unfocus/(\d+)\.html")
	def unfocus(ret):
	    """取消关注指定股票"""
	    conn = pymysql.connect(host=DATABASE_IP, port=3306, user="root", password="themelove", database="stock_db",
	                           charset="utf8")
	    cursor = conn.cursor()
	    stock_code = ret.group(1)
	    print("stock_code=", stock_code)
	    # 1.判断是否存在该只股票
	    sql_is_has_stock = """select * from info where code=%s;"""
	    cursor.execute(sql_is_has_stock, (stock_code,))
	    response_dict = dict()
	    response_dict["status"] = 0
	    response_dict["msg"] = ""
	    response_dict["data"] = ""
	    if not cursor.fetchone():  # 没有该只股票
	        cursor.close()
	        conn.close()
	        response_dict["msg"] = "success"
	        response_dict["data"] = "没有该只股票，创业公司，请大哥手下留情"
	        return json.dumps(response_dict)
	
	    # 2.判断是否已经关注了该只股票
	    sql_is_focus_stock = """select * from info as i inner join focus as f on i.id=f.info_id where i.code=%s;"""
	    cursor.execute(sql_is_focus_stock, (stock_code,))
	    if not cursor.fetchone():  # 说明已经关注过该只股票
	        cursor.close()
	        conn.close()
	        response_dict["msg"] = "success"
	        response_dict["data"] = "已经取消关注，请勿重复取消"
	        return json.dumps(response_dict)
	
	    # 3.取消关注该只股票
	    try:
	        sql_unfocus_stock = """delete from focus where info_id=(select id from info where code=%s);"""
	        cursor.execute(sql_unfocus_stock, (stock_code,))
	        conn.commit()  # 修改操作要提交，pymysql默认开启了事物
	        cursor.close()
	        conn.close()
	        response_dict["status"] = 1
	        response_dict["msg"] = "success"
	        response_dict["data"] = "取关股票(%s)成功！" % (stock_code,)
	        return json.dumps(response_dict)
	    except Exception as e:
	        logging.exception(e)
	        response_dict["status"] = 0
	        response_dict["msg"] = "success"
	        response_dict["data"] = "取关失败，请稍后再试"
	        return json.dumps(response_dict)
	
	
	@route(r"/show_update_page/(\d+)\.html")
	def show_update_page(ret):
	    """修改股票备注信息页面"""
	    content = ""
	    with open("./templates/update.html") as f:
	        content = f.read()
	
	    stock_code = ret.group(1)
	    #  1.连接数据库
	    conn = pymysql.connect(host=DATABASE_IP, port=3306, user="root", password="themelove", database="stock_db", charset="utf8")
	    cursor = conn.cursor()
	
	    #  2.查询是否有该只股票
	    sql_is_has_stock = """select * from info where code=%s;"""
	    cursor.execute(sql_is_has_stock, (stock_code,))
	
	    response_dict = dict()
	    response_dict["status"] = 0
	    response_dict["msg"] = ""
	    response_dict["data"] = ""
	    if not cursor.fetchone():
	        cursor.close()
	        conn.close()
	        response_dict["msg"] = "success"
	        response_dict["data"] = "没有该只股票，创业公司，请大哥手下留情"
	        return json.dumps(response_dict)
	
	    #  3.查询是否关注过该只股票
	    sql_is_focus_stock = """select * from info as i inner join focus as f on i.id=f.info_id where i.code=%s;"""
	    cursor.execute(sql_is_focus_stock, (stock_code,))
	    if not cursor.fetchone():  # 说明已经关注过该只股票
	        cursor.close()
	        conn.close()
	        response_dict["msg"] = "success"
	        response_dict["data"] = "尚未关注该只股票，请确认后再试！"
	        return json.dumps(response_dict)
	
	    # 4.查询该只股票的note_info字段
	    sql_note_info = """select note_info from focus where info_id = (select id from info where code = %s);"""
	    cursor.execute(sql_note_info, (stock_code,))
	    cursor.close()
	    conn.close()
	
	    note_info = cursor.fetchone()[0]
	    content = re.sub("{%code%}", stock_code, content)
	    content = re.sub("{%note_info%}", note_info, content)
	    return content
	
	
	@route(r"/update/(\d+)/(.*)\.html")
	def update(ret):
	    """修改股票备注信息借口"""
	    stock_code = ret.group(1)
	    note_info = unquote(ret.group(2))  # 对用户输入进行urldecode解码，将乱码解析成用户可识别的字符串
	
	    # 1.连接数据库
	    conn = pymysql.connect(host=DATABASE_IP, port=3306, user="root", password="themelove", database="stock_db", charset="utf8")
	    cursor = conn.cursor()
	
	    #  2.查询是否有该只股票
	    sql_is_has_stock = """select * from info where code=%s;"""
	    cursor.execute(sql_is_has_stock, (stock_code,))
	
	    response_dict = dict()
	    response_dict["status"] = 0
	    response_dict["msg"] = ""
	    response_dict["data"] = ""
	    if not cursor.fetchone():
	        cursor.close()
	        conn.close()
	        response_dict["msg"] = "success"
	        response_dict["data"] = "没有该只股票，创业公司，请大哥手下留情"
	        return json.dumps(response_dict)
	
	    #  3.查询是否关注过该只股票
	    sql_is_focus_stock = """select * from info as i inner join focus as f on i.id=f.info_id where i.code=%s;"""
	    cursor.execute(sql_is_focus_stock, (stock_code,))
	    if not cursor.fetchone():  # 说明已经关注过该只股票
	        cursor.close()
	        conn.close()
	        response_dict["msg"] = "success"
	        response_dict["data"] = "尚未关注该只股票，请确认后再试！"
	        return json.dumps(response_dict)
	
	    #  4. 修改股票的备注信息
	    sql_update_note_info = """update focus set note_info = %s where info_id = (select id from info where code = %s);"""
	    cursor.execute(sql_update_note_info, (note_info, stock_code))
	    conn.commit()
	    cursor.close()
	    conn.close()
	
	    response_dict["status"] =1
	    response_dict["msg"] = "success"
	    response_dict["data"] = "修改股票(%s)备注成功" % (stock_code,)
	return json.dumps(response_dict)

```