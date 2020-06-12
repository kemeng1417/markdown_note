# day04

## 内容回顾

- 项目规则

  - 创建项目：静态、视图、路由

- ajax

  ```js
  $.ajax({
  	url:'/sss/',
  	type:'GET',
  	data:{},
  	dataType:'JSON',
  	success:function(res){
  	
  	}
  })
  ```

- ModelForm/Form中想使用视图中数据，例如request

  ```javascript
  重写init方法
  ```

  

## 今日概要



- 点击注册
- 用户登录
  - 短信验证码登录
  - 手机or邮箱/密码登录
- 项目管理（创建/星标）

## 今日详情

### 1.点击注册

#### 1.1 点击手机数据&ajax

```javascript
function bindClickSubmit() {
    $('#btnSubmit').click(function () {
        // 手机表单中的数据(找到每一个数据) $('#regForm').serialize()
        // 所有字段数据+csrf_token
        // 通过ajax发送到后台
        $.ajax({
            url: "{% url 'register' %}",
            type: "POST",
            data: $('#regForm').serialize(),
            dataType: "JSON",
            success: function (res) {
                console.log(res)
            }
        })
    })
}
```

#### 1.2 数据校验

#### 1.3 写入数据库

```
pip3 install pillow
```

基本使用

```python
def check_code(request):
    #Django imag标签src属性导入图片的原理
    f=open('static/imgs/1.jpg','rb')    #注意：static/imgs/1.jpg不能写成 /static/imgs/1.jpg
    data=f.read()
    f.close()
    return HttpResponse(data)
```

写入本地并读取到内存中

```python
def check_code(request):
    #创建code.png写入到本地目录下
    from PIL import Image
    img=Image.new(mode='RGB',size=(120,30),color=(255,255,255)) #默认写入白板，随后在白板上写入字符串等
    f=open('code.png','wb')
    img.save(f,'png')   #png为图片后缀
    f.close()
    #将本地图片code.png读取到内存并使网页图片能显示出来
    f=open('code.png','rb')
    data=f.read()
    f.close()
    return HttpResponse(data)
```

以上操作比较麻烦，需要写入本地又读取到内存中

**优化：直接在内存中开辟空间，在内存中进行读写等操作**

```python
def check_code(request):   
    from io import BytesIO
    #写入内存中
    f=BytesIO()
    img=Image.new(mode='RGB',size=(120,30),color=(255,255,255))
    img.save(f,'png')
    #从内存中读出来
    data=f.getvalue()
    return HttpResponse(data)
```

**1. 创建图片**

```python
from PIL import Image
img = Image.new(mode='RGB', size=(120, 30), color=(255, 255, 255))
  
# 在图片查看器中打开
# img.show()
  
# 保存在本地
with open('code.png','wb') as f:
    img.save(f,format='png')
```

**2. 创建画笔，用于在图片上画任意内容**

```python
img = Image.new(mode='RGB', size=(120, 30), color=(255, 255, 255))
draw = ImageDraw.Draw(img, mode='RGB')
```

**3. 画点**

```python
img = Image.new(mode='RGB', size=(120, 30), color=(255, 255, 255))
draw = ImageDraw.Draw(img, mode='RGB')
# 第一个参数：表示坐标
# 第二个参数：表示颜色
draw.point([100, 100], fill="red")
draw.point([300, 300], fill=(255, 255, 255))
```

**4. 画线**

```python
img = Image.new(mode='RGB', size=(120, 30), color=(255, 255, 255))
draw = ImageDraw.Draw(img, mode='RGB')
# 第一个参数：表示起始坐标和结束坐标
# 第二个参数：表示颜色
draw.line((100,100,100,300), fill='red')
draw.line((100,100,300,100), fill=(255, 255, 255))
```

**5. 画圆**

