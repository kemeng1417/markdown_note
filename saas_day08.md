# day08 wiki

## 今日概要

- 表结构设计
- 快速开发
- 应用markdown组件
- 腾讯cos做上传



## 今日详细

### 1 表结构设计

| id   | 标题 | 内容    | 项目id | 父id |
| ---- | ---- | ------- | ------ | ---- |
| 1    | h1   | content | 1      | null |
| 2    | h2   | content | 1      |      |
| 3    | h3   | content | 2      |      |

```python
class Wiki(models.Model):
    project = models.ForeignKey(verbose_name='项目', to='ProjectInfo')
    title = models.CharField(verbose_name='标题', max_length=32)
    content = models.TextField(verbose_name='内容')

    # 自关联,使用反向查找,添加反向关联查找的字段
    parent = models.ForeignKey(verbose_name='父文章', to='Wiki', null=True, blank=True, related_name='children')
```

### 2 快速开发

#### 2.1 wiki首页展示

- 首页

- 多级目录

  ```
  模板渲染
  构造多级列表
  页面显示，循环显示
  	递归
  缺点：
  	写代码费劲
  	效率低
  ```

  ```python
  后端+前端完成ajax+id选择器
  	-前端 打开页面之后，发送ajax请求获取所有的文档标题信息
  	-后台 获取所有的文章信息
  		queryset = models.Wiki.object.filter(project_id=2).values_list('id','title','parent_id')
  		[
  			{'id':1, title:'x',parent_id:None},
  			{'id':2, title:'xx',parent_id:None},
  			{'id':3, title:'xxx',parent_id:None},
  			{'id':4, title:'xxxx',parent_id:3},
  			{'id':5, title:'xxxxx',parent_id:1},
  		]
          直接返回给前端的ajax
          - ajax的回调函数success中获取res.data， 并循环
          $.each(res.data,function(index,item){
              if(item.parent_id){
                  
              }else{
                  
              }
          })
  ```

  多级目录展示部分存在两个bug

  - 父目录要提前出现：排序+字段（深度）
  - 点击目录查看文章

#### 2.2 添加wiki文章

- bug：文章展示当前用户的文章 modelform

#### 2.3 预览文章

#### 2.4 编辑修改文章

#### 2.5 删除文章