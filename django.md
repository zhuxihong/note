## 创建和启动项目：

```
创建项目： django-admin startproject 项目名称

启动：python3 manage.py runserver
```

### 模式

```
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
views  数据渲染和视图

```



## 路由模块:

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

## views 模块：

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

## templates 模板加载响应

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

模板中使用：{% url '别名'} 

### 静态文件

不能和服务器做动态交互的文件都是静态文件（css，img，js）。settings.py内设置2项内容：

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

## 应用APP

应用在django中是一个独立的业务模块。

创建应用：

```
python3 manage.py startapp music
```

注册：

```
INSTALLED_APPS 列表下添加app名称

migrations 保存数据迁移的中间件
init   应用子包的初始化文件
admin 应用后台管理配置文件
apps 应用的配置文件
models 与数据库相关的映射类文件
tests 应用的单元测试文件
views 视图处理函数文件
```



### 主路由分发

//分布式路由的路由分发

//http://127.0.0.1:8000/路由名称/XXXX

```
path('user/',include('user.urls'))

result=Book.objects.path('路由',include('路由.模块'))
```

### APP路由：

在app文件下创建urls文件，复制主路由   templates文件夹 文件夹下创建与应用同名文件放置模板

当页面有重复是在templates下创建与应用名同名的文件夹。即可解决

## 数据模型层：

sudo apt-get install default-libmysqlclient-dev

sudo apt-get install python3-dev

sudo pip3 install mysqlclient

### 数据库配置

```
DATABASES = {
    'default': {
        'ENGINE': 'django.db.backends.mysql',
        'NAME': 'mysite3',   #数据库名字
        'USER':'root',
        'PASSWORD':'123456',
        'HOST':'127.0.0.1',
        'PORT':'3306'
    }
}
```

django shell使用：

打开shell ：python3 manage.py shell

### orm框架(表创建）：

```
Book.objects.create(tiele='django',price=100)  objects管理器对象

```

创建orm框架步骤：创建应用，并注册。在应用内models.py 内创建模型类。生成数据库迁移文件，执行生成表。

增加字段：django修改表结构一定要使用模型类方式。在数据模型内增加字段，执行迁移,默认值是由django完成，没有表约束；



类--》表  对象—》行 属性—》字段

```
class Book(models.Model):
    # 类似于创建表的
    tiele=models.CharField('书名',max_length=50,default='')
    # 带小数点的小数，但凡表示金额的一般使用这种类型,
    # decimal_places 小数点保留几位
    # default  默认值
    # max_digits 有效位置长度
    # django会自动生成主键
    price=models.DecimalField('定价',max_digits=7,decimal_places=2,default=0.0)
```

生成数据表：

生成数据库迁移文件：

python3 manage.py makemigrations        生成一个0001_initial.py

执行生成表：

python3 manage.py migrate

模型的基本操作：

增create 删delete 改Update  查filter

### 数据库查询：

需要引用

| 方法               | 返回值                                          |
| ------------------ | ----------------------------------------------- |
| all                | 返回对象(select * from XX)                      |
| values             | 获取列返回字典                                  |
| values_list        | 返回列表                                        |
| get                | 满足条件的唯一一条数据。多条数据会报错          |
| filter（属性1=值） | 返回多条数据，多个属性值与关系  #惰性查询       |
| exclude            | 除XX以外                                        |
| filter查询谓词参数 |                                                 |
| __contains         | 包含某值                                        |
| __startswith       | 以什么开头                                      |
| __endswith         | 以什么结尾                                      |
| __gt               | 大于某值                                        |
| __gte              | 大于等于                                        |
| __lt               | 小于某值                                        |
| __lte              | 小于等于                                        |
| __in               | 指定范围内的 XX__in=[值1，值2，值3]  字符串操作 |
| __range            | 指定范围内的 XX__range=（（30，60））范围操作   |

排序：

order_by(列，列)  降序加个 -

### 修改数据：

查-改-保存
保存:模型类.save()

批量修改：

```
books=Book.objects.filter(pub='北京大学出版社')  #找到所有北京大学出版社的书
books.update(market_price=100)    #修改零售价为80

#update 是直接发送sql语句执行
```

### 删除数据：

查询到数据，直接执行对象.delete( )即可，项目中，不会将记录真正删除，而是设置删除标记；



