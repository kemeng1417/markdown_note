# day05

## 今日概要

- django写离线脚本

- 探讨业务

- 设计表结构

- 功能实现

  - 查看项目列表
  - 创建项目
  - 星标项目

- # 今日详细

## 1.django离线脚本

```
django 框架
离线 非web运行时
脚本 一个或几个py文件
```

在某个py文件中对django项目做一些处理

案例一:使用离线脚本在用户表插入数据

```python
import os
import sys
import django

base_dir = os.path.dirname(os.path.dirname(os.path.abspath(__file__)))
sys.path.append(base_dir)
os.environ.setdefault('DJANGO_SETTINGS_MODULE','tracer.settings')
django.setup()

from web import models
models.UserInfo.objects.create(username='alex',email='55252663@qq.com',mobile_phone='13025252525',password='12345678')
```

案例二：数据库录入全国省市县

案例三：朋友圈项目敏感字、词语、

案例四：saas免费版：项目个数、容量等

## 2 业务探讨

### 2.1 价格策略

| 分类     | 标题       | 价格 | 创建项目 | 项目成员 | 每个项目空间 | 单文件 | 创建时间 |
| -------- | ---------- | ---- | -------- | -------- | ------------ | ------ | -------- |
| 1 免费版 | 个人免费版 | 0    | 5        | 5        | 20M          | 5M     |          |
| 2 收费版 | vip        | 199  | 20       | 100      | 50G          |        |          |
| 3 收费版 | vvip       | 299  | 50       | 200      | 100G         |        |          |
| 其他     |            |      |          |          |              |        |          |

注意：新用户注册拥有免费版的额度

### 2.2 用户

| 用户名      | 手机号      | 密码   |
| ----------- | ----------- | ------ |
| 1 alex      | 13032323232 | 123456 |
| 2 wangyang  | 13025252525 | 123    |
| 3 chengshuo | 13526262626 | 123    |

### 2.3 交易

| id   | 状态   | 用户 | 价格 | 实际支付 | 开始      | 结束      | 数量 | 订单号 |
| ---- | ------ | ---- | ---- | -------- | --------- | --------- | ---- | ------ |
| 1    | 已支付 | 1    | 1    | 0        | 2020.6.13 | null      | 1    | UY12   |
| 2    | 已支付 | 1    | 1    | 0        | 2020.6.13 | null      | 1    |        |
| 3    | 已支付 | 1    | 1    | 0        | 2020.6.13 | null      | 1    |        |
| 4    | 已支付 | 2    | 2    | 199      | 2020.7.13 | 2021.7.13 | 1    |        |
| 5    | 未支付 | 3    | 3    | 299*2    | 020.7.13  | 2021.7.13 | 2    |        |

`request.tracer = 交易对象`

### 2.4 创建存储

基于腾讯对象存储cos存储数据

### 2.5 项目

用户上传

| id   | 项目名称 | 项目描述 | 颜色    | 星标 | 参与人数 |      |      |
| ---- | -------- | -------- | ------- | ---- | -------- | ---- | ---- |
| 1    | CRM      |          | rgb颜色 | true | 5        | 3    |      |
| 2    | LL       |          |         |      | 10       | 3    |      |
| 3    | SAAS     |          |         |      |          |      |      |

### 2.6 项目参与者

| id   | 项目 | 用户 | 星标 |
| ---- | ---- | ---- | ---- |
| 1    | 1    | 1    | true |
| 2    | 1    | 2    |      |
|      | 2    | 2    |      |

## 3 任务

### 3.1 创建相应表结构

```python
from django.db import models


# Create your models here.
class UserInfo(models.Model):
    """用户表"""
    username = models.CharField(max_length=32, verbose_name='用户名', db_index=True)  # 检索速度会加快
    email = models.EmailField(max_length=32, verbose_name='邮箱')
    password = models.CharField(max_length=32, verbose_name='密码')
    mobile_phone = models.CharField(max_length=32, verbose_name='手机号')


class PricePolicy(models.Model):
    """价格策略表"""
    category_choices = ((1, '免费版'), (2, '收费版'), (3, '其他'))
    # 小整数
    category = models.SmallIntegerField(verbose_name='收费类型', choices=category_choices, default=1)
    title = models.CharField(max_length=32, verbose_name='标题')
    # 正整数类型
    price = models.PositiveIntegerField(verbose_name='价格')
    project_num = models.PositiveIntegerField(verbose_name='项目数')
    project_member = models.PositiveIntegerField(verbose_name='项目成员数')
    project_space = models.PositiveIntegerField(verbose_name='项目空间')
    per_file_size = models.PositiveIntegerField(verbose_name='单文件大小(M)')
    create_datetime = models.DateTimeField(verbose_name='创建时间', auto_now_add=True)


class Transaction(models.Model):
    """用户交易表"""
    status_choices = ((1, '已支付'), (0, '待支付'))
    status = models.IntegerField(choices=status_choices, verbose_name='支付状态')
    user = models.ForeignKey(to='UserInfo', verbose_name='用户')
    prices_policy = models.ForeignKey(to='PricePolicy', verbose_name='价格策略')
    count = models.IntegerField(verbose_name='数量(年)', help_text='0表示无限期')
    price = models.IntegerField(verbose_name='实际支付')
    start_time = models.DateTimeField(verbose_name='开始时间', null=True, blank=True)
    end_time = models.DateTimeField(verbose_name='结束时间', null=True, blank=True)
    create_datetime = models.DateTimeField(verbose_name='创建时间', auto_now_add=True)
    number = models.IntegerField(verbose_name='数量')
    order = models.CharField(max_length=64, unique=True, verbose_name='订单号')


class ProjectInfo(models.Model):
    color_choices = ((0, '#00F5FF'),
                     (1, '#00FF00'),
                     (2, '#0000FF'),
                     (3, '#CD5C5C'),
                     (4, '#FF00FF'),
                     (5, '#9A32CD'))
    name = models.CharField(max_length=32, verbose_name='项目名称')
    desc = models.CharField(max_length=256, verbose_name='项目描述', null=True, blank=True)
    color = models.SmallIntegerField(choices=color_choices, verbose_name='项目颜色', default=1)
    star = models.BooleanField(default=False, verbose_name='星标')
    use_space = models.IntegerField(verbose_name='项目已使用的空间', default=0)

    join_count = models.SmallIntegerField(verbose_name='参与人数', default=1)
    creator = models.ForeignKey(verbose_name='创建者', to='UserInfo')
    create_datetime = models.DateTimeField(verbose_name='创建时间', auto_now_add=True)


class ProjectUser(models.Model):
    """项目参与者"""
    project = models.ForeignKey(to='ProjectInfo', verbose_name='项目')
    user = models.ForeignKey(to='UserInfo', verbose_name='用户')
    star = models.BooleanField(default=False, verbose_name='星标')

    create_datetime = models.DateTimeField(verbose_name='加入时间', auto_now_add=True)

```



### 3.2 离线脚本创建价格策略

### 3.3 用户注册

- 新建交易记录【免费版】

### 3.4 添加项目

### 3.5 展示项目

- 星标
- 我创建的
- 我参与的

### 3.6 星标项目

