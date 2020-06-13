# day03 用户认证

## 内容回顾&补充

+  虚拟环境 virtualenv(每个项目创建独立虚拟环境)

+ requirements.txt(pip freeze>requirements.txt)

+ local_settings.py

+ gitignore

+ 腾讯云短信/阿里云短信（阅读文档，文档不清晰：谷歌、必应、搜狗、百度）

  + API，提供URL 访问这些URL并根据提示传入参数

    ```python
    request.get("http://www.xxx.com/cs/csd/cs",json={....})
    ```

  + SDK，模块，下载安装模块，基于模块完成任务

    ```python
    xxx.py
    	def funx():
    	return request.get("http://www.xxx.com/cs/csd/cs",json={....})
    ```

    ```python
    pip install xxx
    ```

    ```python
    xxx.func()
    ```

+ redis,在内存中存取数据（基于内存的数据库）

  + 第一步：在A主机安装redi&配置&启动

  + 第二不：链接redis

    + 方式一：利用redis提供的客户端

    + ![image-20200531002757476](C:\Users\km\AppData\Roaming\Typora\typora-user-images\image-20200531002757476.png)

    + 方式二：利用相关模块

      + 安装模块

      ```python
      pip install redis
      ```

      - 使用模块

        ```python
        import redis
        
        conn = redis.Redis(host='10.211.22.221', port=6379,password='foobared',encoding='utf-8')
        conn.set('name','xxx', ex=10)
        valur = conn.get('name')
        print(valur)
        ```

        创建连接池

        ```python 
        import redis
        # 创建redis连接池,默认连接池最大连接数 2**31
        pool = redis.ConnectionPool(host='10.211.22.221', port=6379,password='foobared',encoding='utf-8')
        conn = redis.Redis(connection_pool=pool)
        conn.set('name','xxx', ex=10)
        valur = conn.get('name')
        print(valur
        ```

    + django-redis，在django中方便的使用redis

      ```python
      不方便：redis模块+连接池
      方便：django-redis
      ```

      - 安装 `django-redis`

        ```python
        pip install django-redis
        ```

      - 配置

      ```python
      # 配置settings.py(local_settings.py)
      CACHES = {
          "default": {
              "BACKEND": "django_redis.cache.RedisCache",
              "LOCATION": "redis://10.211.55.28:6379", # 安装redis的主机的 IP 和 端口
              "OPTIONS": {
                  "CLIENT_CLASS": "django_redis.client.DefaultClient",
                  "CONNECTION_POOL_KWARGS": {
                      "max_connections": 1000, # 连接池的个数
                      "encoding": 'utf-8'
                  },
                  "PASSWORD": "foobared" # redis密码
              }
          },
          "master": {
              "BACKEND": "django_redis.cache.RedisCache",
              "LOCATION": "redis://10.211.55.24:6379", # 安装redis的主机的 IP 和 端口
              "OPTIONS": {
                  "CLIENT_CLASS": "django_redis.client.DefaultClient",
                  "CONNECTION_POOL_KWARGS": {
                      "max_connections": 1000, # 连接池的个数
                      "encoding": 'utf-8'
                  },
                  "PASSWORD": "foobared2" # redis密码
              }
          }
      }
      ```

      ```python
      from django.shortcuts import HttpResponse
      from django_redis import get_redis_connection
      def index(request):
          # 去连接池中获取一个连接
          conn = get_redis_connection("default") # 可以连接多台redis主机，读写分离，默认等于default
          conn.set('nickname', "武沛齐", ex=10)
          value = conn.get('nickname')
          print(value)
          return HttpResponse("OK")
      ```

      

## 今日概要

- 注册
- 短信验证码登录
- 用户名密码登录



## 今日详细

### 1.实现注册

#### 1.1 展示注册页面

##### 1.1.1 创建web的应用&注册app

![image-20200531010248120](C:\Users\km\AppData\Roaming\Typora\typora-user-images\image-20200531010248120.png)

##### 1.1.2 创建templates 并区分各个应用

![image-20200531010538192](C:\Users\km\AppData\Roaming\Typora\typora-user-images\image-20200531010538192.png)

##### 1.1.3 母版的准备

![image-20200531013426769](C:\Users\km\AppData\Roaming\Typora\typora-user-images\image-20200531013426769.png)

##### 1.1.4 url的划分

![image-20200531014635042](C:\Users\km\AppData\Roaming\Typora\typora-user-images\image-20200531014635042.png)

##### 1.1.5 注册页面显示

- 母版中导航
- 注册页面样式
- ModelForm放到指定目录forms

#### 1.2 点击获取验证码

##### 1.2.1 按钮绑定点击事件

##### 1.2.2 获取手机号

##### 1.2.3 发送ajax

##### 1.2.4 手机号校验

- 不能为空
- 格式正确
- 没有注册过

##### 1.2.5 验证通过

- 发送短信
- 将短信保存到redis中(60s)

1.2.6 成功失败

- 失败，错误信息

- 成功，倒计时

  - disabled属性

    ```
    $("#btnSms").prop("disabled", true);添加disabled属性，不可操作
    $("#btnSms").prop("disabled", false);移除disabled属性，可操作
    ```

  - 定时器

  ```js
   var obj = setInterval(function(){
   	console.log(123);
   },1000)
   
   clearInterval(obj) //关闭
  ```

  ```js
  var time = 60
  var obj = setInterval(function(){
   	time = time-1);
  	if(time<1){
                 clearInterval(obj);
                  }
   },1000)
  ```

  

#### 1.3 点击注册



## 内容总结

- 视图 views.py -> views目录

- 模板，根目录templates --> 根据app注册的顺序去每个app的templates中

- 静态文件同上

- 项目中多个app想要各自模板、静态文件隔离，建议通过app名称再进行嵌套即可

- 路由分发

  - include
  - namespace

- 母版

  ```python
  title
  css
  content
  js
  ```

- bootstrap导航条、去除圆角、container

- ModelForm生成HTML标签，自动`id_字段名`

- 发送ajax请求

  ```js
  $.ajax({
  	url:'/index/',
  	type:'GET',
  	data:{},
  	datatype:'JSON',
  	success:function(res){
  		console.log(res)
  	}
  })
  ```

- Form&ModelForm可以进行表单验证

  ```python
  form = sendSmsForm(data=request.POST)
  form = sendSmsForm(data=request.GET)
  ```

- Form&ModelForm使用视图中的值（request）

  ```python
  class SendSmsForm(forms.Form):
      """手机号校验的钩子"""
      mobile_phone = forms.CharField(label='手机号', validators=[RegexValidator(r'^1[3|4|5|6|7|8|9]\d{9}$', '手机格式错误'), ])
  
      # 如果想用视图函数里的参数,需要重写init方法
      def __init__(self, request, *args, **kwargs):
          super().__init__(*args, **kwargs)
          self.request = request
  ```

- 短信

- redis(django-redis)

- 倒计时

  