### 聚合操作（分类统计）：

需要导入：from django.db.models import *

不带分组：

Sum，Avg，Count，Max，Min

语法：

数据类.objects.aggregate(结果变量=聚合函数(列))

```python
result=Book.objects.aggregate(mycnt=Count('price'),myMax=Max('price'),myMin=Min('price'))
```

带分组

```
annotate

pub_set=Book.objects.values('pub').annotate(mycnt=Count('price'),mysum=Sum('price'))
#以pub分组，统计数量，总价
```

### F对象：

```
Book.objects.all().update(market_price=F('market_price')+1)

获取所有的书籍给所有的书籍增加1元，比遍历执行效率高！
使用情况：在原有数据的基础行增加值。

books=Book.objects.filter(market_price__gt=F('price'))
F内的可以表示一个值
		用与两个字段比较
		
```

### Q对象：

使用Q包裹查询条件,可以表示 或 非 以及多个条件

```python
引入： from django.db.models import Q
Book.objects.filter（Q（pub=‘北京大学出版社’）|Q（title=‘python’））
#查找北京大学出版社或名称为python的书籍

|或  & 与 ~非
```

### 原生操作:

不推荐使用：会被sql语句注入攻击。迫不得已使用时，必须对输入进行检查

模型类.objects.raw(sql语句，[拼接参数])

### 表关系：

clss B（）：

外键： 属性=models.oneToonefield（A，on_delete=XX）  

#### 主从表删除关系

orm中先删除谁都可以，通过第二个参数可以制定删除的方式。

on_delete=models.CASCADE     级联删除   联动删除(删除主表时从表引用也会删除)

on_delete=models.PROTECT    阻止被引用的对象的删除（主表有被引用是无法被删除）

####  一对一表关系创建/查询：

1创建模型类建立关系

2 添加数据

author1=Author.objects.create(name='王老师')  #先插入主表

wife1=Wife.objects.create(name='王夫人'，author=author1)  #再插入从表

wife1=Wife.objects.create(name='王夫人'，author_id=author1.id)   第二种方式

3 查询数据

正向查询：

关联属性可以作为对象使用。直接使用.即可获取

反向查询：

a2=Author.objects.get(name='王老师')

a2.wife.name   ##王夫人

隐藏属性，主表内隐藏wifi属性。主表获取的数据可以使用影藏属性获取到从表数据

#### 一对多关系建立与查询

创建：

属性=models.foreignkey（模型一的类，删除关系，verbose_name=‘‘出版社’）

添加：

反向添加：pub2.book_set.create(title='西游记')  比一对一多了一种添加方式

查询：

反向查询，一对多返回的是一个对象，需要使用数据库查询的属性获取；

#### 多对多关系建立与查询

创建：

属性=models.Manytomanyfield（author）

给2表中的一个表添加多对多关系会自动创建一张多对多关系表；

添加：

方式1：

a1=Author.objects.create(name='吕老师')

a2=Author.objects.create(name='王老师')

book11=a1.book_set.create(title='python')  #表的关联，在book内会创建一本书

a2.book_set.add(book11)      # 添加多对多的关系

当存在多对多关系表时会自动记录。

方式2：

book=Book.objects.create(title='java')      #创建一本书，此时并无关联

author3=book.authors.create(name='guoxiaonao')   # 创建一个新的作者

book.authors.add(a1)  #增加一个原有作者

查询：

按照一对多方式查询。

### 后台管理管理界面：

创建超级管理员：

python3 manage.py createsuperuser

自定义管理数据类 admin模块下：

admin.site.register(模型类)

自定义模型类的展示样式：

1.重写模型类__str__

2.模型管理器的使用方法。

```
class BookManager(admin.ModelAdmin):
        #设置要显示的字段
        list_display=['id','tiele','pub','price']
        #设置可以点击的
        list_display_links=['id','tiele']
        #设置可以被查询的字段
        search_fields=['id','tiele']
        #设置过滤器（分组功能）
        list_filter=['pub']
        # 设置可以在查看页面直接修改的字段
        list_editable=['market_price']
```

3注册模型管理类

### 用户权限模块：

创建普通用户create_user

```python
from django.contrib.auth.models import user
user=User.objects.creaet_user(username='用户名'，password=‘密码’，email=‘邮箱’)
```

