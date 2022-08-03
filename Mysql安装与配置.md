## 基础篇

### MySQL社区版安装

​	社区版下载地址：[[MySQL ：： 下载 MySQL Installer](https://dev.mysql.com/downloads/windows/installer/8.0.html)](https://dev.mysql.com/downloads/file/?id=511553)

![image-20220624005556509](C:\Users\43549\AppData\Roaming\Typora\typora-user-images\image-20220624005556509.png)

下载后直接进行安装，默认即可

**LINUX**安装：

sudo apt install mysql-server

sudo apt install mysql-server  配置文件：/etc/mysql

数据库存储目录：/var/lib/mysql

LINUX查看是否启动：

sudo service mysql status

启动和关闭：

启动：sudo service mysql startus

关闭：sudo service mysql stop 

登录出问题的时候可以提升管理权限。

### Windows MySQL启动/停止

​	运行services.msc，找到设置的MySQL用户名称，可启停服务！MySQL会随系统自启动

![image-20220624010812254](C:\Users\43549\AppData\Roaming\Typora\typora-user-images\image-20220624010812254.png)

命令行操作

​	启动：net start mysql80

​	停止：net stop mysql80

### MySQL客户端连接

方式1：

​	MySQL自带的命令行工具，打开后直接输入设置的密码即可链接成功

![image-20220624011236190](C:\Users\43549\AppData\Roaming\Typora\typora-user-images\image-20220624011236190.png)

方式2：

CMD命令行工具执行指令

```sql
mysql[-h 123.0.0.1][-P 3306] -U root -p
```

配置系统环境变量：

在系统path变量内添加一个MySQL变量，位置C:\Program Files\MySQL\MySQL Server 8.0\bin  

windows命令行:mysql -u root -p

ps：无法使用命令需要进入C:\Program Files\MySQL\MySQL Server 8.0\bin 文件下再输入