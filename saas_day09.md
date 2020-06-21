# day09 wiki

## 今日概要

- wiki删除
- wiki编辑
- markdown编辑器
  - 添加、编辑
  - 预览页面
- markdown上传图片

## 今日详情

### 1 wiki删除

### 2 wiki编辑

### 3 markdown编辑器

- 富文本编辑器 ckeditor
- markdown编辑器 mdeditor

项目中想要应用markdown编辑器

- 添加和编辑的页面中textarea输入框 -> 转换为markdown编辑器

  ```html
  1. textarea 框通过div包裹以便以后查找并转化为编辑器
  	<div id='editor'>...</div>
  2.  - 应用css
   		<link rel="stylesheet" href="{% static 'plugin/editor-md/css/editormd.min.css' %}">
  	- 应用js
  	 	<script src="{% static 'plugin/editor-md/editormd.min.js' %}"></script>
  3.初始化
  	$(function () {
              initEditorMd();
          });
       function initEditorMd() {
              editormd('editor',{
                  placeholder:'请输入内容',
                  height:500,
                  path:"{% static 'plugin/editor-md/lib/' %}"
              })
          }
  4.全屏问题
  	.editormd-fullscreen{
              z-index: 1001;
          }
  ```

  ```
  https://www.mkeditor.com/
  https://github.com.pandao.editor.md
  https://pandao.github.io/editor.md/exaples/index.html
  ```

- 预览页面按照markdown展示

  ```html
  1.内容区域
  			<div id="previewMarkdown">
                   <textarea>{{ wiki_object.content }}</textarea>
              </div>	
  2.引入css
  <link rel="stylesheet" href="{% static 'plugin/editor-md/css/editormd.preview.min.css' %}">
  3.引入js
  <script src="{% static 'plugin/editor-md/editormd.min.js' %}"></script>
      <script src="{% static 'plugin/editor-md/lib/flowchart.min.js' %}"></script>
      <script src="{% static 'plugin/editor-md/lib/jquery.flowchart.min.js' %}"></script>
      <script src="{% static 'plugin/editor-md/lib/marked.min.js' %}"></script>
      <script src="{% static 'plugin/editor-md/lib/prettify.min.js' %}"></script>
      <script src="{% static 'plugin/editor-md/lib/raphael.min.js' %}"></script>
      <script src="{% static 'plugin/editor-md/lib/sequence-diagram.min.js' %}"></script>
      <script src="{% static 'plugin/editor-md/lib/underscore.min.js' %}"></script>
  4.初始化
  		$(function () {
              initPreviewMarkdown();
          });
  
          function initPreviewMarkdown() {
              editormd.markdownToHTML("previewMarkdown",{
                  // 防止css js代码攻击
  				htmlDecode:"style,script,iframe"
              });
          }
  ```

  总结：编辑器实现markdown编辑和预览

  欠：markdown组件进行上传图片

### 4 腾讯对象存储

#### 4.1 开通服务

![image-20200615222550792](D:\km_py\markdown_note\saas_day09.assets\image-20200615222550792.png)

#### 4.2 创建桶，后台

![image-20200615222708851](D:\km_py\markdown_note\saas_day09.assets\image-20200615222708851.png)

![image-20200615222915999](D:\km_py\markdown_note\saas_day09.assets\image-20200615222915999.png)

#### 4.3 Python实现上传文件

```
pip install -U cos-python-sdk-v5
```

```python
# -*- coding=utf-8
# appid 已在配置中移除,请在参数 Bucket 中带上 appid。Bucket 由 BucketName-APPID 组成
# 1. 设置用户配置, 包括 secretId，secretKey 以及 Region
from qcloud_cos import CosConfig
from qcloud_cos import CosS3Client
import sys
import logging


secret_id = 'COS_SECRETID'      # 替换为用户的 secretId
secret_key = 'COS_SECRETKEY'      # 替换为用户的 secretKey
region = 'COS_REGION'     # 替换为用户的 Region

token = None                # 使用临时密钥需要传入 Token，默认为空，可不填
scheme = 'https'            # 指定使用 http/https 协议来访问 COS，默认为 https，可不填
config = CosConfig(Region=region, SecretId=secret_id, SecretKey=secret_key, Token=token, Scheme=scheme)
# 2. 获取客户端对象
client = CosS3Client(config)
# 参照下文的描述。或者参照 Demo 程序，详见 https://github.com/tencentyun/cos-python-sdk-v5/blob/master/qcloud_cos/demo.py
```

