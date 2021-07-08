#### 创建项目
django-admin startproject + 项目名称
#### 启动服务（测试用，前台启动）
python3 manage.py runserver (+端口号/default:8000)
python3 manage.py runserver 0.0.0.0:8000
#### 关闭服务
在终端crtl+c
sudo lsof -i:8000 ->kill -9ubuntu
#### 启动应用
python manage.py startapp
#### 列出所有的命令
python3 manage.py
#### 生成数据库迁移文件
python manage.py makemigrations
#### 执行数据库迁移
python manage.py migrate
#### 进入 Django Shell
python manage.py shell
#### 创建admin
python manage.py createsuperuser
#### 清理已过期Sessions
python manage.py clearsessions
#### 创建内存表
python manage.py createcachetable

manage.py 包含项目管理的子命令
项目同名文件夹
__init__:python包的初始化文件
wsgi.py:WEB网关的配置文件，正式启动django时才需要用到
urls.py:项目主路由配置-HTTP请求进入Django，有限调用
settings.py:项目的配置文件-包含项目启动需要的配置。


### settings.py
+ 公有配置和自定义配置
+ 配置项格式例： BASE_DIR = 'xxxx'
+ 公有配置：Django官方提供的基础配置
+ 自定义配置满足命名规则，并尽量个性化 

项目的绝对路径
> BASE_DIR = Path(__file__).resolve().parent.parent

启动模式：
True调试模式：
+ 检测代码改动后，立即重启服务
+ 报错后提供一个

False正式启动模式、上线模式
> DEBUG = True

请求Host头,只处理请求头在在列表中的请求，过滤一些请求，debug = 1在调试模式下默认接收127.0.0.1和localhost两个值。
局域网内部访问时需要把局域网IP加上，一旦不为空就必须都配置上
> ALLOWED_HOSTS = []

主路由文件位置：
> ROOT_URLCONF = 'hworld.urls'

语言配置：
> LANGUAGE_CODE = 'en-us'（zh-Hans）

时区：
> TIME_ZONE = 'UTC'（Asia/Shanghai）

### URL
统一资源定位符（Uniform Resource Locator）
> protocal(协议)：//hostname(主机)[:port(端口)]/path(路由)[?query(查询字符串)][#fragment(锚点)]
+ 协议http；https（加密，安全的https）；file，本地磁盘协议（file：///）
+ hostname:依靠DNS解析为IP
+ 端口：默认http为80端口
+ 路由地址
+ 查询字符串：？menuld=634898&version=AID9089
主要用于给动态网页传递参数，样式：？参数=值&参数=值
+ 信息片段：#subject锚点，直接定位到网页指定位置

Django如何处理URL?
1.从配置文件中根据ROOT_URLCONF找到主路由文件，默认urls.py
2.加载urlpatterns变量[包含很多数组的路由]
3.<font color = 'red'>依次</font>匹配urlspatterns的path，匹配到第一个合适的中断后续匹配
4.匹配成功-返回响应
5.匹配失败-返回404

## 视图函数
用于接收浏览器请求并通过HttpResponse对象返回响应的函数。此函数可以接收浏览器请求并根据业务逻辑返回相应的响应内容给浏览器。
语法：
```python
def xxx_view(request[,其他参数…])：
    return HttpResponse对象
```
书写位置：项目同名文件夹下/views.py
```python
from django.http import HttpResponse
def page1_view(request):
    html = '<h1>这是一个页面</h1>'
    return HttpResponse(html)
```

### 路由配置
> path函数
from django.urls import path
path(route,views,name = None)

+ route:字符串类型，匹配的请求路径
+ views：指定路径所处理的视图处理函数的名称
+ name：地址别名

**path转化器**
> path转换器
<转换器类型:自定义名>
作用：若转换器类型匹配到对应类型的数据，则将数据按照关键字传参的方式传递给视图函数

> path('page/<int:page>',views.xxx)

转换器：
+ str：匹配除了'/'之外的非空字符串
+ int：int匹配0或任何正整数
+ slug：匹配任何由ASCII字母或数字以及连字符和下划线组成的短标签
+ path：匹配非空字段，包括路径分隔符‘/’

re_path函数：
正则匹配，更加精密的匹配规则
re_path(reg,view,name = xx)
正则表达式为命名分组模式(?P<name>pattern);

### HTTP请求
1.请求：浏览器通过HTTP协议发送给服务器端的数据
2.响应：服务器端接收到请求后做相应的处理后再回复给浏览器端的数据。

```
起始行：方法、路由、协议

headers：请求头 K:V

请求体(body)：可能为空
```
+ 请求方法：
    + GET：返回实体主体
    + HEAD:调试获取报头
    + POST：向指定资源提交数据进行处理
    + PUT：更新
    + DELETE：删除
    + CONNECT:代理服务器
    + OPTIONS
    + TRACE：回显，主要用于测试和诊断


Django中的请求：
实际就是视图函数的第一个参数，及HttpRequest对象
个人理解就是Django预先将请求转变为了对象，将请求内容转变为对象属性。说到底是对报文进行了预处理。
+ path_info:URL字符串
+ method：表示HTTP请求方法
+ GET：拿查询字符串（？后的值），QueryDict查询字典的对象
+ POST：拿POST表单里的数据，即用户提交的数据，QueryDict查询字典的对象
+ FILES：类似于字典的对象，拿文件
+ COOKIES
+ session
+ body
+ scheme
+ get_full_path()
+ META:请求头

## 请求/响应
```
起始行（协议版本 状态码）
响应头(K:V)
响应体body
```
响应状态码：
+ 200 请求成功
+ 500 内部服务器错误
+ 301 永久重定向
+ 302 临时重定向
+ 404 请求的资源不存在

分类：
+ 1**，收到请求，需要继续执行操作
+ 2**，成功，操作被成功接收并处理
+ 3**，重定向
+ 4**，客户端错误
+ 5**，服务器错误

Django的响应对象
> HttpResponse(content = 响应体，content_type = 响应体数据类型default：html，status = 状态码，default：200)
作用：向客户端浏览器返回响应，同时携带响应体内容。

常用Content_Type
+ 'test/html'
……

