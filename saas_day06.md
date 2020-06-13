# day06

## 1 创建表结构

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

## 2 创建价格策略离线脚本

```python
from scripts import base

from web import models


def run():
    exists = models.PricePolicy.objects.filter(category=1, title='个人免费版').exists()
    if not exists:
        models.PricePolicy.objects.create(
            category=1,
            title='个人免费版',
            price=0,
            project_num=3,
            project_member=2,
            project_space=20,
            per_file_size=5,
        )


if __name__ == '__main__':
    run()

```

## 3 用户注册修改

- 以前创建用户
- 现在创建用户+创建交易记录

## 4 添加项目

### 4.1 项目列表母版+样式

### 4.2 添加

### 4.3 查看项目列表

### 4.4 星标