```python
# 创建桶
response = client.create_bucket(
    Bucket='examplebucket-1250000000'
)
```

```python
# 上传文件
response = client.upload_file(
    Bucket='examplebucket-1250000000',
    LocalFilePath='local.txt', # 本地文件的路径
    Key='picture.jpg', # 上传到桶之后的文件名
    PartSize=1,
    MAXThread=10,
    EnableMD5=False
)
print(response['ETag'])
```

上传文件示例代码

```
from qcloud_cos import CosConfig
from qcloud_cos import CosS3Client
import sys


secret_id = 'AKIDte7KM9N3CAsITMePaUFn44nENFs0YWsx'      # 替换为用户的 secretId
secret_key = 'FExOYS9bqraBVkj12mIjuqZ1C7l9IAkx'      # 替换为用户的 secretKey
region = 'ap-nanjing'     # 替换为用户的 Region


config = CosConfig(Region=region, SecretId=secret_id, SecretKey=secret_key)

client = CosS3Client(config)

# 上传文件
response = client.upload_file(
    Bucket='km-1302167637',
    LocalFilePath='test.png', # 本地文件的路径
    Key='p1.jpg', # 上传到桶之后的文件名
)
print(response['ETag'])
```



创建桶示例代码

```python
from qcloud_cos import CosConfig
from qcloud_cos import CosS3Client


secret_id = 'AKIDte7KM9N3CAsITMePaUFn44nENFs0YWsx'      # 替换为用户的 secretId
secret_key = 'FExOYS9bqraBVkj12mIjuqZ1C7l9IAkx'      # 替换为用户的 secretKey
region = 'ap-nanjing'     # 替换为用户的 Region


config = CosConfig(Region=region, SecretId=secret_id, SecretKey=secret_key)

client = CosS3Client(config)


response = client.create_bucket(
    Bucket='test-1302167637',
    ACL='public-read'# private/public-read/public-read-write
)
```

### 5 项目中集成cos

项目中的图片可以放在cos中，防止我们的服务器压力过大。

5.1 创建项目时 创建一个项目的桶

```python
from qcloud_cos import CosConfig
from qcloud_cos import CosS3Client
from django.conf import settings

def create_bucket(bucket, region='ap-chengdu'):
    """
    创建桶
    :param bucket: 桶名称
    :param region: 桶区域
    :return:
    """
    secret_id = settings.TENCENT_SECRET_ID  # 替换为用户的 secretId
    secret_key = settings.TENCENT_SECRET_KEY  # 替换为用户的 secretKey
    region = region  # 替换为用户的 Region

    config = CosConfig(Region=region, SecretId=secret_id, SecretKey=secret_key)

    client = CosS3Client(config)

    response = client.create_bucket(
        Bucket=bucket,
        ACL='public-read'  # private/public-read/public-read-write
    )

```

5.2 markdown上传图片到cos

- cos上传图片：本地文件，接收markdown上传的文件再进行上传到cos

  ```python
  @csrf_exempt
  def wiki_upload(request, project_id):
      """
      markdown上传图片
      :param request:
      :return:
      """
      result = {
          'success': 0,
          'message': None,
          'url': None,
      }
      image_object = request.FILES.get('editormd-image-file')
      if not image_object:
          result['message'] ='文件不存在'
          return JsonResponse(result)
      ext = image_object.name.rsplit('.')[-1]
      key = '{}.{}'.format(uid(request.tracer.user.mobile_phone), ext)
      image_url = upload_file(
          request.tracer.project.bucket,
          request.tracer.project.region,
          image_object,
          key
      )
      # markdown固定格式
      result['success'] =1
      result['url'] = image_url
      return JsonResponse(result)
  ```

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

  

- markdown上传图片

  ```js
          var WIKI_UPLOAD_URL = "{% url 'wiki_upload' project_id=request.tracer.project.id %}";
          $(function () {
              initEditorMd();
          });
          /*
          初始化markdown编辑器（textarea转化为编辑器）
           */
          function initEditorMd() {
              editormd('editor',{
                  placeholder:'请输入内容',
                  height:500,
                  path:"{% static 'plugin/editor-md/lib/' %}",
                  imageUpload:true,//本地上传功能
                  imageFormats:['jpg','jpeg','png','gif'],//支持的格式
                  imageUploadURL:WIKI_UPLOAD_URL,// 上传的路径
              })
          }
  ```

  