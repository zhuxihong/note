### AJAX原生：

```
创建异步对象：
window.XMLhttpRequest()
ie 6 以前版本
ActiveXObject（'Microsoft.XMLHTTP'）
```

```
创建连接方法：

open()   -->仅创建请求
参数：method（GET|POST）URL(请求地址)asyn（true：异步/false：同步）请求参数url后边

send()   -->发起请求
作用：通知xhr向服务器发送请求
参数：
get请求：body的值为null -》send（null）
post请求：body的值为请求数据 

readyState  -->判断xhr状态 0-5个值
0 代理创建，但尚未调用open
1 open方法已被调用
2 send方法已被调用
3 下载中
4 下载操作完成

属性 responseText  响应的数据

属性 status  服务器端响应码


事件：onreadystatechange  当响应状态为5 
```

```
示例：

function testXhr(){
    if (window.XMLHttpRequest){
        var xhr=new window.XMLHttpRequest();
    }else{
        var xhr=new ActiveXObject('Microsoft.XMLHTTP');
    }
    return xhr
}

        function getXhr(){
            let xhr=testXhr()
            console.log(xhr);
            xhr.open('GET','/test_xhr_get_server',true)
            xhr.onreadystatechange=function(){
                if(xhr.readyState==4 && xhr.status==200){
                    console.log(xhr.responseText);
                }
            }
            xhr.send(null)
        }
```

jQuery的AJAX操作：

```
$.ajax({
url:
type:get/post
data:传递的参数，字符串，也可是字典，
success：回调函数，请求成功时执行,函数包含形参，形参表示服务器返回数据
error：请求失败时执行
beforesend：ajax请求之前执行的操作，
contentType：标注请求数据类型
dataType：响应的数据类型
})
```

```js
使用jq ajax发送post请求：

$(function(){
        $('#btn').click(function(){
            $.ajax(
                {url:'/index/log',
                 type:'post',
                 data:{
                    username:$('#use').val(),
                    userps:$('#pas').val(),
                    csrfmiddlewaretoken:$("[name='csrfmiddlewaretoken']").val()
                 },
                 success:function(res){
                    alert(res)
                 }
            }
            )
        })
    })
```

### JSON遍历

```
 方式1：
 $(json_arr).each(function(index,obj){
                    console.log(index);
                    console.log(obj.username,obj.age);
                })
  方式2：              
  $.each(json_arr,function(index,obj){
  		   console.log(index);
           console.log(obj.username,obj.age);
  })
```

### JSON对象序列化:

前端操作：

因为网络传输永远是字符串，所以json不能直接传递。需要进行序列化传递。

#转换对象为字符串

序列化方法：json.stringify(js对象)    

#转换字符串为对象

反序列：JSON.parse（Js字符串）

后端操作：

导包：import json

序列化：json.dumps（元祖|列表|字典）

反序列化：json.loads(json字符串)



### django处理json数据：

方法1：给到的信息很多。

```
from django.core import serializers
json_str=serializers.serializer('json',对象列表)
return HttpResponse(user_str,content_type='application/json')
```

方法2：

```
user=User.objects.all()
    users=[{'username':i.username,'password':i.password} for i in user ]
    user_str=json.dumps(users)
    return HttpResponse(user_str,content_type='application/json')
return HttpResponse(user_str,content_type='application/json')
```

```
user=User.objects.all()
users=[{'username':i.username,'password':i.password} for i in user ]
return JsonResponse(users,safe=False) 列表或元祖需要设置sefe为False
```

### 跨域：

相同协议，相同域名，相同端口同时满足是才是同源‘。

通过script向服务器资源发送请求由服务器资源指定前端页面哪个方法来执行响应数据。

标签的src原理也是发送GET请求。利用这个特性可以跨域发送请求，在查询字符串中传递函数名称。在服务器返回数据时发送函数名（数据）返回给浏览器，浏览器识别后会执行这个预留函数

jq处理：

json with padding

用户传递一个callback参数给服务器，然后服务器返回数据是会将callback参数作为函数名来包裹json数据

```
方式1：
$.ajax({
	url:XXX,           #跨域路由，这里不需要参数
	type:'get',
	dataType:'jsonp',   #服务器响应回来的格式
	jsonp:'callback'    #查询在字符串名称
	jsonpcallback:函数名，      #预留函数名称
})

最终方式：
$.ajax({
	url:XXX,          
	type:'get',
	dataType:'jsonp',   #服务器响应回来的格式
	success：function（）{
	   要执行的逻辑
	}
})

服务端返回：return HttpResponse(func+'('+ 返回的数据 +')')
```

1.58
