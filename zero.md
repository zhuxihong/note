### 前后端分离

```
对于django项目而言，相当于把templates下的html的页面单独的放到另外一个项目中。相当于

MTV中的T去掉了，视图函数不再返回页面，只返回数据。大多数情况下，交互的数据格式是json

如果在django的视图函数中return render的函数使用了，意味着没有做前后端分离
```

### token-令牌

### 生成token

```
base64:不是加解密，是编解码。不同路由设备传输数据时，对不可见的控制字符处理结果不同，所以可能操作传输错误，传输前使用base64编码。对方收到收在进行解码。由于base64编码全部是可见字符，不会造成传输错误。
```

```
import base64
```

**消息认证码：**

通过特殊计算方式计算出来的秘钥。结合原始数据，使用散列算法计算出来的认证码

```
import hmac

h=hmac.new(key,str,digestmod='sha256')
h.digest()  #获得认证码
```

### 认证码构成

**JION-WEB-TOKEN**

组成部分：HEADER+PAYLOAD+SIGNATURE  头-载荷-签名

头部信息：

```
{"alg":'HS256','typ':'JWT'}
该部分数据转成json并用base64编码
```

载荷信息：

```
公有声明
{‘exp’：xx  token的过期时间戳
 ‘iss’：xx  token签发者
 ‘iat’：xx  token创建时间的时间戳
 ‘aud‘：xx  token签发面向群体
}
```

```
私有声明根据业务需求添加进自定义的key，先转换json串，并用base64编码，共有声明+私有声明加起来就是原始数据
```

签名：

```
JWT签名=HS256（key，HEADER编码+b‘.’+PAYLOAD编码）
hm=hmac.new(key.encode(),header_bs+b'.'+payload_bs,digestmod='SHA256')
token=头部信息+公有声明+签名
```

```
生成token大致流程：

1，制作头部信息（固定）
2，制作载荷信息[在私有声明内增加公有声明](私有声明+公有声明)
3，制作签名信息（私钥，头部信息+b“.”+载荷信息）
4，token就是把3个信息拼接（头部+“.”+载荷+“.”+签名）
每一步都需要把信息从字典转换成json串，再使用bs64编码。bs64编码尾部会产生=号，需要去除。

解析token流程：
1.将拼接信息拆分为三个部分
2.将头部和载荷信息与私钥重新制作签名信息，与发送过来签名信息做对比
3.通过签名核验，则开始解析载荷信息
4.由于载荷信息有去除尾部=号，重新添加。再通过bs64解码
5.解码后转换回字典，核对有效期，有效期内则返回字典信息处理

秘钥的交换传递有专有的方法。秘钥由双方沟通！
```

```
token示例代码：
----------------------------------------------------------------------------
# 用到的模块
import base64
import json
import hmac
import time
import copy

class Jwt():
    def __init__(self) -> None:
        pass
    @staticmethod
    def encode(payload,key,exp=300):
        # HEADER
        header={'alg':'HS256','typ':'JWT'}
        # 字典=》json串
        header_json=json.dumps(header,separators=(',',':'),sort_keys=True)  #separators去除空格 sort_keys保证顺序（因为字典无需，可能发生顺序不一致）
        #base64编码
        header_bs=Jwt.b64encode(header_json.encode())
        # PAYLOAD
        payload_data=copy.deepcopy(payload)
        # 在原来字典上增加公有声明
        payload_data['exp']=time.time()+int(exp)
        # 字典=》json
        payload_json=json.dumps(payload_data,separators=(',',':'),sort_keys=True)
        # base64编码
        payload_bs=Jwt.b64encode(payload_json.encode())
        # 签名(秘钥+)
        hm=hmac.new(key.encode(),header_bs+b'.'+payload_bs,digestmod='SHA256')
        hm.hexdigest()  #16进制的字符串，用于输出
        # 消息验证码，字节串
        digest=hm.digest()    
        hm_bs=Jwt.b64encode(digest)
        # 将三部分合在一起，生成token并返回
        return header_bs+b'.'+payload_bs+b'.'+hm_bs

    @staticmethod
    def b64encode(j_s):
        # 除去等号
        return base64.urlsafe_b64encode(j_s).replace(b'=',b'')
    
    @staticmethod
    def b64decode(b_s):
        # 补上等号，因为编码的时候把后面的等号去掉了，所以需要补回
        rem=len(b_s)%4    #能不4整除的数，表示后面没有=号，如果他有余数则表示有=
        if rem>0:
            b_s+=b"="*(4-rem)  #4减余数则是等号的数量。让他补回
        return base64.urlsafe_b64decode(b_s)

    @staticmethod
    def decode(token,key):
        # 将token拆分成3部分
        header_bs,payload_bs,sign=token.split(b'.')
        # 重新计算消息消息认证码
        hm=hmac.new(key.encode(),header_bs+b'.'+payload_bs,digestmod='SHA256')
        digest=hm.digest()
        # 再次计算的结果，称为计算结果或签名
        hm_bs=Jwt.b64encode(digest)
        # 验签不通过，抛出异常
        if hm_bs !=sign :
            raise
        # 想获取payload,但是需要base64解码
        payload_js=Jwt.b64decode(payload_bs)
        # json=》字典对象
        payload=json.loads(payload_js)
        # 有了字典对象就可以获取公有声明和私有声明的值了。
        exp=payload['exp']
        now=time.time()
        if now>exp:
            raise
        return payload


if __name__ =='__main__':
    token=Jwt.encode({'username':'aid2102'},'123456')
    print(token)
    payload=Jwt.decode(token,'1234567')
    print(payload)
```