创建超级用户create_superuser

```
user=User.objects.creaet_superuser(username='用户名'，password=‘密码’，email=‘邮箱’)
```

删除用户仅为标记

扩展字段：

1，添加新的应用

2.定义模型类 继承 AbstractUser

3.settings.py 中设置 AUTH_USER_MODEL='应用名.类名'

```
clss 新的类名（AbstractUse）：
	属性=数据类型（）
	
在生成系统自带表单前设置。
settings.py 中设置 AUTH_USER_MODEL='应用名.类名'
```

## 用户状态保持验证

### Cookies ：存储在客户端

Cookies存储在客户端会话状态。数据结构是键值对结构。只存储ASC码值，按域隔离。

Cookies必须由

**设置**cookies：HttpResponse.set_Cookies(4个参数)

1. key:cookies的名字
2. value：cookies 的值
3. max_age:cookies 存活时间，秒单位
4. expires：具体过期时间

```
def set_cookies(request):
    resp=HttpResponse('set cookies OK')
    resp.set_cookie('uname','aid2102',600)
    return resp
```

**删除**Cookies：HttpResponse.delete_Cookies（）

**获取**Cookies：request.Cookies.get(值，缺省值)

### session: 存储在服务端

session会话控制，需要借助于Cookies。Cookies中存储session id 与服务器一一对应。

给浏览器Cookies 发送session id 保存，用户再次访问时，携带session id 发送请求。服务器根据session id判断是否过期。未过期则免登陆。在网络上传输的不是真的用户数据，而是sessionid所以相对安全

需要注册app：'django.contrib.sessions',  【默认有】

中间件：'django.contrib.sessions.middleware.SessionMiddleware',【默认有】

保存session值到服务器

request.session['key']=Value

获取session的值

Value=request.session['key']

Value=request.session.get['key',缺省值]

删除 session的值

del request.session['key']

**使用session需要迁移数据库**：python3 manage.py migrate

```
# session 设置
# 设置session过期行为和时间：（翻译浏览器关闭即session过期，过期时间设定）
SESSION_COOKIE_AGE = 60 * 30 # 30分钟
SESSION_SAVE_EVERY_REQUEST = True  # 每次请求会更新sessions有效期限
SESSION_EXPIRE_AT_BROWSER_CLOSE = True # 关闭浏览器，则COOKIE失效
```

```python
def set_session(request:HttpRequest):
    #设置session id并发送给客户端cookies保存,相当于字典
    request.session['uname']='aid2102'
    return HttpResponse('设置session 成功')

def get_session(request:HttpRequest):
    #客户端第二次访问的时候，携带session id发出请求，从cookies获取session id 并向数据库查询
    uname=request.session.get('uname','meiyou')
    return HttpResponse('uname is %s'%uname)

def del_session(request:HttpRequest):
    if 'uname' in request.session:
        del request.session['uname']
    return HttpResponse('session 删除成功')
```

删除过期：python3 manage.py clearsessions            m=hashlib.md5()
            m.update(password_1.encode('utf-8'))
            password=m.hexdigest()

### 加密使用：

 m=hashlib.md5()

m.update(password_1.encode('utf-8'))

password=m.hexdigest()



### 缓存：

使用场景：低频响应，快速响应，需要注意数据的一致性；

1 数据库缓存：

#### 设置MYSQL缓存：

```
CACHES={
    'default':{
        'BACKEND':'django.core.cache.backends.db.DatabaseCache',
        'LOCATION':'my_cache_table', 
        'TIMEOUT':300,  #缓存保存时间
        'OPTIONS':{ 
            'MAX_ENTRIES':300,
            'CULL_FREQUENCY':2
        }
    }
}
```

理解：把数据存进一个比较小的表里面。提高整体的速度。各数据库介质不一样。

生成缓存表：

```
python3 manage.py createcachetable 
```

#### redis设置：

```
需要安装模块：pip3 install django-redis
```

```
CACHES = {
    "default": {
        "BACKEND": "django_redis.cache.RedisCache",
         # 安装redis的主机的 IP 和 端口,以及使用的仓库
        "LOCATION": "redis://127.0.0.1:6379/0",
        "OPTIONS": {
            "CLIENT_CLASS": "django_redis.client.DefaultClient",
            "CONNECTION_POOL_KWARGS": {
            "max_connections": 1000,
            "encoding": 'utf-8'
            },
        "PASSWORD": "123456" # redis密码
        }
    }
}
```



