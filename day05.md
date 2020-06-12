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

| 分类   | 标题       | 价格 | 创建项目 | 项目成员 | 每个项目空间 | 创建时间 |
| ------ | ---------- | ---- | -------- | -------- | ------------ | -------- |
| 免费版 | 个人免费版 | 0    | 5        | 5        | 10G          |          |
| 收费版 | vip        | 199  | 20       | 100      | 50G          |          |
| 收费版 | vvip       | 299  | 50       | 200      | 100G         |          |
| 其他   |            |      |          |          |              |          |

注意：新用户注册拥有免费版的额度