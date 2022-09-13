FLASK_APP = app.py
FLASK_ENV = development
FLASK_DEBUG = 0
In folder D:/envs/flaskProject
D:\envs\flaskday01\Scripts\python.exe -m flask run
 * Serving Flask app 'app.py' (lazy loading)
 * Environment: development
 * Debug mode: off

app = Flask(__name__)

run(host='ip地址',port='端口号')

ip地址,一个端口号对应的是一个程序

http://10.204.32.139:5000/
如果host改成：0.0.0.0    外网可以访问
默认情况下只能是本机

app.run(host='0.0.0.0',port='5001',debug=True)
debug:布尔类型
debug=True 开启了debug调试模式 只要代码改变服务器会重新加载最新的代码 适用于开发环境development
debug=False 默认  代码发生改变不会自动加载    适用于production环境

设置配置文件：
settings
#配置文件
ENV = 'development'
DEBUG = True

# 配置文件settings.py

import settings
# 两种方式如下
app.config.from_object(settings)
app.config.from_pyfile('settings.py')

路由的请求和响应：

浏览器地址栏输入的内容：http://127.0.0.1:8080/index ----->服务器 ----->app -----> 有没有这个路由
----->就会执行路由匹配的函数 -----> return 'hello world' -----> response ----->客户端的浏览器

请求：request
http协议：
request   请求
请求行：   请求地址：http://127.0.0.1:8080/index
         请求方式是什么？ method：get    post