#### 服务器缓存使用：

```
from django.views.decorators.cache import cache_page
```

```
整页缓存：
@cache_page(60)    #缓存60秒 程序的结果。缓存有效期内会执行缓存结果。
def test_cache(request:HttpRequest):
    t1=time.time()
    time.sleep(5)
    return HttpResponse('t1 is %s'%t1)

路由中使用：
path('test_cache',ache_page(60)(views.test_cache))

总结：
整页缓存灵活性差，不能缓存部分数据。很难删除缓存。
```

```
部分缓存导包 ：1. from django.core.cache import caches   多个配置项
			 2. from django.core.cache import cache	   单个配置项
			 -  局部缓存,使用缓存api使用更灵活
```

```
cache.set（‘key：字符串’，‘value：python对象’，缓存时间）
cache.set('uname','aid2102',600)  #uname键，缓存值是aid2102，存600秒

cache.add()  #键存在时则设置失效
cache.get_or_set() #获取成功则不设置，不成功则设置，
cache.get('uname')
cache.delete('uname')

cache.set_many({多个键值})    设置多个键值
cache.get_many([多个键])
cache.delete_many([多个键])
cache.add()  #
```

#### 浏览器缓存：

```
强缓存：
Expires 是响应信息
Cache-Control:  时间段
协商缓存：
强缓存失效以后，会使用到协商缓存
```

### 中间件：

一个中间件包含的：

1. 进入路由前
2. 响应浏览器前
3. 调用视图函数前
4. VIEWS报错时
5. 返回模板 前

模块：from django.utils.deprecation import MiddlewareMixin

重写：新建一个py文件，引入模块，创建类继承重MiddlewareMixin 重写process_方法

process_ 和视图函数一样包含 request 对象 

### CSRF跨站伪造攻击：

通过其他网站post请求，发起攻击。利用浏览器带有的cookie伪造身份。向服务器发送危害请求；

django避免跨站伪造攻击：{% csrf_ token %}

某个模板不需要保护的函数 @csrf_exempt  装饰器

## 分页：

paginator=paginator（需要分类数据的对象列表，每页数据个数）  分页数据整体管理

paginator属性：

count 分类数据的总数

num_pages：分页页面总数

page_range:用户记录当前的码数

per_page:每页数据的个数

paginator方法：

paginator.page(获取第几页的数据)



## 文件下载：

django可以直接在视图函数生成csv文件 并响应给浏览器

```
1.设置响应对象（更改响应头）
2.通过响应头指定以附件方式处理，以及文件名称
3.准备数据
4.构建写入器，数据写入到响应对象中
5.使用创建的写入器写入数据
6.返回响应

理解：把文件内容写进响应对象中。设置响应头，告诉浏览器这是个附件和文件类型，文件名。
```

## 文件上传：

python流程：

1.必须是form表单，带有enctype="multipart/form-data"

2.input 属性type="file"

3.flies=request.FILES['input name']

4.file.name 获取文件名

5.file.file 文件的字节流数据

6.配置文件，并创建文件夹

MEDIA_URL='/media'

