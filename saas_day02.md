# day02 

## 内容回顾

- local_settings的作用？

  ```
  本地配置信息
  -开发
  -测试
  -运维
  ```

- .gitignore的作用？

  ```
  git软件 码云或github
  	git init
  	git add
  	git commit
  码云/github/gitlab 代码托管
  ```

- 虚拟环境的作用？

  ```
  项目环境之间的隔离
  开发：本地环境
  线上：多环境隔离
  ```

- 安装项目所需的环境

  ```bash
  pip freeze > requirements.txt
  pip install -r requirements.txt  # 安装全部的环境
  ```

  

## 今日概要

- 腾讯发送短信
- Django的ModelForm组件
- redis
- 注册逻辑设计
- 开发
- 讲解

## 今日详细

### 1.腾讯发送短信

- 注册
- 登录
- 验证
- 创建应用、模板
- 使用SDK发送短信

### 2.Django ModelForm使用

自动生成表单



### 3.下一步思路

- 点击获取验证码
  - 获取手机号
  - 向后台发送ajax请求
    - 手机
    - tpl=register
  - 手机获取验证码(ajax)
  - 验证码时效判断60s
  - ![image-20200517014449898](C:\Users\km\AppData\Roaming\Typora\typora-user-images\image-20200517014449898.png)

### 4.redis基本操作

#### 4.1 安装redis

windows

Linux

#### 4.2 python操作redis的模块

- redis 直接连接

https://pythonav.com/wiki/detail/10/82/

## 作业

- ModelForm页面
- register页面写ajax，手机号和模板字符串tpl
- 校验
- sms和redis
- 进阶
  - 倒计时效果
  - 注册按钮：字段校验+手机验证码