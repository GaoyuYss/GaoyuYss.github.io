Flask基础1

01、虚拟环境：

在文件夹env下创建虚拟环境: virtualenv --no-site-packages -p D:\python3\python.exe flaskenv7
在虚拟环境中安装flask  ： pip install flask

02、最小应用：
默认IP:127.0.0.1
默认端口: 5000
默认启动命令: app.run(host='127.0.0.1', port=8000, debug=True)

03、安装flask-script ,使用manage管理应用
安装:  pip install flask-script
导入： from flask-script import Manager
应用： manage = Manager(app)   manage.run()
启动命令: python xxxx.py runserver -h 0.0.0.0 -p 8080 -d


04、路由匹配规则

格式: <converter:variable_name>， converter转换器， veriable_name变量名
<int: id>: 接收整型id值
<name>: 接收字符串类型的name值
<string:name>: 接收字符串类型的name值
<float:price>: 接收浮点数类型的price值
<uuid:u>: 接收uuid类型的u值
<path:url>: 接收路由中的一部分地址



05、flask-blueprint（蓝图）
	安装: pip install flask-blueprint
	使用:
		1. 生成实例化对象: blue = Blueprint('first', __name__) --'first'为blue的别名，用来反解析路由用
		2. 管理路由blue.route('/hello/')
		3. 注册: app.register_blurprint(blueprint=blue, url_prefix='/app')
			blueprint参数: 指定蓝图对象
			url_prefix参数: 指定URL前缀
	跳转
		无参跳转
			redirect(url_for('生成蓝图对象的第一个参数.跳转的函数名'))
		有参跳转
			redirec(url_for('生成蓝图对象的第一个参数.跳转的函数名', 参数=值))


06、请求与响应
	请求
		接收HTTP请求方式: @app.route('/', methods=['GET', 'POST', 'DELETE', 'PATCH', 'PUT'])
		判断当前请求方式: request.method
		获取GET请求传递的参数: request.args
			获取值: request.args.get(key)
			获取多值: request.args.getlist(key)
		获取POST请求传递的参数: request.form
			获取值: request.form.get(key)
			获取多值: request.form.getlist(key)
		获取路径: request.path
		获取文件: request.files
	响应
		生成响应对象
		设置cookie: 响应对象res.set_cookie(key, vlaue, max_age, expires)
			key: 键值对中的键
			value: 键值对中的值
			max_age: 过期时间，秒为单位
			expires: 过期时间，datetime为单位
		删除cookie
			delete_cookie: 响应对象res.delete_cookie(key)
			set_cookie: 响应对象res.set_cookie(key, value, max_age=0)



07、session会话
	第一种方式: Flask自带，数据是存储在客户端
		加密: app.secret_key='12121212'
		使用: from flask import session
		session['user_id'] = 1

	第二种方式: 使用Flask-Session进行存储，将数据保存在redis中
		配置
			配置redis: app.config['SESSION_TYPE']='redis'
			配置哪个redis: app.config['SESSION_REDIS']=redis.Redis(host,port)
			初始化: from flask_session import Session
			se = Session()
			se.init_app(app)
		使用
			from flask import session 
			session['user_id']=1



08、模板
	父模板base.html
	初始化base_main.html
	继承: {%  extends 'base_main.html' %}
	获取坑之前填充的内容: {{ super() }}
	解析静态文件
		第一种:<link href="/static/css/style.css" rel="stylesheet"> 
		第二种: <link href="{{ url_for('static', filename='css/style.css') }}" rel="stylesheet">
	解析变量: {{ var }}                  
		flask中 for循环中的loop                                 django中是forloop
			生成编号从1开始: loop.index                          forloop.counter
			从0开始: loop.index0                                 forloop.counter0
			倒序1结束: loop.revindex                             forloop.revcounter
			判断当前第一次循环: loop.first                       forloop.first
			判断当前最后一次循环: loop.last                      forloop.last
	解析标签: {%  标签 %}
		标签for
			{% for i in c %}  {% else %}  { % endfor %}
		标签if
			{% if xxx == 1 %}  {% else %} {% endif %}
		没有ifequal标签
	macro宏定义
		定义无参: {% macro say() %}  {%endmacro%}
		定义有参: {% macro say(name) %} {{ name }} {% endmacro %}
		使用: {{ say() }}   或者    {{ say('小明') }}
		从别的html文件中导入（相似于导入函数）: {% from 'functions.html' import say %}
	过滤器
		使用管道符"|", 可以链式调用
		safe: 解析文本中的样式
		striptags: 删除文本中的样式
		trim: 删除文本前后的空格
		length: 计算长度
		upper: 全部大写
		lower: 全部小写


09、模型
	模型定义
		安装: pip install flask-sqlalchemy
		定义模型
		配置
			连接MySQL数据：app.config['SQLALCHEMY_DATABASE_URI'] = 'mysql+pymysql://root:123456@127.0.0.1:3306/flask7'
			app.config['SQLALCHEMY_TRACK_MODIFICATIONS'] = False
			初始化: db.init_app(app)
	迁移
		创建: db.create_all()
		删除: db.drop_all()