```python
img = Image.new(mode='RGB', size=(120, 30), color=(255, 255, 255))
draw = ImageDraw.Draw(img, mode='RGB')
# 第一个参数：表示起始坐标和结束坐标（圆要画在其中间）
# 第二个参数：表示开始角度
# 第三个参数：表示结束角度
# 第四个参数：表示颜色
draw.arc((100,100,300,300),0,90,fill="red")
```

**6. 写文本**

```python
img = Image.new(mode='RGB', size=(120, 30), color=(255, 255, 255))
draw = ImageDraw.Draw(img, mode='RGB')
# 第一个参数：表示起始坐标
# 第二个参数：表示写入内容
# 第三个参数：表示颜色
draw.text([0,0],'python',"red")
```

**7. 特殊字体文字**

```python
img = Image.new(mode='RGB', size=(120, 30), color=(255, 255, 255))
draw = ImageDraw.Draw(img, mode='RGB')
# 第一个参数：表示字体文件路径
# 第二个参数：表示字体大小
font = ImageFont.truetype("kumo.ttf", 28)
# 第一个参数：表示起始坐标
# 第二个参数：表示写入内容
# 第三个参数：表示颜色
# 第四个参数：表示颜色
draw.text([0, 0], 'python', "red", font=font)
```

# 图片验证码

```python
import random
  
def check_code(width=120, height=30, char_length=5, font_file='kumo.ttf', font_size=28):
    code = []
    img = Image.new(mode='RGB', size=(width, height), color=(255, 255, 255))
    draw = ImageDraw.Draw(img, mode='RGB')
  
    def rndChar():
        """
        生成随机字母  
        :return:
        """
        return chr(random.randint(65, 90))
  
    def rndColor():
        """
        生成随机颜色
        :return:
        """
        return (random.randint(0, 255), random.randint(10, 255), random.randint(64, 255))
  
    # 写文字
    font = ImageFont.truetype(font_file, font_size)
    for i in range(char_length):
        char = rndChar()
        code.append(char)
        h = random.randint(0, 4)
        draw.text([i * width / char_length, h], char, font=font, fill=rndColor())
  
    # 写干扰点
    for i in range(40):
        draw.point([random.randint(0, width), random.randint(0, height)], fill=rndColor())
  
    # 写干扰圆圈
    for i in range(40):
        draw.point([random.randint(0, width), random.randint(0, height)], fill=rndColor())
        x = random.randint(0, width)
        y = random.randint(0, height)
        draw.arc((x, y, x + 4, y + 4), 0, 90, fill=rndColor())
  
    # 画干扰线
    for i in range(5):
        x1 = random.randint(0, width)
        y1 = random.randint(0, height)
        x2 = random.randint(0, width)
        y2 = random.randint(0, height)
  
        draw.line((x1, y1, x2, y2), fill=rndColor())
  
    img = img.filter(ImageFilter.EDGE_ENHANCE_MORE)
    return img,''.join(code)
  
  
if __name__ == '__main__':
    # 1. 直接打开
    # img,code = check_code()
    # img.show()
  
    # 2. 写入文件
    # img,code = check_code()
    # with open('code.png','wb') as f:
    #     img.save(f,format='png')
  
    # 3. 写入内存(Python3)
    # from io import BytesIO
    # stream = BytesIO()
    # img.save(stream, 'png')
    # stream.getvalue()
  
    # 4. 写入内存（Python2）
    # import StringIO
    # stream = StringIO.StringIO()
    # img.save(stream, 'png')
    # stream.getvalue()
```



### 2.短信登录

#### 2.1 展示页面

#### 2.2 点击发送短信

#### 2.3 点击登录



### 3.用户名/密码登录

3.1 python生成图片+写文字



3.2 session&cookie

![image-20200601025127463](C:\Users\km\AppData\Roaming\Typora\typora-user-images\image-20200601025127463.png)

3.3 页面显示

3.4 登录

# 总结（一期项目）

- 项目代码
- 思维导图（知识点）
- 