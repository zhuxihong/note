创建项目： django-admin startproject 项目名称

启动：manage.py runserver

Django是一个MTV模式的框架

url.py 分配任务

views.py 业务逻辑

models.py 数据库操作

templates.py 渲染数据

MTV模式

传统三层架构：

ui 用户界面层

bll 业务逻辑层

dal 数据访问层

MVC模式：

controll 分配任务

models  业务逻辑+数据库

views  数据渲染和显示



### url:

urlpatterns数组保存请求路由,和响应的方法

```
urlpatterns = [
    # 保存url与视图函数的对应关系
    # http://127.0.0.1:8000/page/2003
    # 参数1：path,用户发的请求
    # 参数2：视图函数，服务器对请求的处理
    path('page/2003',views.page_2003.ceshi),
    //普通匹配
    path('page/<int:num>', admin.site.urls),
    //精确匹配
    re_path(r'^birthday/(?P<ri>\d[0-31]{1,2})/(?P<yue>\d[1-12]{1,2})/(?P<nian>\d{4})$',views.page_2003.birthday)
]
path:
语法：<转换器类型：变量名称>   path('page/<int:num>', admin.site.urls，别名),
re_path('(?P<变量名称>正则)')，方法，别名)   需要导包
```

### views 模块：

views需自己创建

```python
from django.http import HttpResponse
views内参数必须包含请求对象request
返回必须是响应对象：HttpResponse

urls内使用path转换器，函数需要多接收一个函数。
```

请求：

get 获取资源

post 创建资源

put 修改资源

delete 删除资源

响应：

永久重定向：301响应头**Location**内记录的新域名。响应类 HttpResponseRedirect()

临时重定向：302



get请求：

产生：输入链接    A 标签  通常form表单提交是post请求 更改属性method=“get”



------

HttpRequest对象：

path_info:URL字符串

method：http请求方法

### **GET 查询字符串传参**

```
http://127.0.0.1:8000/index?name=zhuxihong,123
//避免强硬获取。
Request.GET.get(键,默认值)
```

get_full_pash:获取完整地址（网址后面的所有）

用户通过浏览器向服务器提交数据

1 path转换器

2 查询字符串

requst.GET.get(name)

3 服务器获取表单元素的值

requst.POST.get(name)

### templates 模板加载响应

根据字典数据动态变化的html网页

1.创建模板文件夹templates

2.配置:TEMPLATES下DIRS= [os.path.join(BASE_DIR,'tmplate')]

3.调用loader.get_template('模板名')加载成字符串：对象.render(字典数据)   将动态的字典数据结合模板数据。

需要导包：rom django.template import loader

4.响应给客户端

导包：from django.shortcuts import render

**推荐直接加载并响应：**render（request，模板名称，字典）

模板动态数据变量：

需要使用动态数据的{{ name }} 两边留空格，

{{  列表 .下标 }}  

{{  字典 .键  }}  

{{  对象 .方法  }}  

{{ 函数 }}

locals封装字典，会把函数内所有变量封装成字典

### 避免存储型XSS：

1.避免Xss的有效方式是对数据做转义,使用html模块escape()转义    恢复：unescape()

### 模板标签：

在模板中使用业务逻辑

语法：{% 标签 %} .....{% 结束标签 %}   括号无法使用，格式要求严格！

```django
    {% if amout > 30 %}
        <h3>这是个大班级</h3>
    {% else %} 
        <h3>这是个小班级</h3>
    {% endif %} 
    
    {% for i in generals %}
    <p>{{ forloop.counter }}{{ i }}</p>  
    {% empty %}
    <p>是非成败转头看</p>
    {% {% endfor %} %}
```

过滤器：{{变量|过滤器：参数1|过滤器2:参数2}}

upper  转换成大写

lower 转换小写

safe 默认不对变量中的字符串进行转义

add：“N”  将value的值增加N

truncatechars:“N”  截断，超过设置长度的字符串以...显示

### 模板的继承

父模板的内容复用，并可以覆盖父模板中相应的块。动态数据无法被继承

定义父模板：

{% block block_name %} {% endblock %}   包裹子模板可能需要重写的地方 

继承模板：

{% extends '父模板文件' %} 

重写：{% block 块名称 %}更改内容 {% endblock %}   

### url反向解析

给path的url起别名，在使用url的地方，使用别名。类似于变量名  {% url '别名'}  ,在视图或者前端页面中去使用。方便后续修改。

**好处**：如果url发生变化前端页面不需要去修改，可以后端重定向。如果重名，找参数最匹配的。都一致的话找最后一个。

视图中使用 reverse（名称，args=[值]）

导包：from django.urls import reverse

### 静态文件

不能和服务器做动态交互的文件都是静态文件。settings.py内设置2项内容：

```django
/访问路径
STATIC_URL = '/static/'

/文件所在位置
STATICFILES_DIRS=(
    os.path.join(BASE_DIR,'static'),
)
```

相对定位：/static/img/1.jpeg"

绝对定位：http://127.0.0.1:8000//static/img/1.jpeg

### CDN内容分发网络

如果静态文件太多在同一个服务器发出的话会造成响应速度慢。

### 应用APP

应用在django中是一个独立的业务模块。

创建应用：

python3 manage.py startapp music

注册：

INSTALLED_APPS 列表下添加app名称

migrations 保存数据迁移的中间件

init   应用子包的初始化文件

admin 应用后台管理配置文件

apps 应用的配置文件

models 与数据库相关的映射类文件

tests 应用的单元测试文件

views 视图处理函数文件

### 主路由分发

//分布式路由的路由分发

//http://127.0.0.1:8000/路由名称/XXXX

path('user/',include('user.urls'))

path('路由',include('路由.模块'))

### APP路由：

在app文件下创建urls文件，复制主路由   templates文件夹 文件夹下创建与应用同名文件放置模板

当页面有重复是在templates下创建与应用名同名的文件夹。即可解决

### 模型层：

