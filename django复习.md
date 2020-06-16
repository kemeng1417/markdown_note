# django复习

## 1 django命令

### 1.1 下载安装

```bash
pip install django==1.11.28
```

### 1.2 创建django项目

```bash
django-admin startproject 项目名称
```

### 1.3 启动项目

切换到项目的根目录

```bash
python manage.py runserver 127.0.0.1:8000
python manage.py runserver 80
python manage.py runserver 0.0.0.0:80
```

### 1.4 创建app

```bash
python manage.py startapp app名称
```

### 1.5 数据库迁移命令

```bash
python manage.py makemigrations # 迁移文件，检测所有注册下的app下的models的变更，记录变更记录
python manage.py migrate # 迁移 将变更文件同步到数据库中
```

**注意**：迁移文件不能随便删除，删除后必须同步回退数据库中的操作

## 2 django的配置settings.py

```python
BASE_DIR 项目的根目录

INSTALL_APPS = [
	'app01',
	'app01.apps.App01Config'
]

MIDDLEWARE 
TEMPLATES_DIRS = [
	os.path.join(BASE_DIR, 'templates')
]

DATABASE 数据库

STATIC_URL = '/static/'  # 静态文件的别名
STATICFILES_DIRS = [
	os.path.join(BASE_DIR，'static')
]
```

## 3 django项目使用mysql数据库的流程

- 手动创建一个数据库

- 在settings.py中配置数据库的连接

  ```python
  ENGINE 引擎 
  NAME 数据库名字，手动先创建
  HOST 数据库的IP地址
  PORT 3306
  USER 用户名
  PASSWORD 密码
  ```

- 让django使用pymysql连接mysql数据库

  写在与项目同名的目录下的`__init__.py`中

  ```python
  import pymysql
  pymysql.install_as_MySQLdb()
  ```

- 在app下的models.py中写类

  ```python
  class User(models.Model):
      name = models.CharFiled(max_length = 32)
      
  ```

- 执行数据库迁移的命令

  ```bash
  python manage.py makemigrations
  python manage.py migrate
  ```

## 4 request请求

- request.method 请求方法 GET POST
- request.GET URL上携带的参数 ？k1=v1  {}
- request.POST post提交的数据 {}

## 5  response响应

- HttpRespose(字符串) 返回的是字符串
- render(request, 'template.html', {'key':value}) 返回一个完整的HTML页面
- redirect('/地址/') 重定向，避免重定向次数过多

## 6 ORM 对象关系映射

- 对象关系 

  - 类 ---> 表
  - 对象 ---> 数据行（记录）
  - 属性 ---> 字段

  ```python
  class Publisher(models.Model):
      name = models.CharField(max_length=32)
      
  class Book(models.Model):
      name = models.CharField(max_length=32)
      publisher = models.Foreginkey('Publisher', on_delete=models.CASCADE)
      
  class Author(models.Model):
      name = models.CharField(max_length=32)
      books = models.ManyToManyField('Book') # 不会在author表中创建该字段，而是创建第三章关系表
  ```

- ORM操作

  - 查询

    ```python
    from app01 import models
    models.Publisher.objecks.all() # 查询所有的数据 Queryset 对象列表
    models.Publisher.objects.get(name='xx', pk='1') # 获取有且唯一一个对象
    models.Publisher.objects.filter(name='xx', pk='1') # 获取多个对象 对象列表
    
    pub_obj.pk pub_obj.name
    
    book_obj.pub # 外键 所关联的对象Publisher
    book_obj.pub_id  # 所关联对象的id
    
    author_obj.books # 多对多字段  关系管理对象
    author_obj.books.all() # 关联的所有对象 对象列表
    
    ```

  - 新增

    ```python
    models.Pulisher.objects.create(name='xxx')
    models.Book.objects.create(name='xxx', pub=出版社对象)
    models.Book.objects.create(name='xxx', pub_id=出版社对象的id)
    
    author_obj = models.Author.objects.create(name='xxx')
    author_obj.books.set([1,2]) # 如果是字符串id会自动转换
    ```

  - 删除

    ```python
    models.Publisher.objects.get(pk=1).delete() # 通过对象进行删除
    models.Publisher.objects.filter(pk=1).delete() # 批量删除
    ```

  - 修改

    ```python
    pub_obj.name = 'xxx'
    pub_obj.save()
    
    models.Publisher.objects.filter(pk=1).update(name='xxx')
    # 区别：只针对name字段操作，其他字段不更新
    ```

