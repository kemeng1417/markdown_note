## 内容回顾

登录

注册

```python 
# models.py
class User(models.Model):
    """
    员工信息表用户、密码、职位、公司名（子、总公司）、手机、最后一次登录时间。
    """
    username = models.CharField(max_length=32, verbose_name='用户名',unique=True)
    password = models.CharField(max_length=32, verbose_name='密码')
    position = models.CharField(max_length=32, verbose_name='职位')
    company = models.CharField(max_length=32, verbose_name='公司',
                               choices=(('0', '北京总公司'), ('1', '石家庄分公司'), ('2', '广州分公司')))
    phone = models.CharField(max_length=11, verbose_name='手机号')
    last_time = models.DateTimeField(null=True, blank=True, verbose_name='上次登陆时间')
    create_time = models.DateTimeField(auto_now_add=True, verbose_name='注册时间')

```

form

```python
from django import forms
from app01 import models

# form 
class RegForm(forms.Form):
	username = forms.Charfield(label='用户名')
    password = forms.Charfield(widget=forms.PasswordInput)

# modelform
class RegForm(forms.ModelForm):
    
    password = forms.CharField(widget=forms.PasswordInput, label='密码',min_length=6)
    re_pwd = forms.CharField(widget=forms.PasswordInput, label='确认密码', min_length=6)
    
    class Meta:
        model = models.User
        fields =  '__all__'  # ['username','password']
        exclude = ['last_time']
        
        labels = {
            'username':'用户名'
        }
        
        widgets = {
            'password':forms.PasswordInput
            
        }
        error_messages = {
            'username':{}
        }
    
    
    def clean_password(self):
        password = self.cleaned_data.get('password')
        # 通过校验   返回该字段的值
        # 不通过校验 抛出异常ValidationError
    
    def clean(self):
        self._validate_unique = True # 校验数据的唯一性
        # 通过校验   返回所有字段的值 self.cleaned_data
        # 不通过校验 抛出异常ValidationError
        password = self.cleaned_data.get('password')
        re_pwd = self.cleaned_data.get('re_pwd')
        if re_pwd == password:
            return self.cleaned_data
        self.add_error('re_pwd','两次密码不一致')
     	raise ValidationError('两次密码不一致')   # '__all__'
        
        
def reg(request,*args,**kwargs):
    form_obj = RegForm()
    if request.method == 'POST':
        form_obj = RegForm(request.POST)  # 包含用户提交的数据
        if form_obj.is_valid():   # 对数据进行校验
            form_obj.save()       # 新增上数据
            return redirect('login')
    return render(request,'reg.html',{'form_obj':form_obj})


# html


{{  form_obj.username }}     input框

{{  form_obj.username.label }}    label提示

{{  form_obj.username.id_for_label }}       input框的id   

{{  form_obj.username.errors.0 }} 

```

展示数据

给模板一个queryset对象列表，循环出对象obj

1. 普通字段

   obj.字段名 --> 数据库中的数据

2. 外键

   obj.外键  --> 外键的对象 给类定义`__str__`的方法

3. 带choices参数的

   obj.字段名 --> 数据库中的数据 这个显示不友好

   `obj.get_字段名__diaplay()` 显示友好的结果  在模板中不用括号

4. 自定义方法

   ```python 
   def show_publish_status(self):
       show_dic = {
           True: 'green',
           False: '#c34a0c',
       }
       return mark_safe(
           '<span style="background:{};color:white;padding:3px">{}</span>'.format(show_dic[self.publish_status],
                                                                                  self.get_publish_status_display()))
   ```

   用户上传头像

   ```python
   avatar = models.ImageField(upload_to='img/avatar', default='img/avatar/default.jpg')
   # 需要pillow  pip install pillow
   ```

   配置 settings

   ```python
   MEDIA_URL = '/media/'
   MEDIA_ROOT = os.path.join(BASE_DIR, 'media')
   ```

   配置urls

   ```python
   from django.views.static import serve
   from django.conf import settings
   
   urlpatterns = [
       url(r'^admin/', admin.site.urls),
       url(r'', include('app01.urls')),
       url(r'^media/(?P<path>.*)$', serve, {'document_root':settings.MEDIA_ROOT}),
   ]
   
   # 路径必须以media开头
   ```

   request的querydict对象

```python
from django.http.request import QueryDict

request.GET._mutable = True
request.GET['page'] = 1

request.GET.copy()  # 返回一个可编辑的深拷贝

request.GET.urlencode()  # page=1&aa=111
```