请求头：    key:value
    Accept: text/html,application/xhtml+xml,application/xml;q=0.9,image/webp,image/apng,*/*;q=0.8,application/signed-exchange;v=b3;q=0.9
    Accept-Encoding: gzip, deflate, br
    Accept-Language: zh-CN,zh;q=0.9,en;q=0.8,en-GB;q=0.7,en-US;q=0.6
    Cache-Control: max-age=0
    Connection: keep-alive
    Host: 127.0.0.1:8080
    sec-ch-ua: "Microsoft Edge";v="105", " Not;A Brand";v="99", "Chromium";v="105"
    sec-ch-ua-mobile: ?0
    sec-ch-ua-platform: "Windows"
    Sec-Fetch-Dest: document
    Sec-Fetch-Mode: navigate
    Sec-Fetch-Site: none
    Sec-Fetch-User: ?1
    Upgrade-Insecure-Requests: 1
    User-Agent: Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/105.0.0.0 Safari/537.36 Edg/105.0.1343.27
请求体

response 响应
响应行 状态码
200 ，   请求完成
404 ，   not found 请求无法完成
500 ，   服务器内部错误，说明你写的代码有问题
302 ,   重定向

响应头
    Connection: close
    Content-Length: 43
    Content-Type: text/html; charset=utf-8
    Date: Thu, 08 Sep 2022 07:01:59 GMT
    Server: Werkzeug/2.2.2 Python/3.9.12
响应体
<font color="red"> Hello World!2222 </font>

1.路由

 @setupmethod
    def route(self, rule: str, **options: t.Any) -> t.Callable[[T_route], T_route]:
        def decorator(f: T_route) -> T_route:
            endpoint = options.pop("endpoint", None)
            self.add_url_rule(rule, endpoint, f, **options)
            return f
        return decorator
 这个装饰器其实就是将rule字符串跟视图函数进行了绑定，通过add_url_rule()实现的绑定
 以下两种方式是等效的
@app.route('/index')
def index():
    return 'welcome everyone!'
等效的
def index():
    return 'welcome everyone!'
app.add_url_rule('/index', view_func=index)


2.路由的变量规则

string （缺省值） 接受任何不包含斜杠的文本   *
int     接受正整数   *
float   接受正浮点数
path    类似 string，但可以包含斜杠
uuid    接受 UUID 字符串

@app.route('/getcity/<key>')  # key就是一个变量名，默认是字符串类型的
def getcity(key):   # 参数是必须添加的
    print(type(key))
    return data.get(key)

2.视图
返回值：
response响应对象

request请求对象
参数


视图函数的返回值：
response响应：
1.str               自动转成response对象
2.dict              json
3.response对象       response对象
4.make_response()   response对象
5.redirect()        重定向 302状态码
6.render_template() 模板渲染 + 模板


3.模板 (网页)
模板语法：
1.在模板中获取view中传递的变量值：{{ 变量名key }}

render_template('模板名字',key1 = value1, key2 = value2)

    name = 'xunyicao'  # 字符串类型 str
    age = 12  # 整型 int
    friends = ['小岳岳', '美珍', '黑虎王', '猪八戒']  # 列表 list
    gifts = {'g1': '费列罗', 'g2': 'cake', 'g3': '糖果'}  # 字典 dict
    # 创建对象
    girl = Girl('每每', '湖北武汉') # 自定义的类构建的类型：Girl对象

模板:
    {{ list.0 }}    同   {{ list[0] }}
    {{ dict.key }}  同   {{ dict.get(key) }}
    {{ girl.name }} 同   {{ 对象.属性 }}

2. 控制块：
{% if  条件 %}

{% endif %}

{% if 条件 %}
    条件为True
{% else %}
    条件为False
{% endif %}


{% for 变量 in 可迭代的对象 %}
    for循环要做的任务

{% endfor %}

可以使用loop变量
loop.index  序号从1开始
loop.index0 序号从0开始

loop.revindex   reverse 序号是倒着的
loop.revindex0

loop.first  布尔类型    是否是第一行
loop.last   布尔类型    是否是第二行

3.过滤器
过滤器的本质就是函数
模板函数中过滤器：
{{ 变量名 | 过滤器(*args) }}

{{ 变量名 | 过滤器 }}

常见的过滤器：
1.safe：禁用转译
msg = '<h1>520快乐</h1>'
return render_template('show3.html', girls=girls, users=users, msg=m
不想让其转译：
{{ msg | safe }}
2.capitalize:单词的首字母大写
{{ n1 | capitalize }}
3.lower和upper
大小写的转换
4.title 一句话每个单词的首字母大写
msg = 'She is a beautiful girl'
{{ msg | title }}
5.reverse 翻转
{{ n1 | reverse }}
6.format
{{ '%s is %d years old' | format('lily',10) }}
7.truncate 字符串截断

list的操作：
{# 列表过滤器的使用 #}
{{ girls | first }}
{{ girls | last }}
{{ girls | length }}
{# {{ girls | sum }} 整型的计算 #}
{{ [1,3,5,7,9] | sum }}
{{ [3,1,6,2,5] | sort }}

dict:
    {{ users.0 }} ------->获取大字典

    {% for value in users.0.values() %}  ----->获取值
        {{ value }}
    {% endfor %}

    {% for key in users.0.keys() %}   ------>获取键
        {{ key }}
    {% endfor %}

    {% for k,v in users.0.items() %}  ------>获取键值
        {{ k }}-----{{ v }}
    {% endfor %}

review2:
视图：
request
request.method ----> app.url_map
request.args
request.form
...

response
1.字符串
2.字典
3.tuple
4.response()
5.make_response()
6.render_template()
7.redirect() ----> response 重定向

render_template('模板名字',**context)

render_template('模板名字',name=name,age=age,...)

模板语法：
1.变量
{{ name }}
{{ age }}

字符串
列表
tuple
set
对象 ----> [s1,s2,s3]
    ----> [{},{},{}]
2.过滤器：本质就是函数

{{ name | length }}
{{ users | first }}

3.控制块
{% if 条件 %}

{% endif %}

{% if 条件 %}
    pass
{% else %}
    pass
{% endif %}

{% if 条件 %}
    pass
{% elif 条件 %}
...
{% endif %}

{% for 变量 in 可迭代 %}
    {{ loop.index }}
{% endfor %}

end review2:

2022 - 9 - 12
# 过滤器本质就是函数
1.通过flask模块中的add_template_filter方法
    a.定义函数，带有参数和返回值
    b.添加过滤器 app.add_template_filter(function,name='')
    c.在模板中使用：{{ 变量 | 自定义过滤器 }}
2.使用装饰器完成
    a.定义函数，带有参数和返回值
    b.通过装饰器完成，@app.template_filter('过滤器名字')装饰步骤一的函数
    c.在模板中使用：{{ 变量 | 自定义过滤器 }}

模板：复用
模板继承 *
include
宏

模板继承：
需要模板继承的情况：
1.多个模板具有完全相同的顶部和底部
2.多个模板具有相同的模板内容，但是内容中部分不一样
3.多个模板具有完全相同内容

标签：
{% block 名字 %}

{% endblock %}

1.定义父模板
2.子模板继承父模板
步骤：
父模板：
1.定义一个base.html的模板
2.分析模板中哪些是变化的比如：{% block title %} 父模板的title{% endblock %}
  对变化的部分用block进行“预留位置”也称作：挖坑
3.注意：样式和脚本 需要提前预留
    {% block myclass %} {% endblock %}
    {% block myjs %} {% endblock %}

子使用父模板：
1.{% extends '父模板的名称' %} 将父模板继承过来
2.找到对应的block (坑)填充，每一个block都是有名字的。

include:包含
在A,B,C页面都共同的部分，但是其他页面没有这部分
这个时候考虑使用include
步骤：
1.先定义一个公共的模板部分，xxx.html
2.谁使用则include过来，{% include '文件夹/xxx.html' %}

宏：macro
1.把它看作是jinja2的一个函数，这个函数可以返回一个HTML字符串
2.目的：代码可以复用，避免代码冗余

定义两种方式：
1.在模板中直接定义：
    类似：macro1.html  中定义方式
2.将所有宏提取到一个模板中：macro.html
    谁想使用谁导入：
    {% import 'macro.html' as XXX %}
    {{ xxx.宏名字(参数) }}

总结：
变量：{{ 变量 }}
块：
{% if 条件  %}...{% endif %}
{% for 条件  %}...{% endfor %}
{% block 条件  %}...{% endblock %}
{% macro 条件  %}...{% endmacro %}

{% include '' %} 包含
{% import '' %} 导入宏
{% extends '' %}

{{ url_for('static',filename='') }}
{{ hongname(XXX) }}

view:
@app.route('/',endpoint='',methods=['GET','POST'])
def index():
    直接使用request
    return response|''|render_template('xxx.html')

template:
    模板的语法
