# day10 文件管理

知识点

- 模态对话框&ajax&后台modelform校验
- 目录切换：展示当前文件夹& 文件
- 删除文件夹：嵌套的子文件&子文件全部删除
- js上传文件到cos（wiki用python上cos上传文件）
- 进度条操作
- 删除文件
  - 数据库中删除
  - cos中文件删除
- 下载文件 

## 今日概要

- 设计 
- 表结构的创建
- 单独知识点  

## 今日详情

### 1 功能设计

![image-20200616223813089](D:\km_py\markdown_note\saas_day10 文件管理.assets\image-20200616223813089.png)	

### 2 数据库的设计

| id   | 项目id | 类型 | 文件<br />文件夹 | 大小 | 父目录 |      | 更新者 | 更新时间 | key          |
| ---- | ------ | ---- | ---------------- | ---- | ------ | ---- | ------ | -------- | ------------ |
|      |        |      |                  |      |        |      |        |          | 生成的文件名 |
|      |        |      |                  |      |        |      |        |          |              |
|      |        |      |                  |      |        |      |        |          |              |

### 3 知识点

#### 3.1 url传参/不传参

```python 
url(r'^file/$', manage.file, name='file'),
```

```python
# file
# file/?folder_id =50
def file(request, project_id):
	folder_id = request.GET.get('folder_id')
```

#### 3.2 模态框+警告框

嵌套

#### 3.3 获取导航条

```python
# file
# file/?folder_id =50
def file(request, project_id):
    url_list = []
	folder_id = request.GET.get('folder_id')
    if not folder_id:
        pass
    else:
        file_object = models.fileRepository.objects.filter(id=folder_id, file_type=2).first()
        row_object = file_object
        while row_object:
            url_list.insert(0,{name:row_object.name, id=row_object.id})
            row_object = row_object.parent
    print(url_list)
        
        
```

#### 3.4 cos上传文件：python实现文件上传

```python
def upload_file(bucket, region, file_object, key):
    """
    上传文件
    :param bucket:
    :param region:
    :return:
    """
    secret_id = settings.TENCENT_SECRET_ID  # 替换为用户的 secretId
    secret_key = settings.TENCENT_SECRET_KEY  # 替换为用户的 secretKey
    region = region  # 替换为用户的 Region

    config = CosConfig(Region=region, SecretId=secret_id, SecretKey=secret_key)

    client = CosS3Client(config)

    # 上传文件

    response = client.upload_file_from_buffer(
        Bucket=bucket,
        Body=file_object,  # 本地文件的路径
        Key=key,  # 上传到桶之后的文件名
    )
    # 返回图片路径
    return "https://{}.cos.{}.myqcloud.com/{}".format(bucket,region,key)
```

#### 3.5 cos上传文件：js直接上传>>>建议官方文档

##### 1 下载js（前端sdk）

腾讯云下载js

```
<script src="./cos-js-sdk-v5.min.js"></script>
```



##### 2  前端代码

存在跨域请求的问题

```html
<input id="file-selector" type="file">
<script src="dist/cos-js-sdk-v5.min.js"></script>
<script>
var Bucket = 'examplebucket-1250000000';
var Region = 'COS_REGION';     /* 存储桶所在地域，必须字段 */

// 初始化实例
var cos = new COS({
    getAuthorization: function (options, callback) {
        // 异步获取临时密钥
        $.get('http://example.com/server/sts.php', {
            bucket: options.Bucket,
            region: options.Region,
        }, function (data) {
            var credentials = data && data.credentials;
            if (!data || !credentials) return console.error('credentials invalid');
            callback({
                TmpSecretId: credentials.tmpSecretId,
                TmpSecretKey: credentials.tmpSecretKey,
                XCosSecurityToken: credentials.sessionToken,
                // 建议返回服务器时间作为签名的开始时间，避免用户浏览器本地时间偏差过大导致签名错误
                StartTime: data.startTime, // 时间戳，单位秒，如：1580000000
                ExpiredTime: data.expiredTime, // 时间戳，单位秒，如：1580000900
            });
        });
    }
});

// 接下来可以通过 cos 实例调用 COS 请求。
// TODO

</script>
```

##### 3 跨域问题

![image-20200617001450433](D:\km_py\markdown_note\saas_day10 文件管理.assets\image-20200617001450433.png)

![image-20200617001817599](D:\km_py\markdown_note\saas_day10 文件管理.assets\image-20200617001817599.png)



#### 3.6 cos上传文件：临时秘钥【推荐】

##### 1.路由

```python
url(r'^demo2/$', manage.demo2, name='demo2'),
url(r'^cos/credential/$', manage.cos_credential, name='cos_credential'),
```

##### 2.视图

```python
def demo2(request):
	return render(request, 'demo2.html')


def cos_credential(request):
    # 生成一个临时凭证，并返回给后端
    # 安装一个生成临时凭证的python模块 pip install -u qcloud-python-sts
    # 写代码
    
```

##### 3.html页面