MEDIA_ROOT=os.path.join(BASE_DIR,'media‘’)

模块：

```python
#导入django的sttings而不是项目的
from django.conf import settings   

from django.views.decorators.csrf import csrf_exempt

```

django提供的方法：

借助orm,文件路径保存在数据库。

创建表属性：

```python
myfiles=models.filefield(upload_to='myfiles')  存储路径

在业务逻辑中，插入filed对象。就可以完成上传

```

## 设置为静态直接访问：

```
在主路由模块添加设置
from django.conf import settings 
urlpattrns += static（settings.MEDIA_URL,document_root=stings.MEDIA_ROOT）
```

------



## 邮件发送：

```
from django.core import mail
```

```
固定设置：
EMAIL_BACKEND='django.core.mail.backends.smtp.EmailBackend'
EMAIL_HOST = "smtp.qq.com" 
EMAIL_PORT = 25 
EMAIL_HOST_USER = "435498423@qq.com"
EMAIL_HOST_PASSWORD = "kkcmxfuhnotvbhii"
------------------
EMAIL_USE_TLS = True    #是否明文
EMAIL_FROM = "l791034063@163.com"
```

```
mail.send_mail(标题，邮件内容，发送者，接收人[列表])
```

------



## 项目部署：

1.在安装机器上配置相同版本环境

2 django项目迁移  sudo scp 当前项目源代码

3 用uwsgi代替 runserver 方法启动服务器

4 配置nginx反向代理服务器

5用nginx配置静态文件路径，解决静态路径问题

```ini
uwsgi配置：
添加文件: 项目同名文件夹/uwsgi.ini

[uwsgi]
模式：
#套接字模式
socket = 127.0.0.1:8000   
#http模式
http=127.0.0.1:8000
#项目目录
chdir = /home/zhuxihong/mysie8    
#wsgi.py所在位置
wsgi-file = mysie8/wsgi.py   
#进程数
processes = 4      
#线程数
threads = 2		
#服务记录文件（服务启动后主进程的ID）
pidfile=uwsgi.pid  
#服务的日志文件
daemonize=uwsgi.log  
#开启主进程管理模式
master=true   

DEBUG关掉
在项目文件下：sudo uwsgi --ini uwsgi.ini
停止 uwsgi ：sudo uwsgi --stop uwsgi.pid
查看进程：ps aux|grep 'uwsgi'
stop失败：
ps -ef |grep ‘uwsgi’ | grep -v grep | awk '{print $2}'|xargs sudo kill -9
```

```nginx
nginx反向代理配置：

作用：均衡负载
修改配置文件 /etc/nginx/
nginx.conf  主服务配置
access_log    访问日志41行
error_log     错误日志42
virtual HOst configs   管理站点 61，62行


2.
sudo vi /etc/nginx/sites-enabled/default
location / {
uwsgi_pass 127.0.0.1:8000;   #重定向到127.0.0。1的8000端口
include /etc/nginx/uwsgi_params; #将所有参数转到uwsgi下
}
	
3 修改后重启
sudo /etc/init.d/nginx start | stop

4.修改uwsgi配置模式为socket
nginx配置后默认使用80端口，后续无需再使用端口号

nginx读取不到静态文件需要配置
1，创建新路径-主要存放django所有静态文件
2，在django中添加新的配置：
STATIC_ROOT ='/home/用户名/项目名称_static/static'
3.迁移所有静态文件：
pyhon3 manage.py collectstatic
4.修改配置nginx,增加
location /static {
    root /home/zhuxihong/tarena/mysie8_static;
}
ps 出现403问题，这个就是由于nginx中通常会默认user为wwwuser wwww，而你在实际后台登录时，通常是作为管理员root(部分云的管理员用户不一样，比如腾讯云就是ubuntu)用户登录，而产生了启动用户和nginx工作用户不一致，导致权限问题。
解决方法：将nginx.config的user改为和启动用户一致
```

## 定制404/500页面

模板页内文件名404.html，新增页面需要重启uwsgi。

## 邮件告警：

ADMINS=[(错误接收方)]

过滤敏感信息：

```
from django.views.decorators.debug impor sensitive_variables,sensitive_post_parameters
增加装饰器
@sensitive_variables(需要过滤的变量)
post过滤:
@sensitive_post_parameters(需要过滤的变量)
```



## 网络云笔记项目：

```
models1. 创建项目: django-admin startproject net_note

2. 启动项目：python3 manage.py runserver

3. 设置中文与时区：

   LANGUAGE_CODE = 'zh-Hans'

   TIME_ZONE = 'Asia/Shanghai'
   

4. 创建数据库：create database net_note default charset utf8
5. django配置数据库：

   DATABASES = {
    'default': {
        'ENGINE': 'django.db.backends.mysql',
        'NAME': 'net_note',   #数据库名字
        'USER':'root',
        'PASSWORD':'123456',
        'HOST':'127.0.0.1',
        'PORT':'3306'
    }
}
6. 配置sessions：python3 manage.py migrate
7. 创建用户app  创建笔记app
   python3 manage.py startapp user
   python3 manage.py startapp note
8. 构建模型类：
9. 命令构建表：
   生成迁移文件python3 manage.py makemigrations  
   执行生成表 python3 manage.py migrate
10. app内添加模板目录
11. 在主路由设置app分发路由
12. app分布路由urls参考主路由,设置app路由
```

