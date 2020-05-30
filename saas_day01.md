# day01 前戏

## 今日概要

- 虚拟环境（项目环境）
- 项目框架：local_settings
- git实战应用（代码每天提交）
- 通过python&腾讯sms发送短信

## 今日详情

### 1.虚拟环境 virtualenv

#### 1.1 安装

~~~bash
pip install virtualenv
~~~

#### 1.2 创建虚拟环境

```bash
virtualenv 环境名称

# 注意：创建[环境名称] 文件夹，放置所有的环境。创建时先进入指定的目录
```

```bash
假设：电脑上有多个python环境，可以指定python版本

virtualenv 环境名称 --python==python3.6
virtualenv 环境名称 -p "C:\Users\km\AppData\Local\Programs\Python\Python36\python3.6.exe"
virtualenv 环境名称 --python==python2.7

```

```python
1.打开终端
2.安装：virtualenv
	pip install virtualenv
3.关闭终端，重新打开
4.通过命令进入指定目录
	win:
        >>>D:
        >>>cd py_virtual_env
5.创建虚拟环境
	virtualenv s28
```

#### 1.4 激活虚拟环境

```
win:
	>>> cd Scripts 进入虚拟环境 Scripts环境
	>>> activate.bat 激活虚拟环境
mac：
	>>> source s25/bin/activate
```

```
win:
	>>> cd Scripts 进入Scripts目录
	>>> deactivate.bat 退出虚拟环境
mac：
	>>> deactivate
```

- 在激活的环境中安装模块

  ```
  pip install django==1.11.7 或 1.11.28
  pip install django==1.11.7 -i http://pypi.douban.com/simple --trusted-host pypi.douban.com
  ```

  **注意**:激活虚拟环境，在虚拟环境中安装

  **补充**：

  - py3.7+django1.11.7 创建django会报错
  - 解决思路：安装django1.11.28

### 2.搭建项目环境（django+虚拟环境）

![image-20200515191847839](C:\Users\km\AppData\Roaming\Typora\typora-user-images\image-20200515191847839.png)

![image-20200515191923472](C:\Users\km\AppData\Roaming\Typora\typora-user-images\image-20200515191923472.png)

![image-20200515192005865](C:\Users\km\AppData\Roaming\Typora\typora-user-images\image-20200515192005865.png)

### 3.本地配置

local_settings.py

#### 3.1在settings中导入

```python
try:
    from .local_settings import *
except ImportError:
    pass
```

#### 3.2创建local_settings.py

```python
LANGUAGE_CODE = 'zh-hans'
SMS=666
```

**注意：给别人代码时，不要给local_settings.py**

### 4.给别人代码，忽略部分环境

#### 4.1创建一个远程仓库

![image-20200515193714962](C:\Users\km\AppData\Roaming\Typora\typora-user-images\image-20200515193714962.png)

#### 4.2本地代码推送到gitee

- git要忽略一些文件 .gitignore

  ```
  # pycharm
  .idea/
  .DS_Store
  
  __pycache__/
  *.py[cod]
  *$py.class
  
  # Django stuff:
  local_settings.py
  *.sqlite3
  
  # database migrations
  */migrations/*.py
  !*/migrations/__init__.py
  ```

- git管理项目

  ```
  git remote add origin https://gitee.com/kemeng1414/s25.git
  git push -u origin master
  ```

#### 4.3 测试获取代码

```
进入自己想要放代码的目录
git clone https://gitee.com/kemeng1414/s25.git
```

## 今日作业

- 虚拟环境：tracer
- 创建一个django项目：tracer
- git仓库：tracer（.gitignore）