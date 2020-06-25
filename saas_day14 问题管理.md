# day14 问题管理

## 今日概要

- 添加问题
- 问题列表展示+分页
- 编辑问题
  - 回复
  - 变更

```
$('#addSubmit').click(function () {
            $.ajax({
                url: "{% url 'issues_add' %}",
                data: $('#addForm').serialize(),
                dataType:'JSON',
                type:'post',
                success:function (res) {
                    console.log(res)
                }
            })
        })
```



## 今日详情

### 1 添加问题

#### 1.1 数据初始化和合法性

#### 1.2 添加数据（成功之后页面刷新）

#### 1.3 错误提示

#### 1.4 扩展（可选）

```
bootstrap-select
下拉框渲染（自定义插件）
```

### 2 问题列表的展示

#### 2.1 问题列表

#### 2.2 分页

```
http://127.0.0.1:8000/manage/8/issues/?page=1
- 数据库获取数据
	models.user.objects.all()[0:10]
	models.user.objects.all()[10:20]
- 显示页码
	- 点击当前显示的页码
	- 显示11个页码（前5个，后5个）
	
```

### 3 编辑问题

#### 3.1 编辑页面展示

### 3.2 问题讨论（回复嵌套）



| id   | 内容 | 类型     | 评论者 | 时间 | 自己FK | 问题FK |      |      |      |
| ---- | ---- | -------- | ------ | ---- | ------ | ------ | ---- | ---- | ---- |
|      |      | 回复     |        |      |        |        |      |      |      |
|      |      | 修改记录 |        |      |        |        |      |      |      |
|      |      |          |        |      |        |        |      |      |      |

```python
class IssuesReply(models.Model):
    """ 问题回复 """
    reply_type_choices = (
        (1,'修改记录'),
        (2,'回复'),
    )
    reply_type = models.SmallIntegerField(verbose_name='类型', choices=reply_type_choices)
    issues = models.ForeignKey(verbose_name='问题', to='Issues')
    content = models.TextField(verbose_name='描述')
    creator = models.ForeignKey(verbose_name='创建者', to='UserInfo', related_name='create_reply')
    create_datetime = models.DateTimeField(auto_now_add=True, verbose_name='创建时间')
    reply = models.ForeignKey(verbose_name='回复', to='self', null=True, blank=True)
```



#### 3.2.1 ajax请求获取所有评论

- 获取评论
- js嵌套展示

3.2.2 评论&回复

- 回复
- 评论