```js
var COS = require('cos-js-sdk-v5');
var cos = new COS({
    // 必选参数
    getAuthorization: function (options, callback) {
        // 服务端 JS 和 PHP 例子：https://github.com/tencentyun/cos-js-sdk-v5/blob/master/server/
        // 服务端其他语言参考 COS STS SDK ：https://github.com/tencentyun/qcloud-cos-sts-sdk
        // STS 详细文档指引看：https://cloud.tencent.com/document/product/436/14048
        $.get('http://example.com/server/sts.php', {
            // 可从 options 取需要的参数
        }, function (data) {
            var credentials = data && data.credentials;
            if (!data || !credentials) return console.error('credentials invalid');
            callback({
                TmpSecretId: credentials.tmpSecretId,
                TmpSecretKey: credentials.tmpSecretKey,
                XCosSecurityToken: credentials.sessionToken,
                // 建议返回服务器时间作为签名的开始时间，避免用户浏览器本地时间偏差过大导致签名错误
                StartTime: data.startTime, // 时间戳，单位秒，如：1580000000
                ExpiredTime: data.expiredTime, // 时间戳，单位秒，如：1580000900
            });
        });
    }
});
```

##### 4.跨域问题

#### 3.7 创建项目/创建桶

```python
def project_list(request):
    """项目列表"""
   	...
    form = ProjectModelForm(request, data=request.POST)
    if form.is_valid():
        # 创建一个桶
        bucket = '{}-{}-1302167637'.format(request.tracer.user.mobile_phone, str(int(time.time())))
        region = 'ap-nanjing'
        create_bucket(bucket,region)
        # 验证通过:项目名、颜色、描述
        form.instance.creator = request.tracer.user
        form.instance.bucket = bucket
        form.instance.region = region
        # 创建项目
        form.save()
        return JsonResponse({'status': True, })
    return JsonResponse({'status': False, 'error': form.errors})
```

```python
def create_bucket(bucket, region="ap-chengdu"):
    """
    创建桶
    :param bucket: 桶名称
    :param region: 区域
    :return:
    """

    config = CosConfig(Region=region, SecretId=settings.TENCENT_COS_ID, SecretKey=settings.TENCENT_COS_KEY)
    client = CosS3Client(config)
    client.create_bucket(
        Bucket=bucket,
        ACL="public-read"  # private  /  public-read / public-read-write
    )

    cors_config = {
        'CORSRule': [
            {
                'AllowedOrigin': '*',
                'AllowedMethod': ['GET', 'PUT', 'HEAD', 'POST', 'DELETE'],
                'AllowedHeader': "*",
                'ExposeHeader': "*",
                'MaxAgeSeconds': 500
            }
        ]
    }
    client.put_bucket_cors(
        Bucket=bucket,
        CORSConfiguration=cors_config
    )
```



## 总结

- python 直接上传
- js+临时凭证（跨域问题）



#### 3.8  markdown上传文件（无改动）

#### 3.9 js上传文件

- 临时凭证：当前项目&区域（request.tracer.project...)
- js上传文件：当前设置的桶、区域

#### 3.10 this

```js
var name = 'jack';

function fun(){
    var name = 'jack222'
	console.log(name) //jack222
};

func(); 
```

```js
var name = 'jack';

function fun(){
    var name = 'jack222'
	console.log(this.name) //jack
};

func(); //相当于window.func()
```

```js
var name = 'jack';

info = {
	name:'cc',
	func:function (){
	console.log(this.name) //cc
};
}


into.func();
```

**总结：每个函数都是一个作用域，在他的内部都会存在this，谁调用的函数，那么this就是谁** 

```js
var name = 'jack';

info = {
	name:'cc',
	func:function (){
	console.log(this.name)//info.name cc
	function test(){
	console.log(this.name);//window.name jacg
	}
	test()
};
}


into.func();
```

```js
var name = 'jack';

info = {
	name:'cc',
	func:function (){
	var that= this;
	function test(){
	console.log(that.name);// 	info.name --> cc
	}
	test()
};
}


into.func();
```



#### 3.11 闭包

```js
data_list = [11,22,33]
for(var i=0; i++; i<data.length){
	console.log(i, data[i])
}
```

```js
data_list = [11,22,33]
for(var i=0; i++; i<data.length){
	// 循环会发送三次ajax请求，由于ajax是异步请求，所以在发送请求时候不会等待
	$.ajax({
		url:"..",
		data:{value:data_list[i]},
		success:function(res){
			//1分钟之后执行回调函数
			console.log(i);  //输出2
		}
		
		
	})
}
console.log(i) //输出：2
```

```
data_list = [11,22,33]
for(var i=0; i++; i<data.length){
	// 循环会发送三次ajax请求，由于ajax是异步请求，所以在发送请求时候不会等待
	function xx(data){
	$.ajax({
		url:"..",
		data:{value:data_list[i]},
		success:function(res){
			//1分钟之后执行回调函数
			console.log(data);  //输出0,1,2 创建三个封闭的区域 
		}
		
		
	})
	}
	xx(i)
}
console.log(i) //输出：2
```

```js
data_list = [11,22,33]
for(var i=0; i++; i<data.length){
	// 循环会发送三次ajax请求，由于ajax是异步请求，所以在发送请求时候不会等待
  // 使用自执行函数完成
	(function(data){
	$.ajax({
		url:"..",
		data:{value:data_list[i]},
		success:function(res){
			//1分钟之后执行回调函数
			console.log(data);  //输出0,1,2 创建三个封闭的区域 
	})(i)
}
console.log(i) //输出：2
```

注意事项：如果以后循环，循环内容发送异步请求，异步任务完成后，返回的已经是循环以后的值了