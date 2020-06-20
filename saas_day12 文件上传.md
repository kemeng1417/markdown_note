# day12 文件上传

## 今日概要

- 获取临时凭证&上传文件
- 右下角展示进度条
- 上传文件保存到数据库
- 容量的限制



## 今日详细

### 1 获取临时凭证&上传文件

- 创建项目时解决跨域问题：跨域添加
- 上传文件前：临时凭证
  - 全局：默认超时之后，自动再次去获取
  - 局部：每次上传前，进行临时凭证的获取
- 容量限制：
  - 单文件限制
  - 总容量限制
- 继续上传
  - 添加上传进度条及展示
- 上传成功后：将当前上传的信息发送至数据库
  - 前端向cos上传文件成功之后
  - 前端向后台发送请求
- 实时展示添加的文件
- 

扩展：ajax向后台发送消息

```
前端：
	$.ajax({
		...
		data:{name:11,age:12,'xx':[12.23]}
	})
	$.post(url,data,callback)
Django后台：
	request.POST
	request.POST.get('name')
	request.POST.get('age')
	request.POST.getlist('xx')
```

```
前端：
	$.ajax({
		...
		data:{name:{k1:v1,k2:v2}, xx:[11,22,33]}
		data:JSON.stringfy{name:{k1:v1,k2:v2}, xx:[11,22,33]}
	})
	
Django后台
	request.body
	info = json.loads(request.body.decode('utf-8'))
	info['name']
	info['xxx']
```

