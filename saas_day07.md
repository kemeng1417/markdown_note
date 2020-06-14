# day07

## 今日概要

- 展示项目
- 星标项目
- 添加项目：颜色选择
- 项目切换&项目的菜单处置
- wiki管理



## 今日详情

### 1 展示项目

#### 1.1数据

```python
1.从数据库中获取三部分的数据
	我创建的项目：已星标、未星标
	我参与的所有项目：已星标、未星标
2.提取已星标
 	列表= 循环我创建的+我参与的，把所有星标的项目提取出来
得到三个列表：星标，创建，参与	
```

#### 1.2样式

```css
.panel-body {
            padding: 0;
            display: flex;
            flex-direction: row;
            justify-content: left;
            align-items: flex-start;
            flex-wrap: wrap;
        }

        .panel-body .item {
            border-radius: 6px;
            width: 228px;
            border: 1px solid #dddddd;
            margin: 20px 10px;
        }

        .panel-body .item:hover {
            border: 1px solid #f0ad4e;
        }

        .panel-body .item .title {
            height: 104px;
            color: white;
            display: flex;
            justify-content: center;
            align-items: center;
            border-top-left-radius: 6px;
            border-bottom-right-radius: 6px;
            font-size: 15px;
            text-decoration: none;
        }

        .panel-body .item .info {
            padding: 10px 10px;
            display: flex;
            justify-content: space-between;
            border-bottom-left-radius: 6px;
            border-bottom-right-radius: 6px;
            color: #8c8c8c;
        }

        .panel-body .item .info a {
            text-decoration: none;
        }

        .panel-body .item .info .fa-star {
            font-size: 18px;
        }

```

### 2 星标项目（去除星标）

#### 2.1 星标

```
我创建的项目：project的star=True
我参与的项目：projectuser的star=True
```

#### 2.2 移除星标

```
我创建的项目：project的star=False
我参与的项目：projectuser的star=False
```

### 3 选择颜色

#### 3.1 部分样式应用bootstrap

```python
class BootStrapForm(object):
    """ 写一个类,以便后面的模板继承 """

    bootstrap_class_exclude = []
    # 重写父类方法，加上class
    def __init__(self, *args, **kwargs):
        super().__init__(*args, **kwargs)

        for name, field in self.fields.items():
            if name in self.bootstrap_class_exclude:
                continue
            field.widget.attrs['class'] = 'form-control'
            field.widget.attrs['placeholder'] = '请输入{}'.format(field.label)
```

```python
class ProjectModelForm(BootStrapForm, forms.ModelForm):
    bootstrap_class_exclude = ['color']
    # desc = forms.CharField(widget=forms.Textarea())
    class Meta:
        model = models.ProjectInfo
        fields = ['name', 'color', 'desc']
        widgets = {
            'desc': forms.Textarea,
            'color':ColorRadioSelect(attrs={'class':'color-radio'}),
            # 可以添加属性 attr={xx=xxx}
        }

```

#### 3.2 定制ModelForm的插件

```python
class ProjectModelForm(BootStrapForm, forms.ModelForm):
    bootstrap_class_exclude = ['color']
    # desc = forms.CharField(widget=forms.Textarea())
    class Meta:
        model = models.ProjectInfo
        fields = ['name', 'color', 'desc']
        widgets = {
            'desc': forms.Textarea,
            'color':ColorRadioSelect(attrs={'class':'color-radio'}),
            # 可以添加属性 attr={xx=xxx}
        }
```

```python
from django.forms import RadioSelect


class ColorRadioSelect(RadioSelect):
    template_name = 'widgets/color_radio/radio.html'
    option_template_name = 'widgets/color_radio/radio_option.html'
```

```html
{% with id=widget.attrs.id %}
    <div{% if id %} id="{{ id }}"{% endif %}{% if widget.attrs.class %} class="{{ widget.attrs.class }}"{% endif %}>
        {% for group, options, index in widget.optgroups %}
            {% for option in options %}
                <label {% if option.attrs.id %} for="{{ option.attrs.id }}"{% endif %} >
                    {% include option.template_name with widget=option %}
                </label>
            {% endfor %}
        {% endfor %}
    </div>
{% endwith %}

```

```html
{% include "django/forms/widgets/input.html" %}
<span class="cycle" style="background-color:{{ option.label }}"></span>
```

#### 3.3 项目选择颜色

##### 3.1 3.2知识点的应用+前端样式的编写



### 4 切换菜单

```
1.数据库中获取
	我创建的
	我参与的
2.循环希纳是
3.当前页面需要，其他页面也需要 用inclusion实现
```

### 5 项目管理

```
/manage/项目id/
```

#### 5.1 进入项目展示菜单

```
进入项目
展示菜单
```

#### 5.1.1 是否进入项目【中间件中判断】

判断url是否以manage开头的

project_id是否是我创建或者我参与的

#### 5.1.2 展示菜单

```django
{% if request.tracer.project %}
                <ul class="nav navbar-nav">
                    <li><a href="#">产品功能</a></li>
                    <li><a href="#">企业方案</a></li>
                    <li><a href="#">帮助文档</a></li>
                    <li><a href="#">价格</a></li>
                </ul>
            {% endif %}
```

#### 5.1.3 默认选择菜单

```python
@register.inclusion_tag(('inclusion/manage_menu_list.html'))
def manage_menu_list(request):
    data_list = [
        {'title': '概览', 'url': reverse('dashboard', kwargs={'project_id': request.tracer.project.id})},
        {'title': '问题', 'url': reverse('issues', kwargs={'project_id': request.tracer.project.id})},
        {'title': '统计', 'url': reverse('statistics', kwargs={'project_id': request.tracer.project.id})},
        {'title': '文件', 'url': reverse('file', kwargs={'project_id': request.tracer.project.id})},
        {'title': 'wiki', 'url': reverse('wiki', kwargs={'project_id': request.tracer.project.id})},
        {'title': '设置', 'url': reverse('setting', kwargs={'project_id': request.tracer.project.id})},
    ]

    for item in data_list:
        # 用户访问的url
        # 当前循环的url
        if request.path_info.startswith(item['url']):
            item['class'] = 'active'
    return {'data_list': data_list}
```

## 总结

### 项目实现的思路

### 星标/取消星标

### inclusion_tag实现项目切换

### 项目菜单

- 中间件 process_view
- 默认选中：inclusion_tag
- 路由分发
  - include('xxx.url')
  - include([sss,sss])

### 颜色选择:源码+扩展【实现】