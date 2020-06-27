# day15 问题管理

## 今日概要

- 问题更新+操作记录
- 问题实时筛选
- 邀请成员

## 今日详情

### 1 知识点

#### 1.1 反射

```python
xxx_object.name
getattr(xxx_object,'name')

request.POST
getattr(request, 'POST') 可以通过该方式获取，效果相同

row = models.User.objects.filter(id=1).first()
row.name
row.password
getattr(row,'name')
```

```python
# 给对象设置值
xxx_object.name = '擦擦擦'
setattr(xxx_object, 'name', '擦擦擦')

等同于：
	row = models.User.objects.filter(id=1).first()
	row.email = 'xxx@xx.com'
	setattr(row, 'email', 'xxx@xx.com')
	row.save()
```

用户通过ajax发送`{'v1':'email','v2':'xxx@xx.com}`需要获取后再数据库中更新

```python
def index(request):
	data_list = json.loads(request.body.decode('utf-8'))
	
	user_object = models.User.objects.filter(id=1).first()
	setattr(user_object, data_list['v1'], data_list['v2'])
	user_object.save()
	
	return JsonResponse({'status':True})
```

#### 1.2 django的orm字段

- 可以通过`_meta.get_field(字段)`的方式获取字段对象，然后可以获取字段中的属性值

```python
def index(request):
	data_dict = json.loads(request.body.decode('utf-8'))
	field_object = models.UserInfo._meta.get_field(data_dict['key'])
	print(field_object.verbose_name)
	print(field_object.null) # 可以得到该字段是否为空
	
```

#### 1.3 可迭代对象

类中定义了`__iter__`方法，且**返回一个迭代器**，那么根据类创建的对象，成为可迭代对象

可迭代对象支持for循环

```python
class Foo:
	pass

obj1 = Foo()
obj2 = FOO()
```

```python
class Bar:
	def __iter__(self):
		yield 1
		yield 2
		yield 3
		
obj3 = Bar()
obj4 = Bar()
for循环会打印yield的值0
```

### 2 问题更新

#### 2.1 前端标签绑定事件

#### 2.2 触发事件发送ajax

```
{'name':'issues_type', 'value':'功能'}
```

#### 2.3 后台接收数据后做数据更新

分为四种情况判断，用到反射

#### 2.4 生成更新记录并返回







### 3 筛选

- checjboc 筛选
- select 筛选

