### GET请求和POST请求
统一由视图函数接收，因此一定是需要隔离业务逻辑
```python
if request.method == 'GET':
    处理GET业务逻辑
elif request.method =='POST':
    处理POST的业务逻辑
else:
    其他业务逻辑
````
**GET处理：**
一般用于向服务器获取数据。
能够产生GET请求的场景：
+ 浏览器地址栏输入URL并回车
+ <a href = "地址？参数=值&参数=值"\>
+ form表单中的method为get

GET请求方法中，如果有数据需要传递给服务器，通常会使用查询字符串传递。【注意不要传递敏感数据】
URL:http://127.0.0.1:8000/page1\?a=100&b=200
服务器端接收参数
获取客户端请求GET请求提交的数据：

```python
request.GET['参数名']
request.GET.get('参数名','默认值')
request.GET.getlist('参数名')
```
如果有传递多个值，则参数对应的应该是一个列表，需要使用getlist方法取出所有值，get方法只能取出最后一个值。
**应用场景：问卷调查的复选框**

**POST处理**
一般用于向服务器提交大量/隐私数据
通过表单
```python
<form method = 'post' action = '/login'>
    姓名：<input type = 'text' name = 'username'>
    <input type = 'submit' value = '登录'>
</form>
```
#### CSRF验证问题
本身是django的防御措施，防御CSRF攻击问题，会阻止POST，暂时关闭：
settings.py》注释django.middleware.csrf.CsrfViewMiddleware

### Django的设计模式
传统的MVC Mode-View-Controller（模型-视图-控制器）模式。
特点：低耦合
+ M模型层：主要用于对数据库层的封装
+ V视图层：用于给用户展示结果（WHAT + HOW显示什么，怎么显示）
+ C控制层：用于处理请求、获取数据、返回结果

Django：MTV模式
把MVC的V拆成了两层，并且弱化C层为路由文件。
+ M模型层：与MVC相同
+ T模板层：Template，专门负责html相关事情（HOW）
+ V视图层：核心，负责接收请求，获得结果，返回结果。
在该模式下依然存在控制层C，即主路由

## Django模板层
### 模板层创建
模板：根据字典数据动态变化的html网页，根据视图中传递的字典数据动态生成相应的html页面
模板配置：
+ 创建模板文件夹 <项目名>/templates
+ 在settings.py中TEMPLATE配置项
    + BACKEND：指定模板引擎
    + DIRS：模板的搜索目录（可以是一个或多个）
    + APP_DIRS：是否在应用中的templates文件夹中搜索末班文件
    + OPTIONS：有关模板的选项
+ 主要需要修改的：DIRS：
'DIRS':[os.path.join(BASE_DIR,'templates')]

**模板加载方案1：**
```python
from django.template import loader
1.通过loader加载模板
t = loader.get_template("模板文件名")
2.将t转化为HTML字符串
html = t.render(字典数据)
3.用响应对象将转换的字符串内容返回给浏览器
return HttpResponse(html)
```
**模板加载方案2：**
使用render直接加载并且响应模板。
在视图函数中
```python
from django.shortcuts import render
return render(request,'模板文件名',字典数据)
```

视图层与模板层之间的交互
+ 视图函数中可以将Python变量封装到字典中传递到模板中。
```python
def xxx_view(request):
    dic = {
        k1: v1,
        k2: v2
    }
    return render(request, 'xxx.html', dic)
```
+ 模板中使用{{变量名}}的语法来调用视图传进来的变量。


### 模板的变量
能传递到模板中的变量类型：str,int,list,tuple,dict,func,obj

在模板中使用变量的语法：
+ {{变量名}}
+ {{变量名.index}}
+ {{变量名.key}}
+ {{对象.方法}}
+ {{函数名}}

### 模板层的标签
作用：将一些服务器端的功能嵌入到模板中，例如流程控制等

语法
```
{% 标签 %}
…
{% 结束标签 %}
```
例：
if标签
```
{% if 条件表达式 1 %}
…
{% elif 条件表达式 2 %}
…
{% elif 条件表达式 3 %}
…
{% else %}
…
{% endif %}#！！一定记住要封口
```
NOTICE!在模板中使用实际括号是无效的语法，如果需要指示优先级，则应该选择嵌套if

for标签
语法
```
{% for 变量 in 可迭代对象 %}
    …循环语句
{% empty %}
    …可迭代对象无数据时填充语句
{% endfor %}
```
内置变量forloop
forloop.counter:循环的当前迭代（从1开始索引）
forloop.counter0:循环的当前迭代（从0开始索引）
forloop.revcounter:counter倒序
forloop.revcounter0:counter0倒序
forloop.first:第一次循环为真‘
forloop.last：最后一次循环为真
forloop.parentloop：外层循环

### 模板层过滤器
过滤器：在变量输出时对变量的值进行处理
可以通过使用过滤器来改变变量的输出显示

语法：{{变量|过滤器1：'参数1'|过滤器2：'参数值2'…}}

常用过滤器：
+ lower：转换为小写
+ upper：转换为大写
+ safe：魔人布对变量内的字符串进行html转义
+ add：将value的值增加n

### 模板的继承
模板继承使父模板内容重用，子模板直接继承父模板的全部内容并可以覆盖父模板中相应的块。

语法——父模板中：
+ 定义父模板中的块block标签
+ 识别出哪些在子模板是允许被修改的
+ block标签：在父模板中定义，在子模板中覆盖

语法——子模板中：
+ 继承模板extends标签（写在模板第一行）
例如{%extend 'base.html'}
+ 子模板 复写父模板中的内容块
{block block_name}
{% endblock blockname %}

重写的覆盖规则
+ 不重写，按照父模板的效果显示
+ 重写，则按照重写效果显示
注意：
+ 模板继承时，服务器的动态内容无法继承

### url反向解析
代码中url的位置：
1.模板
+ 超链接<a href = >
+ form表单 form action 将表单中的数据用POST的方法提交到url  

2.视图函数中 - 302跳转 HttpResponseRedirect('url')
将用户地址栏中的地址跳转到url

代码中的url书写规范
+ 1.绝对地址：http://127.0.0.1:8000/page/
+ 2.相对地址：
    + a '/page/1/'浏览器会把当前地址栏的协议、IP和端口加上这个地址
    + b 'page/1/'没有/开头的，浏览器会根据当前url最后一个/之前的内容加上该相对地址作为最终访问地址

url反向解析
指在视图或模板中，用path定义的别名来动态查找或计算出相应的路由。
path：
+ path(route,views,name='别名')

模板中：
{% url '别名'%}
{% url '别名' '参数值1' '参数值2'%}
视图函数中
调用reverse方法进行方向解析
```python
from django.urls import reverse
reverse('别名', args=[], kwargs={})
```
ex:
print(reverse('pagen',args=[300]))
print(reverse('person',kwargs={'name':'xixi','age':18}))

### 静态文件
什么是静态文件：图片、css、js、音频、视频
静态文件属于静态请求，不经过视图函数
静态文件配置-settings.py
+ 1.静态文件的访问路径，默认'/static/'
+ 2.STATICFILES_DIRS，静态文件在服务器端的存储位置
元组
```
STATICFILES_DIRS = （
        os.path.join(BASE_DIR, "static"),