## 7 模板

render(request, '模板的名字', {'key':value})

```django
{{ k1 }} 变量

{% for i in list %}
	{{ forloop.counter }} # 循环的次数
	{{ i.name }}
{% endfor %}

{% if 条件 %}
{% elif 条件 %}
{% else %}
{% endif %}
```

## 8 MVC&MTV

MVC

- M model模型 操作数据库
- V view视图 展示数据 HTML
- C controller 控制器 流程 业务逻辑

MTV

- M model ORM
- T template 模板
- V view 视图 业务逻辑

# django深入

## 1.模板深入

### 1.1 变量

```django
{{ 变量 }}
(.) 顺序查询
1 字典中查询
2 属性或方法 # 方法不能加括号
3 数字索引
```

#### 1.1.1Filter变量过滤器 ，改变变量的显示结果

语法： `{{ value|fifter_name:"参数"}}`

**：**两边没有空格！！！

- default `{{ value|default:"nothing"}}` value为空则显示nothing

- filesizeformat 文件大小格式化一个byte>kb>mb>gb>tb>pb

- add {{ value|add:"2" }}    + value+2 数字会相加  字符串会拼接 列表会拼接

- lower 小写

- upper 大写

- title 标题 首字母大写

- length 变量长度相当于len

- slice 切片 {{ value|slice:"2:-1" }}  和python切片相同

- first 取第一个

- last 取最后一个

- join {{ value|join:"//" }}

- truncatechars 截断字符串 {{ value|truncatechars:9 }}   abcdefgh >   abcdef...

- truncatewoeds 按单词进行截断  以空格区分

- date 格式化日期 和python格式化有点区别 {{ value|date:'Y-m-d H:i:s' }}   可以在settings.py中设置

  ```python
  DATETIME_FORMAT = 'Y-m-d H:i:s' 
  # 或者 TIME_FORMAT,DATE_FORMAT
  USE_LION = False
  ```

-  safe 防止js攻击 告诉模板是安全的 不用转义

  ```python
  可以在自定义的过滤器中导入,模板中不用使用safe过滤器
  from django.utils.safastring import mark_safe
  ```

  

#### 1.1.2 自定义过滤器

- 在已注册的app下创建一个名为templatetags的python包 **包名固定**

- 创建一个python文件，文件名自定义 mytags.py

- 在python文件中写入 

  ```python
  from django import template
  register = template.Library() # register名字不能替换
  ```

- 写函数+加装饰器

  ```python
  @register.filter
  def add_arg(value, arg): # 参数最多有两个
  	# 功能自己写
  	return	"{}_{}".format(value,arg)
  ```

  ```django
  {% load mytags %}
  
  {{ value|add_arg:'dashabi' }}
  ```

  ```django
  <ul>
  	{% for i in list %}
  		<li>{{ forloop.couter}}-{{ name }}</li>
  	{% endfor %}
  </ul>
  ```

1.2 for

forloop.couter 从1开始循环

forloop.couter0 从0开始循环

forloop.revcouter 倒着循环至1结束

forloop.revcouter0 到0结束

forloop.first 是否是第一次循环 布尔值

forloop.last 是否是最后一次循环 布尔值

parentloop 本层循环的最外层循环的变量

```django
{% for foo in kong %}
	{{ foo }}
{% empty %}
	空
{% endfor %}
```

### 1.3 if

if elif else 

if else 

if 支持的语法 and 	or	 ==	 >	 < 	!=	>=	in	not in	is	is not

### 1.4 with

```django
{% with alex=list.o.name %} # 定义中间变量，起别名
	{{ alex }}
{% endwith %}
```

### 1.5 csrf_toke

form表单里写上{% csrf_token %}

```
input请求里隐藏携带name 和 密钥
```

### 1.6 注释