### 跨域资源共享

浏览器自动完成，服务器需要支持（响应头需要有特殊头）

简单请求和预检请求

**满足以下全部条件**的请求为 **简单请求**

1，请求方法如下：

GET  or HEAD or POST

2，请求头仅包含如下：

Accept

Accept-Language

Content-Language

Content-Type

3，Content-Type 仅支持如下三种：

application/x-www-form-urlencoded

multipart/form-data

text/plain

**不满足以上任意一点的请求都是 预检请求**



### RESTful-Representational State Transfer

前后端交互的规范：

1.每个资源都有一个独一无二的URI,URI表示统一资源标识符，URL统一资源定位符

具体案例如下：

```python
GET /zoos：列出所有动物园
POST /zoos：新建一个动物园
GET /zoos/ID：获取某个指定动物园的信息
PUT /zoos/ID：更新某个指定动物园的信息（提供该动物园的全部信息）
PATCH /zoos/ID：更新某个指定动物园的信息（提供该动物园的部分信息）
DELETE /zoos/ID：删除某个动物园
GET /zoos/ID/animals：列出某个指定动物园的所有动物
DELETE /zoos/ID/animals/ID：删除某个指定动物园的指定动物
```



​	6，巧用查询字符串

```python
?limit=10：指定返回记录的数量
?offset=10：指定返回记录的开始位置。
?page=2&per_page=100：指定第几页，以及每页的记录数。
?sortby=name&order=asc：指定返回结果按照哪个属性排序，以及排序顺序。
?type_id=1：指定筛选条件
```

​	

​	7，状态码

​		1，用HTTP响应码表达 此次请求结果，例如

```python
200 OK - [GET]：服务器成功返回用户请求的数据
201 CREATED - [POST/PUT/PATCH]：用户新建或修改数据成功。
202 Accepted - [*]：表示一个请求已经进入后台排队（异步任务）
204 NO CONTENT - [DELETE]：用户删除数据成功。
400 INVALID REQUEST - [POST/PUT/PATCH]：用户发出的请求有错误，服务器没有进行新建或修改数据的操作，该操作是幂等的。
401 Unauthorized - [*]：表示用户没有权限（令牌、用户名、密码错误）。
403 Forbidden - [*] 表示用户得到授权（与401错误相对），但是访问是被禁止的。
404 NOT FOUND - [*]：用户发出的请求针对的是不存在的记录，服务器没有进行操作，该操作是幂等的。
406 Not Acceptable - [GET]：用户请求的格式不可得（比如用户请求JSON格式，但是只有XML格式）。
410 Gone -[GET]：用户请求的资源被永久删除，且不会再得到的。
422 Unprocesable entity - [POST/PUT/PATCH] 当创建一个对象时，发生一个验证错误。
500 INTERNAL SERVER ERROR - [*]：服务器发生错误
```

​		2, 自定义内部code 进行响应

​		如 返回结构如下  {'code':200,  'data': {}, 'error': xxx}



​	8，返回结果

​	根据HTTP 动作的不同，返回结果的结构也有所不同

```python
GET /users：返回资源对象的列表（数组）
GET /users/guoxiaonao：返回单个资源对象
POST /users：返回新生成的资源对象
PUT /users/guoxiaonao：返回完整的资源对象
PATCH /users/guoxiaonao：返回完整的资源对象
DELETE /users/guoxiaonao：返回一个空文档
```

项目技巧：

1 如果每个表都需要使用某种数据可以写个类来继承

2.解决一个函数处理多个请求问题，采用视图类(视图类写在该app目录下继承：from django.views import View,继承view后内置as_view参数，浏览器发送相关请求方法，会自动调用视图类中相同的方法。如浏览器发送get请求，会调用视图类的get函数)

3.路由模块在后面不需要分割时。

4.密码需要加密