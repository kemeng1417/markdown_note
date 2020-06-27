# day17



## 今日概要

- 统计报表

  支付



## 今日详细

### 1 django时区

```python
# datetime.datetime.now() 获取到的是UTC时间/ datetime.datetime.UTCnow()
# TIME_ZONE = 'UTC'

# 获取到的是东八区的时间
TIME_ZONE = 'Asia/Shanghai'

USE_I18N = True

USE_L10N = True


# 影响自动生成数据库时间字段
# USE_TZ=True 创建UTC时间写入数据库
# USE_TZ=False 根据TIME_ZONE设置的时区写入数据库
# USE_TZ = True
USE_TZ = False

```

### 2 bug



### 3 画图

网页上画图：HighCharts/Echarts

#### 3.1 下载

https://www.highcharts.com.cn/download

#### 3.2 应用

```html
<script src="http://cdn.highcharts.com.cn/highcharts/8.1.2/highcharts.js"></script>
```

#### 3.3 中文包

- 提供js中文包文件
- 不提供

#### 总结：

- 下载并引用

- 应用：

  - 引入js
  - 定义div
  - js进行配置

- 需求：根据demo中变化+api

- 注意事项：series

  - 生成单条的图

    ```
    series:[
    	{
    		data:[]
    	}
    ]
    ```

    

  - 生成多个数据

    ```
    series:[
    	{
    		name:'',
    		data:[]
    	},
    	{
    		name:'',
    		data:[]
    	},
    	{
    		name:'',
    		data:[]
    	},
    ]
    ```

### 4 统计

#### 4.1 daterangepicker

注意js的引入数据

```html
<script src="{% static 'plugin/daterangepicker/moment.min.js' %}"></script>
    <script src="{% static 'plugin/daterangepicker/daterangepicker.js' %}"></script>
```



#### 4.1 饼图



#### 4.2 柱状图