{# #}

### 1.7 withratio 

```django
{% withratio 100 2 1 %}

{# a/b*c #}
```

## 2 母版

### 2.1 定义母版块

在模板中挖坑，通常需对不相同部分进行抽离，相同部分保留，专用的css和js块也进行抽离，方便页面替换

```django
{% block xxx %}

{% endblock %}
```

### 2.2 继承母版

```django
{% extends 'base.html '%}
```

### 2.3 块

通过母版中使用`{% block xxx %}`定义块

### 2.4 组件

可以将常用的页面如导航条，页尾信息等组件保存在单独的文件中，然后可以导入使用。

```django
{% include 'navbar.html '%}
```

## 3 静态文件

```django
{% load static %}
<img src="{% static "images/index.jpg" %}" alt="hello" />
```

某个文件多处被使用可以存为一个变量

```django
{% load static %}
{% static "images/index.jpg" as myphoto %}
<img src="{{ myphoto }}"></img>
```

### 3.1 get_static_prefix

```django
get_static_prefix 可以获取static的路径 返回
'/static/' 后面可直接加文件路径
```

## 4 自定义组件

### 4.1 simpletag

定义注册simple_tag

```
@register.simple_tag
def plus(a, b, c):
 	return "{}+{}+{}".format(a, b, c)
```

使用自定义simple_tag

```django
{% load app01_demo %}
{# simplt tag #}
{% plus "1" "2" "abc" %}
```

### 4.2 **inclusion_tag**

用于返回html代码片段

```python
from django import template

register = template.Library()

@register.inclusion_tag('result.html')
def show_results(n):
	n=1 if n<1 else int(n)
	data = ["第{}项".format(i) for i in range(1, n+1)]
	return {'data':data}
```

新建一个result.html

```html
<ul>
    {% for choice in data %}
    	<li>{{ choice }}</li>
    {% endfor %}
</ul>
```

在index.html导入

```django
<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8">
  <meta http-equiv="x-ua-compatible" content="IE=edge">
  <meta name="viewport" content="width=device-width, initial-scale=1">
  <title>inclusion_tag test</title>
</head>
<body>

{% load my_inclusion %}

{% show_results 10 %}
</body>
</html>
```

# django提高

## 1 视图

### 1.1 CBV和FBV

FBV function based view

CBV class based view

```python
from django.views import View

class xxx(View):
	def get(self, request):
		return response
		
	def get(self request):
		return response
```

```
urlpatterns = [
	url(r'(/publisher_list/), view.PulisherAdd.as_view()),
]
```

as_view()的流程

- 项目运行时加载urls.py的文件，执行类as.view()方法
- as_vire()方法执行，内部定义了一个view函数，并且返回
- 请求到的时候，执行view函数
  - 实例化类-->self
  - self.request = request
  - 执行self.dispatch(request, *args, **kwargs)的方法
    - 判断请求方式是否被允许
      - 允许
        - 通过反射获取请求方式对应的方法---handler
        - 获取不到self.http_method_not_allowed---handler
      - 不允许
        - self.http_method_allowed--hanler
    - 执行handler，返回结果

### 1.2 CBV加装饰器

- 使用一个装饰器

```python
from django.utils.decorators import method_decorator

class xx(View)
    @method_decorator(要装饰的函数)
    def xx():
        return ..
```

- 给所有方法加装饰器

```python
class xx(View):
	 @method_decorator(要装饰的函数)
	 def dispatch(self,request,*args,**kwargs):
	 	res = super().dispatch(request,*args,**kwargs)
	 	return res
```

- 给整个类加装饰器

```python
@method_decorator(要装饰的函数，name='get') name指定要加在哪个函数上面或者直接 name=dispatch
class xx(View):
	def get(self, request):
	pass
	
```

### 1.3 装饰器额外

装饰器推荐写法，增加wraps

```python
from functools import wraps

def timer(func):
	@wraps(func)
	def inner(*args, **kwargs):
		执行前功能
		res = func(*args, **kwargs)
		执行后功能
		return res
	return inner
```

### 1.4 request对象

属性：

- request.method 请求方法GET POST 
- request.GET URL上携带的参数 ？k1=v1&k2=v2  {}
- request.POST POST请求提交的数据 {}



- request.path_info 返回url信息，不包含域名，只包含路径信息
- request.body 请求体，get请求没有请求体 byte类型
- request.scheme  返回http或https 的str
- request.cookies cookie
- request.session session
- request.FILES 上传的文件
- request.META 请求头信息
- request.user 自带的认证系统

方法：

- request.get_host()
- request.get_full_path() 如果携带参数,也会包含，区别于request.path_info
- request.is_secure() 请求是否安全
- request.is_ajax() 请求是否是ajax请求

### 1.5 response对象

```python
from django.shortcuts import render,HttpResponse,redirect
HttpResponse('字符串') # 返回字符串
render(request, '模板的名字', {'ki':v1}) # 返回一个html页面，模板中可以直接使用request对象
redirect('地址') # 重定向 location：地址  响应状态码301 302
JsonResponse 前后端分离
```

```python
from django.http.response import JsonResponse
JsonResponse({'k1':v1})
JsonResponse({'k1':v1},safe=Flase) # 可以返回列表等不是字典类型的数据
```

### 1.6 上传文件

```html
<form action="" method="post" enctype="multipart/form-data">
    {% csrf_token %}
    <input type="file" name="fff">
    <button>上传</button>
</form>
```

```python
f1 = reques.FILES.get('fff')
f1.name # 上传文件的名字
with open('f.name', 'wb') as f:
	for i in f1:
		f.write()
		
```

## 2 路由

### 2.1 基本格式

```python
from django.conf.urls import url
from . import view
urlpatterns =[
	url(正则表达式，views视图, 参数, 别名)
]


urlpatterns = [
    url(r'^articles/2003/$', views.special_case_2003),
    url(r'^articles/([0-9]{4})/$', views.year_archive),
    url(r'^articles/([0-9]{4})/([0-9]{2})/$', views.month_archive),
    url(r'^articles/([0-9]{4})/([0-9]{2})/([0-9]+)/$', views.article_detail),
]

django2.0中 from django.urls import path，re_path
```

**注意**：

- 自上而下匹配，一旦成功不再继续
- 需要获取url的值，只需要放在圆括号内（分组匹配）
- 前面不需要反斜杠，后面需要
- 正则前面的r应该加上

```python
# 是否开启URL访问地址后面不为/跳转至带有/的路径的配置项
APPEND_SLASH=True
```

### 2.2 分组命名规则

正则表达式中，分组命名正则表达式的语法是`(?P<name>pattren)`其中，name是组的名称，pattern是要匹配的模式

### 2.3 特性

- URLconf不检查请求的方法
- 捕获到的参数永远都是字符串
- 视图中可以指定默认值

```python
# urls.py中
from django.conf.urls import url

from . import views

urlpatterns = [
    url(r'^blog/$', views.page),
    url(r'^blog/page(?P<num>[0-9]+)/$', views.page),
]

# views.py中，可以为num指定默认值
def page(request, num="1"):
    pass
```

## 3 models

### 3.1 聚合 aggregate 终止子句

```python
from django.db.models import Max,Min,Count,Sum,Avg

res = models.Book.objects.all().aggregate(max=Max('price'))
```

### 3.2 分组 group by annotate

annotate 注释， 过程中使用了分组

统计每本书的作者个数

```python
ret = models.Book.objects.annotate(Count('authors')).values()  # 添加额外的信息
for i in ret:
	print(i)
```

统计出每个出版社卖的最便宜的书的价格

```python
# 方法1：
res = models.Publisher.Objects.annotate(Min('book_price')).values() # 跨表查询
for i in res:
	print(i)
    
# 方法2：
res = models.Book.objects.values('pub').annotate(Min('price')) # 按照pub__id做的分组
```

统计不止一个作者的图书

```python
res = models.Book.objects.annotate(count=Count('authors')).filter(count__gt=1)
```

### 3.3 F和Q查询

```python
from django.db.models import F, Q

res = models.Book.objects.filter(kucun__lt=50)

res = models.Book.objects.filter(sale__lt=F('kucun')) # where 'sale'>'kucun'
res = models.Book.objects.filter(id__lte=3).update(sale=F('sale')*2+3)
```

```python
ret = models.Book.objects.filter(Q(id__lt=3)|Q(id__gt=5))
```

& | ~ 与 或 非

### 3.4 事务 同生共死

有一条会错误前面会回退

```
from django.db import tansaction

try 必须放在外面 不能放在with里面，否则会影响回退
	# 一系列的操作
	try:
		with transaction.atomic():
		pass
	except Exception as e:
		pass
	
```

## 4 cookie 

保存在浏览本地上的一组组键值对

为什么要有cookie？

​		HTTP协议是无状态的，每次请求都是相互独立的，没有办法保存状态

特性：

- 由服务器让浏览器进行设置的（返回set-cookie的响应头）
- cookie信息保存在浏览器本地，浏览器可以不保存
- 浏览器再次访问时自动携带对应的cookie

django中操作cookie

```python
# 设置cookie
response = HttpResponse('xxx')
response.set_cookie(key, value)

# 获取
request.COOKIES
request.COOKIES.get('key')

# cookie加密
response.set_signed_cookie(key,value, salt='哈哈哈哈')

# 获取加密 cookie
request.get_signed_cookie('key', salt='哈哈哈哈', default='')

# 删除coolkie  将值设置为空，超时时间设置为0
response.delete_cookie(key)

```

参数：

- key
- vlaue
- max_age 超时时间
- path='/'   /表示根路径，根路径下面的都可以获取到cookie  cookie生效路径
- domain=None cookie生效的域名
- secure = True  https进行传输
- httponly = False  https传输 无法被JavaScript传输

清除cookie

- ret.delete_cookie('is_login')  # 删除某个键值对

## 5 session

保存在服务器上的一组组键值对，依赖于cookie使用

为什么使用？

```
1.cookie是保存在浏览器本地，不太安全
2.浏览器会对cookie的大小和个数有一定限制
```

特性：（过程）

1. 第一个请求，没有cookie，设置键值对，根据浏览器生成一个唯一标识，给一个字典设置键值对。

2. 将字典转成字符串（json序列化），进行加密，将唯一标识和字符串保存在数据库里(django_session)
3. 返回给浏览器唯一标识(sessionid)的cookie
4. 下次请求携带sessionid，服务器根据session找到对应的数据(session_data),进行解密，进行反序列化，根据key获取对应的值

django中session的操作

```python
# 设置
request.session[key] = value
# 获取
request.session.get(key)
request.session[key]
# 删除
request.sesion.pop(key)  del request.session[key]
request.session.delete() # 删除所有的键值对， 不删除cookie
request.session.flush() # 删除所有的键值对， 也删除cookie

# 其他
默认的超时时间（2周）
request.session.set_expiry(value)  # 设置超时时间
request.session.clear_expired() # 清除已经失效的session数据

from django.conf import global_settings
```

## 6 中间件

django的中间件是全局范围内处理django的请求和响应的框架级别的钩子。

```PYTHON
from django.utils.deprecation import middlewareMixin

class MD1(middlewareMixin):
	def process_request(self, request):
		pass
		
# settings中要中注册
MIDDKEWARE = [
	'app01.middlewares.my_middleware.MD1',
]
```



5个方法 4个特点

### 6.1 process_request(self, request)

执行时间： 路由匹配之前，视图函数之前

参数：request 请求的对象和视图函数是同一个对象

执行顺序： 按照注册的顺序 顺序执行

返回值：None 正常流程

​				HttpResponse 不执行后续的中间件的Process_request、路由匹配、函数都不执行，直接执行当前中间件的process_response方法

### 6.2 process_response(self, request, response)

执行时间：视图函数之后

参数：request 请求的对象和视图函数是同一个对象

​			response 响应对象

执行顺序：按照注册的顺序，倒序执行

返回值：HttpRsponse 必须返回

### 6.3 process_view(self, request, view_func, view_args, view_kwargs)

执行时间：路由匹配之后，视图函数之前

参数：request 请求的对象和视图函数是同一个对象

​			view_func 视图函数

​			view_args 视图函数的位置参数

​			view_kwargs 视图函数的关键字参数

执行顺序：按照注册的顺序，顺序执行

返回值：HttpRsponse 之后的中间件的process_view 、视图都不执行，执行最后一个中间件的process_response方法

![image-20200525192054788](C:\Users\km\AppData\Roaming\Typora\typora-user-images\image-20200525192054788.png)

### 6.4 process_exception(self, request, exception)

 执行时间：（触发条件）视图中有异常才执行

参数：request 请求的对象和视图函数是同一个对象

​			exception 异常的对象

执行顺序：按照注册的顺序，倒序执行

返回值：

​			None 当前的中间件没有处理异常，交给下一个中间件处理异常，如果都没有处理异常，django处理异常

​			HttpRsponse 当前中间件处理了异常，后面的中间件process_exception方法不执行。执行最后一个中间件的process_response方法

### 6.5 process_template_response(self, request, response)

执行时间：（触发条件）视图中返回的对象是TemplateResponse对象

参数：request 请求的对象和视图函数是同一个对象

​			response 返回的templateResponse对象

执行顺序：按照注册的顺序，倒序执行

返回值：

​			HttpRsponse  必须返回 返回的templateResponse对象

​			过程处理模板的名字，参数

​			response.template_name 模板的名字

​			response.context_data 变量的名字



中间件限制访问频率

```python
visit_history = {
	# 'ip':[time,time]
}

import time
class Throttle(MiddlewareMixin):
    def process_request(self,request):
        ip = request.META.get('REMOTE_ADDR')
        history = visit_history.get(ip, [])
        now = time.time
        new_histoty = []
        for i in history:
            if now - i < 5:
                new_history.append(i)
             
        visit_history[ip] = new_history
        if new_history >=3:
            return HttpResponse('访问太快了')
        new_history.append(now)
```

## 7 ajax请求

JSON

轻量级的文本数据交换格式

python

支持的数据类型：

​	字符串 数字 列表 字典 None 布尔值

序列化

​	python --> json 字符串

反序列化

​	json字符串 --> python



js

ajax 是一个js的技术，异步发送请求的

ajax特点：

1. 异步
2. 局部刷新
3. 传输的数据量小

```js
$.ajax({
    url:'/calc/',
    type:'get',
    data:{
        'x1':$('[name="i1"]').val(),
        'x2':$('[name="i2"]').val(),
    },
    success:function(data){  //回调函数，可以使用箭头函数 =>
        $('[name="i3"]').val(data)
    }
})
```

ajax传收数据：

```js
$.ajax({
	url: '/test/',
	type:'get',
	data:{
		name:'alex',
		age:34,
		hobby: json.stringify(['抽烟','喝酒','撩妹'])
	},
	success:function(data){
		console.log(data)
		console.log(data.status)
	}
})
```

使用ajax上传文件

```js
$('#b1').click(function(){
	var formData = new FormData();	//multipart/form-data
	formData.append('name', 'alex');
	formData.append('f1', $('#f1')[0].files[0]);
	$.ajax({
		url:'/upload/',
		type:'post',
		data: formData,
		processData:false, // ajax不处理数据的编码
		contentType:false, // 不修改content-type的请求头
		success：function(data){
			alert(data)
		}
	})
})
```

```python
from django.views.decorators.csrf import csrf_exempt, csrf_protect
csrf_exempt 加在视图上， 该视图不需要进行csrf校验
csrf_protect 加在视图上， 该视图必须进行csrf校验
ensure_csrf_cookie 加在视图上，确保返回时设置csrftoken的cookie
```

csrf的校验

```python
从cookie中获取csrftoken的值
从request.POST 中获取csrgmiddlewaretoken的值或者从请求头中获取x-csrftoken的值
把这个值做对比，如果相同，通过校验，反之拒绝
```

前提：必须有csrftoken的cookie

1. 使用{% csrf_token %}
2. from django.views.decorators.csrf import ensure_csrf_cookie

让ajax可以通过csrf的校验：

1. 给data添加csrfmiddlewaretoken的键值对
2. 给headers添加x-csrftoken的键值对

## 8 form组件

form

```python
from django import forms
class RegForm(forms.Form):
	user = forms.CharFiled()
	password = forms.CharField(widget=forms.PasswordInput)
	
url(^'reg',reg)

def reg(request)：
	form_obj = RegForm(
    	label='用户名',
        min_length=11,
        inital='初始值',
        required = True,
        disabled = False,
        error_massage={
            'required':'xxx',
            'min_length':'xxxx'
        }
       	validators=[自定义的函数，内置的校验器]
    				)
    if request.method == 'POST':
        form_obj = RegForm(request.POST) # 拿到POST里的所有数据
        # 对提交的数据进行校验
        if form_obj.is_valid():
        	# 校验成功做的操作
            form_obj.clean_data  # request.POST
	return render(request, 'reg.html', {'form_obj':form_obj})
```

```django
<form action="" method='post'>
{% csrf_token %}
{{ form_obj.as_p }}
{{ form_obj.user }} input框
{{ form_obj.user.label }} label提示信息
{{ form_obj.user.id_for_label }} input框的id
{{ form_obj.user.errors }} 某个字段的所有错误
{{ form_obj.user.errors.0 }} 某个字段的第一个错误
    
    
{{ form_obj.errors }} 所有字段的所有错误
<button>注册</button>
</form>
```

校验

校验器：

1. 写函数

   ```python
   from django.core.exceptions import validationError
   def xxx(value):
   	# 写校验规则
   	# 校验成功 什么都不做 return
   	# 校验失败 抛出异常 validationError('提示信息')
   ```

2. 内置的校验器

   ```python
   from django.core.validators import RegexValidator
   ```

钩子函数：

1. 局部钩子

   ```python
   class RegForm(forms.Form):
   	user = form.CharField()
   	
   	def clean_user(self):
   		# 校验
   		# 校验成功 返回该字段的值
   		# 校验失败 抛出异常
   ```

2. 全局钩子

   ```python
   class RegForm(forms.Form):
   	user = forms.CharField()
   	
   	def clean(self):
   		# 校验
   		# 校验成功 所有字段的值 self.clean_data
   		# 校验失败 抛出异常
   ```

   

