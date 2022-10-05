# DJANGO笔记

### django使用的模式：

django是MTV模式的框架，他分为业务路由层-逻辑层—数据访问层—渲染层。

MVC模式：路由层—视图层（包含业务逻辑+渲染层）—模型层 ，

传统三层架构：ui 用户界面层 —业务逻辑层—数据访问层

### django目录下文件：

```
settings  配置文件
urls 路由模块
models 数据模型
views 视图模型

migrations 保存数据迁移的中间件
init   应用子包的初始化文件
admin 应用后台管理配置文件
apps 应用的配置文件
models 与数据库相关的映射类文件
tests 应用的单元测试文件
views 视图处理函数文件
```

### django常用命令

**创建项目：**

```
django-admin startproject 项目名称
```

**创建APP**：

```
在django主目录下能显示（manage.py）下输入
python3 manage.py startapp app名称
```

**运行项目**：

```
python3 manage.py runserver
```

**代码测试**：

```
python3 manage.py shell
```

**生成数据库迁移文件**：

```
python3 manage.py makemigrations  
-执行后会app内生成一个迁移文件
```

**生成数据库表**

```
python3 manage.py migrate
```

### urls路由模块：

路由模块下urlpatterns数组保存请求path转换器路由匹配与视图函数的对应关系。可以接收来自HTTP的请求（GET,POST.....）

path转换器的使用：

```python
如url：http://127.0.0.1:8000/page/
路由设置：path（‘page/’，viws.pageviws）

@普通匹配：
1. path（‘page/<str:username>’，viws.pageviws）
-http://127.0.0.1:8000/page/zhangsan
2. path（‘page/<int:userid>’，viws.pageviws）
-http://127.0.0.1:8000/page/123
语法：<转换器类型：变量名>
注：使用past转换器需在视图函数内形参中增加变量接收

@正则匹配：（需要导包）
1. re_path('^page/（?P<ri>正则）/')

@分布式路由：
导包：from django.urls import include
-http://127.0.0.1:8000/login/
path（'login/',include('app名称.urls')）
```

path第三个参数：

path转换器第三个参数是路由别名，设置别名后可在渲染时采用别名。在前端页面需要更改路由名称时，前端页面就不需要跟着去修改，链接会跟随路由名称。（前后端不分离情况下）

```python
path（‘路由’，视图函数，别名）
```

**同一个路由处理多种请求：**

在一个路由内即需要处理GET又处理POST等请求时，可以使用视图类的方法as_view方法实现同一个路由处理多种请求。

使用方法：

```python
path('result/',views.Resultview.as_view())
```

### views 视图模块：

较为常用的包：

```python
from django.shortcuts import render    （返回渲染后的html文件）
from django.views import View       （用户继承view）
import json		（处理json数据）
from django.http import JsonResponse,HttpResponse 
（返回json格式数据，直接返回数据）
from django.conf import settings （导入配置的参数）
```

在视图函数中第一个参数必须为request，当有path转换器传参时也必须增加参数接收。当一个视图函数需要处理多种请求是使用视图类会更便于维护。

```python
class ResultView(View):
	def get(self,request):
		#业务逻辑
		return JsonResponse({'code':200})
	def post(self,request)：
		#业务逻辑
		return JsonResponse({'code':200})
```

### **request对象：**

**path_info** 返回用户访问url，不包括域名

```python
url：http://127.0.0.1:8000/index?name=zhansan
print(request.path_info)
结果：
#/index
```

**method** 请求中使用的HTTP方法的字符串表示，全大写表示。

```python
print（request.method）
##‘post’或‘get’
```

**GET** 包含所有HTTP GET参数的类字典对象

```python
url：http://127.0.0.1:8000/index?name=zhansan

get请求：

软获取：
request.GET.get(‘name’，缺省值)   ---zhansan

硬获取：
request.GET['name']
硬获取如果键不存在会报错。

keys方法:
url：http://127.0.0.1:8000/index?name=zhansan&age=18
request.GET.keys
# name,age

```

**POST** 包含所有HTTP POST参数的类字典对象

```python
[{'username':zhangsan,'age':18}]
post请求：
request.POST.get(username，缺省值)   --zhansan
```

**body** 请求体，byte类型 request.POST的数据就是从body里面提取到的

```python
b[{'username':zhangsan,'age':18}]
```

**MATE** 包含HTTP请求头的信息

```python
request.MATE
```

**path**  请求路径

获取完整路径（包含参数）：request.get_full_path() 

**session(单独有内容)**：

```python
设置session：
requset.session['name']='zhangsan'
获取session：
requset.session['name']
```

**COOKIES(单独有内容)：**:

```python
设置cookie：
respose.set_cookie('name','zhangsan')
获取cookie:
requset.COOKIES.get('name')
```

### templates模板：

templates根据字典数据动态变化的HTML网页，在使用时需要在项目根目录下创建templates文件夹，在文件夹下放置模板。在前后端分离模式下，该模块由前端负责。后端仅做数据返回，前端接受到数据后根据数据组织网页内容。在模板使用中locals，可以将函数内所有变量打包成字典。

```python
1.在根目录下创建templates文件夹，并放置模板
2.在配置文件下配置路径：:TEMPLATES下DIRS= [os.path.join(BASE_DIR,'tmplates')]
3.在视图函数中调用render响应给客户端  （ from django.shortcuts import render）
  return render(request,'index.html'，locals())

  注：在多人协同开发，如模板页重名，在负责的app内创建template文件夹，文件夹内再创建app同名文件夹存放模板页。
   return render(request,'logeing/index.html'，locals())

```

**动态数据：**

render将动态的字典数据结合模板数据一起返回给客户端。

在模板动态数据的使用：

{{ 列表.下标 }}

{{ 字典.键 }}

{{ 对象.方法 }}

{{ 函数 }}

示例：

```
后端视图：

def userview(request):
    user=zhangsan
    age=18
    data={'a':10}
    return render(request,'logeing/index.html'，locals())

前端页面代码：

<body>
	<h1>{{ user }}</h1>
	<h1>{{ age }}</h1>
	<h1>{{ data.a }}</h1>
	<h1>{{ data.a }}</h1>
</body>

```

**模板标签使用判断以及for循环**：

```django
语法：{% 标签 %} .....{% 结束标签 %}   括号无法使用，格式要求严格！
使用方式：

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

**过滤器使用：**

```
过滤器：{{变量|过滤器：参数1|过滤器2:参数2}}

upper  转换成大写

lower 转换小写

safe 默认不对变量中的字符串进行转义

add：“N”  将value的值增加N

truncatechars:“N”  截断，超过设置长度的字符串以...显示

前端代码：
<body>
	//转换成大写
	<h1>{{ user|upper }}</h1>
	// 将value增加1
	<h1>{{ age|add:1 }}</h1>
	// 截断超过XX的数据以...显示
	<h1>{{ data.a：truncatechars：10 }}</h1>
	<h1>{{ data.a }}</h1>
</body>
```

**模板的继承：**

父模板的内容复用，并可以覆盖父模板中相应的块。动态数据无法被继承。

定义父模板：

{% block block_name %} {% endblock %}   包裹子模板可能需要重写的地方 

继承模板：

{% extends '父模板文件' %} 

重写：{% block 块名称 %}更改内容 {% endblock %}   