）
```

+ 3.方案2通过{% static %}标签访问静态文件
    + 1.加载static-{% load static %}
    + 2.使用静态资源-{% static'静态资源路径' %}
    + 3.样例：
    ```
    <img src="{% static 'images/lena.jpg' %}">
    ```

## 应用
### 应用创建
每一个应用都是一个MTV
创建应用
+ 用manage.py执行startapp
> python manage.py startapp music
+ 在settings.py的INSTALLED_APPS列表中配置安装此应用

执行创建应用后，应用文件夹下
+ migrations文件夹：与DB有关
+ __init__.py
+ admin.py
+ apps.py
+ models.py:与DB相关
+ tests.py
+ views.py：视图函数

### 分布式路由
Django中，主路由配置文件可以不处理用户具体路由，主路由配置文件的可以做请求的分发（分布式请求处理）。具体的请求可以由各自的应用来进行处理。
主路由匹配前缀，如/news/，再往下分发到子路由配置。
配置分布式路由：
+ 主路由中调用include函数
```
http://127.0.0.1:8000/music/index -> path('music/',include('music.urls'))
```
+ 在子路由下创建urls.py，其内部结构与主路由完全一致。
path('index/', views.index_view)

### 应用下的模板
应用内部可以配置模板目录
+ 1.应用下手动创建templates文件夹
+ 2.settings.py中开启应用模板功能
> TEMPLATE配置项中的'APP_DIRS'值为True即可、

**important：查找模板的顺序：外层templates文件夹->注册顺序的app内部的templates文件夹**
解决方法：在应用层的templates下创建嵌套同名子目录，从而在views.py render中使得html获得不一样的路径

## 模型层及ORM介绍
模型层：负责与数据库之间进行通信
Django配置mysql

+ 创建数据库mysql
+ 更改settings.py：
```python
DATABASES = {
    'default': {
        'ENGINE': 'django.db.backends.mysql',
        'NAME': 'mysite3',
        'USER': 'root',
        'PASSWORD': '123456',
        'HOST': '127.0.0.1',
        'POST': '3306'
    }
```

什么是模型：
+ 模型是一个Python的类，它是由django.db.models.Model派生出的子类。
+ 一个模型类代表数据库的一张数据表
+ 模型类中每一个类属性都代表数据库中的一个字段
+ 模型是数据交互的接口，是表示和操作数据库的方法和方式。

### ORM框架
ORM（对象关系映射），是一种程序技术，能够使用类和对象对数据库进行操作，从而避免通过SQL语句操作数据库。

作用
+ 建立模型类和表之间的对应关系，允许我们通过面向对象的方式来操作数据库。
+ 根据设计的模型类生成数据库中的表格。
+ 通过简单的配置就能更换数据库引擎。

优点：
+ 只需要面向对象编程，简化了向数据库编写代码的流程。
+ 实现了数据模型与数据库的解耦，屏蔽了不同数据库操作上的差异。

缺点：
+ 对于复杂业务，使用成本较高
+ 根据对象的操作转换为SQL语句，根据查询结果转化为对象，在映射过程中有性能损失。

映射图：
ORM---------->DB
类---------->数据表
对象-------->数据行
属性-------->字段

数据库迁移：
迁移是Django同步您对模型所做出的更改（添加字段，删除模型等）到您的数据库模式的方式。
+ 1.生成迁移文件：
> python manage.py makemigrations
生成一个中间文件，并保存在migrations文件夹中

+ 2.执行迁移脚本程序
> python manage.py migrate
执行迁移程序实现迁移，将每个应用下的migrations目录中的中间文件同步回数据库。

### 模型类
模型类-创建
```python
from django.db import models
class 模型类名(models.Model):
    字段名 = models.字段类型（字段选项）
```
表名实际为 APP名称+类名称小写

### 模型类-字段类型：
+ BooleanField
数据库类型：tinyint(1)
编程语言中将使用True或False来表示值
在数据库中则使用0或1来表示具体的值

+ Char
数据库类型：VarChar（Django不支持Char）
注意：必须要指定max_length参数值

+ DataField()
数据库类型：date
作用：表示日期
参数：**三选一**
    + auto_now:每次保存对象时，自动设置该字段为当前时间（取值：True/False）
    + auto_now_add:当兑现第一次被创建时自动设置当前时间（取值：True/False）
    + default:设置当前时间

+ DataTimeField()
数据库类型：datatime(6)通常用这个，比上面那个精确一些
作用：表示日期和时间
参数：同DataField

+ FloatField()
数据库类型：double
编程语言中和数据库中都使用小数表示值

+ DecimalField()
数据库类型：decimal(x,y)
编程语言中:使用小数表示该列的值
在数据库中：使用小数
参数（必须有）：max_digits;decimal_places

+ EmailField()
数据库类型：varchar
编程语言和数据库中均使用字符串，但Django含有正则检查。

+ IntegerField()
数据库类型：Int
编程语言和数据库中使用整数

+ ImageField()
数据库类型：varchar(100)
作用：在数据库中为了保存图片路径
编程语言和数据库中使用字符串

+ TextField()
数据库类型：longtext
作用：表示不定长的字符数据

### 模型类-字段选项：
创建列的额外信息
+ primary_key：设置为True，则为主键，此数据库表不会创建id字段
+ blank:设置为True，则字段可以为空，控制的是Admin后台的提交，和mysql的null不同
+ null：设置为True，则该列允许为空
默认为False,需要一个default选项来设置默认值
+ default：设置该列的默认值
+ db_index：设置为True，表示为该列增加索引
+ unique: 唯一索引
+ db_column：指定列的名称，如果不指定的话则采用属性名作为列名。不给的话字段名就是列名。
+ verbose_name：设置此字段在admin界面上的显示名称，可以中文化admin界面

好习惯：字段选项【添加或更改】均要执行

### 模型类-Meta内部类
使用Meta类来给**模型(其实就是表的属性)**赋予属性，Meta类下有很多内建的类属性，可对模型类做一些控制。
如改表名：
```python
from django.db import models
class 模型类名(models.Model):
    字段名 = models.字段类型（字段选项）
    class Meta:
        db_table = 'book'
        #控制模型类在admin后台显示的名称
        verbose_name = '单数名'
        #指定复数形态
        verbose_name_plural = '复数名'

```

Django对于数据库操作是惰性的，尽量不对数据库进行积极的修改，如设置的default值将不参与表的字段生成，只会参与实际插值。

### ORM-创建数据
增删改查
ORM CRUD核心：模型类.管理器对象

管理器对象
每一个models.Model的模型类，都会有一个objects对象被同样继承下来，这个对象叫做管理器对象。
数据库的增删改查可以通过管理器实现。

创建数据
方案1：
+ MyModel.objects.create(属性1=值1，属性2=值2,…)

成功：返回创建好的实体对象
失败：抛出异常

方案2：
+ 创建MyModel实例对象，并调用save()并保存
```python
obj = Mymodel(属性 = 值，属性 = 值)
obj.属性 = 值
obj.save()
```

需要使用Django Shell
> python manage.py shell
**代码每次变化，都需要重启Django Shell**


## ORM
### 查询操作
通过管理器对象进行
通过MyModel.objects管理器方法调用查询方法
方法
+ all()
用法：MyModel.objects.all()
等价于select * from table
返回值：QuerySet容器对象，类数组对象，内部存放了MyModel实例 
如
```
<QuerySet [<Book: Python,20.00>, <Book: jango,50.00>, <Book: JQuery,40.00>, <Book: Linux,30.00>, <Book: HTML5,26.00>]>
```
**可以在模型类中定义__str__方法，自定义QuerySet中的输出格式，则输出时能输出格式化字符串。**

Django 同样支持方法的串联，以下方法在返回QuerySet后均可使用方法串联，且最终都会按照SQL顺序对数据库进行操作。

**print(QuerySet.query)将取得实际的SQL语句。**
+ values('字段1','字段2')
等价于select 列1，列2 from xxx
返回：QuerySet，但内部存字典，每一个字典代表一个数据
如：
```
<QuerySet [{'title': 'HTML5'}, {'title': 'jango'}, {'title': 'JQuery'}, {'title': 'Linux'}, {'title': 'Python'}]>
```
+ values_list('字段1','字段2')
基本同上，但返回元组，使用时需要使用索引
```
<QuerySet [('HTML5',), ('jango',), ('JQuery',), ('Linux',), ('Python',)]>
```

+ order_by('-列'(降序),'列')
等价于 ORDER BY
返回：QuerySet
如：
```
<QuerySet [<Book: jango,50.00>, <Book: JQuery,40.00>, <Book: Linux,30.00>, <Book: HTML5,26.00>, <Book: Python,20.00>]>
```

+ filter(条件)
语法：MyModel.objects.filter(属性1=值1，属性2=值2)（,默认是AND查询）
等价于 条件查询
返回值：QuerySet，存放模型实例

+ exclude(条件)
语法同上
等价于 WHERE NOT
作用：返回不包含此条件的全部数据集
电商取非可以用该语句

+ get(条件)
**容易报错，一定要Try一下**
语法：MyModel.objects.get（条件）
作用：返回满足条件的唯一一条数据
说明：**该方法只能返回一条数据**，直接获得Object
查询结果多于一条则抛出异常；查询结果没有数据则抛出Model.DoesNotExist异常。


### 查询谓词
类属性 + '__' + 谓词
定义：做更灵活的条件查询时需要使用查询谓词
说明：每一个查询谓词都是一个独立的查询功能
+  __exact:等值匹配
> Author.objects.filter(id_exact=1) <=> SELECT * FROM Author WHERE ID=1
+ __contains:包含指定值
>Author.objects.filter(name__contains='w')
SELECT * FROM Author WHERE name Like '%w%'
+ __startwith:以xxx开始
+ __endwith:以xxx结束
+ __gt:大于指定值
+ __gte:大于等于
+ __lt:小于
+ __lte:小于等于
+ __in:指定范围内
>Author.objects.filter(name__in=['中国'，'美国'])
+ __range:查询数据是否在指定区间范围内
>Author.objects.filter(age__range(35,50))
<==> SELECT * FROM Author WHERE age BETWEEN 35 AND 50

### 更新操作
1.针对单个数据的修改
查(get())->改：通过对象.属性的方式更改->保存：对象.save()
2.批量更新数据
直接调用QuerySet的update(属性=值实现批量修改)
针对QuerySet来做更改

### 删除操作
1.单个数据删除
+ 查找对应的数据对象
+ 调用该数据对象的delete()方法实现删除

2.批量数据删除
+ 查找QuerySet
+ 调用delete()方法实现删除

3.伪删除操作
通过在表里添加一个布尔型字段（is_active），默认是True；执行删除时，将欲删除数据的is_active置为False。
**注意**：使用伪删除时，确保显示数据的地方，均添加了is_active=True的过滤查询。


### F对象
F对象(很适合用于点赞)
·········································
**F对象实际等价于语句：
UPDATE TABLE SET COLUMN=TABLE.VALUE+10
使用单句的数据库查询语句，Mysql的InnoDB引擎使用行锁，因此F对象的本质是使用了数据库中的锁。 **
·········································
```python
from django.db.models import F
```
+ 一个F对象代表数据库中某条记录的字段的信息(不直接取出来)
+ 作用：通常对数据库字段值在不获取的情况下进行操作，用于类属性之间的比较
F('列名')
**对数据库字段值在不获取的情况下进行操作**：
例：需求：将Book表中所有的market_price全部自增10。

该需求原本只能通过循环取出每一个数据后+10再写入实现。

Query的update方法必须结合F对象方法实现
使用F语句
```python
Book.objects.all().update(market_price=F('market_price')+10)
```
**用于类属性之间的比较**：
```python
Book.objects.filter(market_price_gt=F('price')+10)
```

### Q对象
用于进行逻辑或、逻辑非操作时使用
```python
Book.objects.filter(Q(market_price_lt=35)|Q(price_gt=40))
```
Q对象能够实现互相间的&与,|或,~非,&~与非等操作。

### 聚合查询
聚合查询是指对一个表中的一个字段的数据进行部分或全部进行统计查询。
分为整表聚合和分组聚合。
+ 整表聚合
聚合函数需要导入
```python
from django.db.models import *
```
语法：
```python
MyModel.objects.aggregate(结果变量名（别名）=聚合函数('列'))
```
返回：字典
+ 分组聚合
其实是为了实现Having语句
通过计算查询结果中每一个对象所关联的对象集合，从而得出总计值，为查询集的每一项生成聚合
```python
QuerySet.annotate(结果变量名（别名）=聚合函数('列'))
```
返回：QuerySet
### 原生数据库操作：
```python
1.只用来查询：MyoModel.objects.raw(sql语句，拼接参数)
```
返回值：RawQuerySet集合对象，不支持方法串联，只支持基本的循环等。

SQL注入问题：
使用原生语句，使用拼接参数的方式进行查询能适当避免SQL注入问题。

2.完全跨过模型类操作数据库
+ 导入
```python
from django.db import connection
```
+ 用创建cursor类的构造函数创建cursor(游标)对象，为保证在出现异常时能够释放cursor，通常用with来创建操作
```python
with connection.cursor() as cur:
    cur.execute('执行SQL语句','拼接参数')
```

## admin后台管理
admin后台用于开发过程中调用和调试，django会搜集所有已注册的模型类，并为这些模型类提供数据管理界面。
### 后台的创建：
```python
python manage.py createsuperuser
```
### 注册自定义模型类
+ 在应用的admin.py导入注册要管理的models类
+ 调用admin.site.register方法进行注册

显示样式是按照models.py中__str__方法显示的。

### 模型管理器类
作用：为后台管理界面添加便于操作的新功能
继承于django.contrib.admin里的ModelAdmin类
+ 在应用的admin.py定义模型管理器类
```python
class XXXXManager(admin.ModelAdmin):
    ……
```
+ 绑定注册模型管理器和模型类,使用调用admin.site.register方法的第二个参数

类属性：
```python
class XXXXManager(admin.ModelAdmin):
    #表头
    list_display = ['id','title','price']
    #控制list_display哪些字段超链接进修改页
    list_display_links = ['title']
    #添加过滤器
    list_filter = ['id']
    #添加搜索框（模糊查询）
    search_fields = ['title']
    #添加可在列表页可编辑的字段，与
    #list_display_links字段是互斥的
    list_editable = ['price']
```

## 关系映射
关系映射：一对一，一对多，多对多。
### 一对一映射
创建一对一外键：
语法：OneToOneField(类名, on_delete=xxx(级联删除：在存在键的前提下的删除规则))
on_delete:
+  models.CASCADE：级联删除，只是模拟SQL约束ON DELETE CASCADE，不影响Mysql设置。
+ models.PROTECT:保护删除，等同于mysql默认的RESTRICT
+ SET_NULL:保留关联数据，设置为NULL
+ SET_DEFAULT:将外键设置为默认值。

创建一对一数据
无外键的模型类，和之前相同
有外键的模型类：
> wife = Wife.objects.create(name='王夫人',author = author1(类属性名称绑实例))
> wife = Wife.objects.create(name='王夫人',author_id = 1(类属性字段绑值))

一对一查询
+ 正向查询：从外键查对象
+ 反向查询：从对象查外键
调用反向属性查询到关联的一方

### 一对多查询
核心：正向属性(authors)和反向属性(book_set)
在多表上设置外键，关联一表。

创建一对多数据：
语法：Foreignkey("一"的模型类, on_delete=xxx(级联删除：在存在键的前提下的删除规则))

添加数据：
先添加“一”，再添加“多”。
无外键的模型类，和之前相同
有外键的模型类：
类似上面
> wife = Wife.objects.create(name='王夫人',author = author1(类属性名称绑实例))
> wife = Wife.objects.create(name='王夫人',author_id = 1(类属性字段绑值))

查询数据：
正向查询（有显性属性的）：由book查出版社：book.publisher
反向查询(使用反向属性)：
> books = pub1.book_set.all()
或books = Book.objects.filter(publisher=pub1)



### 多对多映射
核心：正向属性(authors)和反向属性(book_set)
mysql中多对多需要用三张表实现
Django中无需手动创建第三张表，Django自动完成

创建字段语法：属性 = models.ManyToManyField(MyModel)

创建数据：
+ 1.先创建Author，再关联book
> author1 = Author.objects.create(name='1')
author2 = Author.objects.create(name='2')
book1 = author1.book_set.create(title = '1')#创建
author2.book_set.add(book1)#绑定
+ 2.先创建book,再关联author
> book = Book.objects.create(title='p1')
author3 = book.authors.create(name="3")
book.authors.add(author1)

## Cookies和Session
### 会话
从打开浏览器访问一个网站，到关闭浏览器结束此次访问，称之为一次会话。
HTTP本身是无状态的，导致会话状态难以保持。

### Cookies
保存在客户端浏览器上的存储空间
特点：
+ cookies在浏览器是以键值对的形式进行存储的，键和值都是以ASCII码的形式存储的
+ 存储的数据带有生命周期
+ cookies的数据是按照域隔离的，不同的域之间无法访问
+ **cookies的内部数据会在每次访问此网站时都会携带到服务器，如果cookies过大会影响访问速度。**

存储
HttpResponse.set_cookie(key,value='',max_age=None,expires=None)
-key:cookie的名字
-value：cookie的值
-max_age：存活相对时间，秒
-expires：具体过期时间
当不指定max_age和expires时，关闭浏览器时此数据失效。

删除&获取
获取：request.COOKIES
删除：request.delete_cookie(key)

### session
会话保持-登录流程
用户登录->账号密码传至后端，服务器数据库验证，正确则发放cookie->后续浏览器将自动把当前域下的cookie都发送至服务器。但浏览器存储不是十分安全，因此引入了session。

session技术实际将数据存在了服务器里，对于不同的浏览器有不同的存储空间，生成空间后，会将一个sessionID返还给浏览器，浏览器会将sessionID存储在Cookies，之后每次返还给服务器。

session是在服务器上开辟一段空间用于保留浏览器和服务器交互时的重要数据。

session初始配置：
+ 1.INSTALLED_APPS:django.contrib.sessions
+ 2.MIDDLEWARE:
'django.contrib.sessions.middleware.SessionMiddleware'

session的使用：
session对象是一个类似于字典的SessionStore类型的对象。
+ 保存session的值到服务器：
request.session['KEY'] = VALUE
+ 获取session的值
value = request.session['KEY']
value = request.session.get('KEY',默认值)
+ 删除session
del request.session['KEY']


干预session时间：settings.py里的SESSION_COOKIE_AGE指定cookies中的保存时间，默认两周
SESSION_EXPIRE_AT_BROWSER_CLOSE = True,关闭浏览器自动清除session，默认False
SESSION的数据在Django中保存在数据库中，因此需要保证已经执行过了migrate

Django session的问题：
+ 1.django session的表是单表设计，且该表数据量不会自动清理，哪怕是已经过期。
+ 2.可以每晚执行python manage.py clearsessions，会自动删除已经过期的session数据。

## 缓存
**定义**：缓存是一类可以更快的读取数据的介质统称，也指其他可以加快数据读取的存储方式。一般用来存储临时数据，常用介质的是读取速度很快的内存。
**意义**：视图渲染有一定成本，数据库的频繁查询过高；所以对于低频变动的页面可以考虑使用缓存技术，减少实际渲染次数；用户拿到响应的时间成本会更低。
**场景**：1.博客列表页；2.电商商品详情页
场景特点：数据变动频率较少

### Django中设置缓存：(settings.py)
+ 数据库缓存：将缓存存储在数据库中，尽管存储介质还是数据库，但把一次复杂查询的结果直接存储在表里，可避免重复进行复杂查询，提升效率。

配置方法：
```python
CACHES={
    'default': {
        'BACKEND':'django.core.cache.backends.db.DatabaseCache',#引擎
        'LOCATION':'my_cache_table',#指定用于缓存的表
        'TIMEOUT':300,#缓存时间
        'OPTIONS':{
            'MAX_ENTRIES':300,#最大存储条数
            'CULL_FREQUENCY':2,#缓存条数达到最大值时，删除1/x的数据
        }
    }
}
```

+ 缓存到服务器内存中
配置方法：
```python
CACHES={
    default: {
        'BACKEND':'django.core.cache.backends.locmem.LocMemCache',#引擎
        'LOCATION':'unique-snowflake',#雪花算法内存寻址
    }
}
```

+ 将缓存数据存储到本地文件中
配置方法：
```python
CACHES={
    default: {
        'BACKEND':'django.core.cache.backends.filebased.FileBasedCache',#引擎
        'LOCATION':'/var/tmp/django_cache',#存储路径
        # win'c:\test\cache'
    }
}
```

### 整体缓存策略
+ 视图函数中
django还是使用了装饰器来实现缓存逻辑
```python
from django.views.decorators.cache import cache_page
@cache_page(30)# 单位秒
def my_view(request):
```
+ 路由中
一个道理，在进入视图函数前首先使用装饰器。换个地方写而已。
```python 
from django.views.decorators.cache import cache_page
urlpatterns = [
    path('foo/',cache_page(60)(my_view)),
]
```
使用简单粗暴，但是无法控制。
### 局部缓存策略
相较于整体缓存更加灵活，复用性更好。
缓存api的使用：
+ 方式1：使用caches['CACHE配置key']导入具体对象
```python 
from django.core.caches import caches
cache1 = caches['myalias']
cache2 = caches['myalias_2']
```
+ 方式2
```python 
直接调用CACHE中的default值。相当于1中的
cache = caches['default']
from django.core.caches import cache
```

缓存api：
+ 1.cache.set(key,value,timeout)-存储缓存
key:缓存的key，字符串类型
value:python对象
timeout：缓存存储时间，默认为CACHES中的TIMEOUT值
返回值：None
+ 2.cacahe.get(key)-获取缓存
key：缓存的key
返回值，key的对应值，没有则返回None
+ 3.cache.add(key,value)-存储缓存，只在key不存在的时候生效
返回值：True或False
+ 4.cache.get_or_set(key,value,timeout)
+ 5.cache.set_many(dict,timeout)
+ 6.cache.get_many(key_list)
+ 7.cache.delete(key)
+ 8.cache.delete_many(key_list)


### 浏览器缓存策略-强缓存
不会向服务器发送请求，直接从缓存中读取资源
+ 1.响应头-Expires：定义缓存过期时间，是服务器端的具体的时间点
样例：Expires:Thu,02 Apr 2030 05:14:08 GMT
+ 2.响应头-Cache-Control
'Cache-Control:max-age=120'120秒后缓存失效
说明：目前服务器都会带着这两个头同时响应给浏览器，浏览器优先使用Cache-Control
cache-page整体缓存自带强缓存功能
### 浏览器缓存策略-协商缓存
强缓存的对象是一些静态文件、大图片等，考虑到这类资源比较费带宽且不易变化，强缓存到期后，浏览器会根服务器进行协商，当前缓存是否可用，如果可用，服务器不必返回数据，浏览器继续使用原来缓存的数据，如果文件不可用，则返回最新数据。
+ 1.Last-Modified响应头：文件的最近修改时间，同时告诉服务器到期后协商
+ 2.If-Modified-Since请求头，浏览器向服务器请求协商，如果资源未发生变化，则返回304（响应体为空），否则返回200代表缓存不可用（响应体为最新资源）

上述的两个头仅通过精确到秒的时间来判断缓存是否有效，不是特别精准，后来HTTP又引入了新的缓存头：
+ 3.Etag响应头：返回当前资源的唯一标识（由服务器生成），只要资源变化，Etag就会重新生成
+ 4.缓存到期，浏览器返回If-None-Match请求头，给服务器请求协商，服务器比对文件标识，不一致则认为资源不可用。

## 中间件 
+ 中间件是请求/响应的钩子框架，用于全局改变Django的输入和输出。
+ 中间件以类的形式体现
+ 每个中间件负责一些特定的功能

### 中间件方法
继承django.utils.deprecation.MiddlewareMixin类
中间件类须实现下列五个方法中的一个或多个：
+ process_request(self,request)
执行路由之前被调用，在每个请求上调用，只能返回None或HttpResponse对象，None则通过。
+ process_view(self,request,callback,callback_args,callback_kwargs)
在视图之前调用，在每个请求上调用，返回None或HttpResponse对象。
+ process_response(self,request,response)
在响应返回浏览器被调用，在每个请求调用，返回HttpResponse对象。
+ process_exception(self,request,exception)
当处理过程中抛出异常时调用，返回一个HttpResponse对象。用来统一抓所有视图函数的异常。
+ process_template_response(self,request,response)
在响应中包含render方法时被调用，该方法返回二次封装后的render响应对象。


中间件中的大部分方法返回None则表示进入下一项时间，返回HttpResponse则被拦截。

### 编写中间件
+ 1.注册中间件settngs.py-MIDDLEWARE
+ 2.建立中间件包，编写中间件’

中间件的执行顺序
先由上到下，在进入视图后变为由下至上
![Django工作流程图片](https://gimg2.baidu.com/image_search/src=http%3A%2F%2Fresource.shangmayuan.com%2Fdroxy-blog%2F2019%2F11%2F30%2Feb4f43aabc4246c9833b1ea85b4c1870-1.jpg&refer=http%3A%2F%2Fresource.shangmayuan.com&app=2002&size=f9999,10000&q=a80&n=0&g=0n&fmt=jpeg?sec=1628171335&t=98388132f33ffd35cf82d62b95a82fef)

## CSRF - 跨站伪造请求攻击
利用Cookies自动提交功能，同时利用了form表单及一些html控件的跨域提交。

django的防御机制：页面和COOKIE都有一个暗号，只有两个暗号同时发送到服务器并且能够匹配，才允许POST。
配置步骤：
+ 保证django.middleware.csrf.CsrfViewMiddleware打开
+ 模板中，form标签下添加如下标签：
{% csrf_token %}

当个别视图不需要django进行csrf保护可以用装饰器关闭对此视图的检查
```python
from django.views.decorators.csrf import csrf_exempt

@csrf_exempt
def my_view(request):
    return …
```

## 分页
分页指在web页面有大量数据需要显示，为了阅读方便在每个页中只显示部分数据。

优点：
+ 1. 方便阅读
+ 2. 减少数据提取量，减轻服务器压力。

Django提供了Paginator类可以方便的实现分页功能
Paginator类位于'django.core.paginator'模块中
### paginator对象
负责分页数据整体的管理
> pagenator = Paginator(object_list,per_page)
- object_list 需要分页数据的对象列表，如QuerySet
- per_page 分页数据个数
- 返回值：Paginator对象

Paginator对象属性
+ count：需要分页的数据对象总数
+ num_pages:分页后的页面总数
+ page_range：从1开始的range对象，用于记录当前页码数
+ per_page：每页数据个数

Paginator方法
paginator对象.page(number)
    -参数number为页码信息(从1开始)
    -返回当前number页对应的页信息
    -如果提供的页码不存在，抛出InvalidPage异常，包含两种异常子类-PageNotInteger页码不是整数和-EmptyPage页码超页

### page对象
Paginator对象的page()方法返回Page对象
page = paginator.page(页码)

page对象属性：
+ object_list：当前页上所有数据对象的列表
+ number:当前页面的序号，从1开始
+ paginator：当前page对象相关的Paginator对象

Page对象方法： 
+ has_next():如果有下一页返回True
+ next_page_number():下一页页码，如果下一页不存在，抛出InvalidPage异常
+ has_previous:如果有上一页返回True
+ previous_page_number():返回上一页的页码，如果上一页不存在，抛出InvalidPage异常。
+ has_other_pages：如果有上一页或者有下一页返回True


## csv文件
+ csv文件：逗号分隔值文件，其文件以纯文本形式存储表格数据（数字或文本）
说明：可被常见制表工具，如excel等直接进行读取

### python中生成csv文件
python提供了内建库 -csv；可直接通过该库操作csv文件。
案例：
```python
import csv
with open('eggs.csv','w', newline='') as csvfile:# newline指文件输出时换行符怎么处理，空字符串将不会转义
    writer = csv.writer(csvfile)
    writer.writerow(['a','b','c'])
```

### csv文件下载
在网站中实现下载csv，注意如下：
+ 响应Content-Type类型需修改为text/csv。这告诉浏览器该文档是CSV文件，而不是HTML文件
+ 响应为额外添加一个Content-Disposition标头，其中包含CSV文件的名称，它将被浏览器用于开启"另存为"对话框。
```python
import csv
from django.http import HttpResponse
from .models import Book

def make_csv_view(request):
    response = HttpResponse(content_type = 'text/csv')
    response['Content-Disposition'] = 'attachment;filename = "mybook.csv"'
    all_book = Book.objects.all()
    writer = csv.writer(response)
    writer.writerow(['id','title'])
    for b in all_book:
        writer.writerow([b.id,b.title])
    return response
```

## 内建用户系统
模型类位置
from django.contrib.auth.models import User
（mysql中auth_user表）
字段（属性）：
+ username 用户名
+ password 密码
+ email 邮箱
+ first_name 名
+ last_name 姓
+ is_superuser 是否为超级用户
+ is_staff 是否为内部员工
+ is_active 伪删除
+ last_login 上次登陆时间
+ date_joined 用户创建时间

命令：
+ 1.创建普通用户create_user,主要是需要处理密码，将自动转化密码为hash值。
```python
from django.contrib.auth.models import User
user = User.objects.create_user(username="用户名",password="密码",email="邮箱",…)
```
+ 2.创建超级用户create_superuser

+ 3.删除用户(伪删除，更新操作)

+ 4.校验密码
```python 
from django.contrib.auth import authenticate

user = authenticate(username=username, password=password)
```
如果用户名密码校验成功则返回用户对象，否则返回None

+ 5.修改密码set_password

+ 6.登录状态保持
只存session，且时间不可控
```python
from django.contrib.auth import login
def login_view(request)：
    user = authenticate(username=username, password=password)
    login(request,user)
```
+ 7.登录状态校验
```python
from django.contrib.auth.decorators import login_required
@login_required
def index_view(request):
    login_user = request.user#直接能拿到usert对象
```
+ 8.登录状态取消
```python
from django.contrib.auth import logout
def logout_view(request):
    logout(request)
```
**内建用户表-扩展字段**
方案1:通过建立细腻哦啊，和内建表做1对1映射
方案2：继承抽象user模型类
方案2步骤：
+ 1.添加应用
+ 2.定义模型类，继承AbastractUser
+ 3.settings.py中指明AUTH USER MODEL = '应用名.类名'

**！！注意：此操作需要在第一次migrate之前进行！！**
```python
from django.db import models
from django.contrib.auth.models import AbstractUser

class UserInfo(Abstractuser):
    phone = models.CharField(max_length = 11, default = '')
```
在settings.py里面添加配置：
AUTH_USER_MODEL = 'user.UserInfo'

添加用户
from user.models import UserInfo

UserInfo.objects.create_user(username=……,phone='')

## 文件上传
定义：用户可以通过浏览器将图片等文件传至网站
场景：
+ 上传头像
+ 上传流程性的文件

### 上传规范-前端
上传必须为POST提交方式
表单'<\form>'中文件上传时必须有带有enctype='multipart/form-data'时才会包含文件内容数据。
表单中用\<input type = 'files' name='xxx'\>标签上传文件

### 上传规范-后端
视图函数中，需要用request.FILES取文件框的内容
file=request.FILES['xxx']
说明：
+ 1.FILES的key对应页面中file框的name值
+ 2.file绑定文件流对象
+ 3.file.name文件名
+ 4.file.file文件的字节流数据

配置文件的访问路径和存储路径
在settings.py中设置MEDIA相关配置，Django将用户上传的文件统称为media资源
```python
MEDIA_URL='/media/'
MEDIA_ROOT = os.path.join(BASE_DIR, 'media')
```
同时MEDIA_URL和MEDIA_ROOT还需要手动绑定：在主路由添加：
```python
from django.conf import settings
from django.conf.urls.static import static
urlpatterns += static(settings.MEDIA_URL,document_root=settings.MEDIA_ROOT)
```
等价于做了MEDIA_URL开头的路由，Django接到该特征请求后去MEDIA_ROOT路径查找资源

文件写入：
+ 1.open方法
可能出现文件名称的重名问题
```python
# 在视图函数POST中
a_file = request.FILES['myfile']
fileadd = os.path.join(settings.MEDIA_ROOT,afile.name)
with open(filename,'wb') as f:
    data = afile.file.read()
    f.write(data)
return HttpResponse
```
+ 2.借助ORM
字段FileField(upload = '子目录名')
建表时增加一个字段即可，实际收到后直接在视图函数中将绑定文件流对象扔给对应字段即可
```python
Content.objects.create(desc = title, content = a_file)
```
该方法若文件名重复则django将自动添加后缀

## django发送邮件
业务场景：
+ 业务报警
+ 邮件验证
+ 密码找回

### 邮件相关协议
+ SMTP：Simple Mail Transfer Protocol，简单邮件传输协议（port：25）
负责邮件的发送，属于“推送”协议

+ IMAP：Internet Mail Access Protocol，交互式邮件访问协议，应用层协议（port：143）
负责本地邮件客户端访问远程服务器上的邮件，属于“拉取”协议

+ POP3：Post Office Protocol3：邮局协议第3个版本，是TCP/IP协议族中的一员（port：110）
本协议主要用于支持使用客户端远程管理在服务器上的电子邮件，同样属于“拉取”协议。

**IMAP VS POP3:**
+ 两者均为“拉取”协议，负责从邮件服务器中下载邮件
+ IMAP支持摘要浏览功能，并且是双向协议，客户端操作可以反馈给服务器
+ POP3必须下载全部邮件，且为单向协议，客户端操作无法同步服务器。

### Django发邮件
Django中配置邮件功能，主要为SMTP协议，负责发邮件
原理：
+ 给Django授权一个邮箱
+ Django用该邮箱给对应收件人发送邮件
+ django.core.mail封装了电子邮件的自动发送SMTP协议

授权：
+ 邮箱端修改
开启SMTP相关的功能，获得授权码。
+ Django修改(settings.py添加)
```python
EMAIL_BACKEND = 'django.core.mail.backends.smtp.EmailBackend'# 引擎：写死
EMAIL_HOST = 'smtp.qq.com'# 腾讯qq邮箱的SMTP服务器地址
EMAIL_PORT = 25# 默认25
EMAIL_HOST_USER = 'xxxx@qq.com'
EMAIL_HOST_PASSWORD = '*******'# 指的是授权码
EMAIL_USE_TLS = False # 与SMTP服务器通讯时，是否启动TLS连接（安全链接）默认为False，安全协议比较耗时
```

发送：
```python
from django.core import mail
mail.send_mail(
    subject,
    message,
    from_email,# 发送邮箱
    recipient_list = ['xxx@qq.com'],# 接受者邮箱列表
)
返回1则成功发送
```

通过中间件可以捕获所有视图函数的异常，并发送到指定邮箱
process_exception(self,request,exception)
定位错误位置：异常追溯
```python
import traceback
traceback.format_exc()# 直接获得错误具体位置及出错信息
```
邮箱里可以直接发送traceback.format_exc()

自定义收件人：
在settings里自定义属性，在调用send_mail位置
```python
from django.conf import settings
recipient_list = settings.自定义属性
```


## 项目部署
在软件开发完毕后，将开发机器上运行的软件实际安装到服务器上进行长期运行
1. 安装机器上安装和配置同版本的环境[py,数据库等]
2. django项目迁移，第三方工具：finalshare等
ubuntu直接用scp命令
sudo scp 需要复制文件路径 远程ip:路径
3. 用uWSGI替代python manage.py runserver方法
4. 配置nginx反向代理服务器
5. 用nginx配置静态文件路径，解决静态路径问题

### uWSGI
WSGI：Web Server Gateway Interface，Web服务器网关接口，是Pyhton应用程序或框架和Web服务器之间的一种接口。
uWSGI：WSGI的一种，实现了http协议、WSGI协议、uwsgi协议等多种协议。在python web圈热度极高，主要以学习配置为主。

uWSGI安装
pip命令可以安装
[ubuntu验证安装]:sudo pip3 freeze|grep -i 'uwsgi'
[ubuntu安装]:sudo pip3 install uwsgi==2.0.18 -i https://pypi.tuna.tsinghua.edu.cn/simple/

配置uWSGI:
+ 项目同名文件夹/uwsgi.ini

如mysite1/mysite1/uwsgi.ini
文件以[uwsgi]开头，有如下配置项：
+ 1监听端口
套接字方式的IP地址：端口号[此模式需要有nginx]
socket=127.0.0.1:8000
Http通信凡是的IP地址：端口号
http=127.0.0.1:8000
+ 2项目当前工作目录
chdir=**绝对路径**
+ 3项目中wsgi.py文件目录，相对于当前工作目录
wsgi-file=**相对路径**
+ 4进程个数(最多为cpu核数)
process=4
+ 5每个进程的线程个数
threads=2
+ 6服务的pid记录文件
pidfile=uwsgi.pid
+ 7服务的日志文件位置(后台启动以及所有日志位置)
daemonize=uwsgi.log
+ 8开启主进程管理模式
master=true