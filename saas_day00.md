## saas平台

- bug追踪

### 1.如何学&如何讲

- 讲
  - 拆解知识点
  - 分配任务，完成功能
  - 讲解
- 学
  - 守规则
  - 写代码
  - 实事求是

### 2.涉及的知识点

- 虚拟环境，电脑上创建多个python环境

  ~~~
  py3:
  	django 1.11版本 --> crm [维护]
  	django 2.0版本  --> 新项目
  虚拟环境：
  	py3(django/pymysql/sms/redis/celery)
  	- 虚拟：py3 纯净 django 1.11
  	- 虚拟2：py3 纯净 django 2.0
  ~~~

- local_settings.py

  ```
  为了区分开发和测试的数据库和隐私设置等
  ```

  ```python
  try:
  	from .local_settings import *
  except ImportError:
  	pass
  ```

  ```python
  # local_settings.py 中重写配置
  LANGUAGE_CODE = 'zh-hans'
  
  DATABASES = {
  	'default':{
          'ENGINE':'django.db.backens.sqlite3',
          'name':'database_test',
      }
  }
  ```

- 腾讯云平台

  - sms短信，申请服务

  - cos对象存储，使用腾讯云，项目中上传的文件/查看文件/下载

    ```
    使用自己的电脑存储，速度慢
    ```

- redis

  ```
  MySQL:
  	pymysql           -->          mysql软件 -->	硬盘中操作，速度慢
  										读取语句create table
  										insert into 
  redis:
  	redis模块          -->          redis软件 --> 行为：
  										set name = 'jack' 在内存中
  										get name 从内存中获取，速度块
  										超时时间
  # 可以在一台电脑上操作（本地测试）
  ```

### 3.项目开发

- 一期：用户认证（短信认证、图片验证码、django ModelForm组件）

- 二期：wiki、文件、问题管理

- 三期：支付、部署（linux）

  ​			一共需要12天左右	

  



















































