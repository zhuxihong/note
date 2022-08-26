创建异步对象：

```

window.XMLhttpRequest()

ie 6 以前版本
ActiveXObject（'Microsoft.XMLHTTP'）
```

```
方法：
open()
参数：method（GET|POST）URL(请求地址)asyn（true：异步/false：同步）请求参数url后边
send()
作用：通知xhr向服务器发送请求
参数：
get请求：body的值为null -》send（null）
post请求：body的值为请求数据      


```

