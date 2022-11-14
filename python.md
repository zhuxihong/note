## Python

### 项目文件管理：

python主模块不会生成【.pyc】机器码文件，主模块内代码尽量少。导入模块会生成机器码，提高执行效率！

模块变量：

模块第一个字符串储存在doc内

被导入的模块存储的真是名字，主模块存储的是main

 

程序结构：

 架构：继承是隔离，多态环境下 

算法：高内聚，降维

### 生成器/迭代器

```
迭代器：
下列代码主要用于学习迭代器，迭代器需要的2个重要函数 
1. __iter__   生成可迭代对象
2. __next__   生成迭代器
```

#### for 循环原理：

for循环通过对象是否包含__iter__函数，不断的执行next函数，

生成器等于可迭代对象加迭代器，特点是惰性，延迟。不占据内存，可迭代对象最主要的功能是可以被for循环使用，当使用生成器的时候就是重复调用__next__推算数据。不将数据存储在内存中，生成器的本质是迭代器。他弱化了可迭代对象

#### 生成器

调用但不执行函数体，而是返回生成器对象。
生成器=可迭代对象+迭代器 
计算一次 生成一次 返回一次
适用性： 函数有一个结果，使用return返回，函数有多个结果使用yield

```
优点：
    不占内存
缺点：
    不能重复使用
    无法索引
```

**yield**可以做到连续返回效果，只要有数据，他就能按照提取方法一条一条的返回。不像return结束就没有了yield语句 代替了__next__函数。生成了迭代器简化了生成迭代对象，传递数值的过程。yield是传递信息的通道

```
循环一次 计算一次 返回一次
不存储数据，需要那些数据，他会通过自定义规则。计算提取，以达到节省内存
```

### 函数式编程

------

适用性：多个函数，代码大致相同，逻辑不同js变量

封装  将变化点分成多个函数

继承   将变化点【抽象】为参数，通过参数确定【统一】的调用方法，从而【隔离】通用函数与变化点函数。

抽象（将变化点抽象，剔除不同，寻找共同点），统一，隔离

多态  编码通用函数是调用参数，运行时传递具体功能函数。

价值：增加功能，不修改通用函数。



多个函数，代码大致相同，逻辑不同

运用函数式变成思想，创建了集成操作框架（来源于微软的Linq技术）

### 闭包

------

三大要素：

1，必须是嵌套函数

2，内部函数访问外部函数变量

3，外部函数返回值必须是内部返回值

作用：

外部函数栈帧执行过后不释放供内部函数使用，延迟调用返回的函数

应用：

逻辑连续，得到一个变量可以多次调用。

1.在旧的函数内增加新的功能使用。

2.在没有面向对象逻辑的语言环境下使用。在转换C语言

代码例子：

```python
def get_pocketMoney(money):
    print(f"获得了{money}元")
    def spendMoney(article,spend):
        nonlocal money
        money -= spend
        print(f"买{article}花了{spend}元,还剩{money}")
    return spendMoney
action=get_pocketMoney(500)
action("芭比娃娃",200)
action("芭比娃娃",300)
```

### 装饰器：

在不修改旧功能的基础上，增加新功能，核心逻辑是拦截调用，外函数返回内函数，内函数调用外函数传递的参数，使用闭包思想。内函数的返回值是旧功能的返回值，

传统解决方案：

```python
# 在不修改旧功能调用以及代码情况下增加新功能
def func01():
    print("我是旧功能")
def new_func(func):
    def ceshi():
        print("我是一个新功能")
        func()
    return ceshi
func01=new_func(func01)
func01()
```

python装饰器：

```python
# 在不修改旧功能调用以及代码情况下增加新功能
def new_func(func):
    def ceshi():
        print("我是一个新功能")
        func()
    return ceshi

@new_func  #《---代替了传统的赋值语句
def func01():
    print("我是旧功能")

func01()
```

```python
def verify_permissions(func):  
    def verification(*args): #星号表示可以传递多个参数
        print('验证权限')
        func(*args)         #表示拆分多个参数
    return verification
@verify_permissions
def insert(p1):
    print('插入',p1)
@verify_permissions
def delete(p1,p2):
    print('删除',p1,p2)
insert()
delete()
```

传参情况下使用星号接受多个参数，在调用时星号表示拆分多个参数。【星号元组形参，星号元组实参】关键字实参两星。一个星是使用元组包装，两个星是字典。

### python内存存储机制：

1.引用计数：每个对象记录被变量绑定（引用）的数量，当为0的时候被销毁。缺点：当变量循环引用的时候，会发生内存泄漏。（时刻检测）

2.标记清除：全盘扫描，标记不再使用的数据。缺点：性能低，仅标记。

3.分代回收:程序运行时将内存分为小中大/0/1/2三块，每次创建数据一定在0代分配空间。如果内存告急触发标记清除，将有用的数据升代。升代后释放上代数据（清除）

价值：内存优化（尽少产生垃圾）向列表添加元素不会产生垃圾。对象池，配置垃圾回收（需丰富经验） 。

对象池，每次创建数据时，都先判断池中是否存在相同数据，如果存在直接返回地址，如果不存在则创建新数据。核心数据类型才有对象池。

### 跳转语句：

bareak跳出

continue跳过

return 返回一个参数，退出

yield 返回多个数据，暂时离开

raise 返回错误信息，专用管道，

### 容器类型：增删改查

### 函数参数

实参，形参。*元组类型，**字典类型  位置参数必填，关键字参数有默认值，可填可不填写。星号收集参数，双星收集关键字参数。

### 软件变成三大范式

#### 面向过程

解决文笔从细节步骤出发，

#### 面向对象

语法：创建类，创建对象，实例成员，类成员，静态方法

思想：

封装：划分为多个类（变化点）

继承：抽象变化，统一变化，隔离变化

多态：调用父类一个方法，在不同子类上有不同实现

#### 函数式编程

思想：

封装：划分为多个函数（变化点）

继承：抽象变化，统一变化，隔离变化 参数

多态：lamabda表达式

面向过程中有常使用的算法类函数较为适合使用函数式编程，解决结构式问题，使用面向对象，三大范式相辅相成。

### 项目文件

数据访问层

业务逻辑层

用户显示层

执行层

## LINUX

### 根目录

etc 配置文件

usr 存储用户文件，安装的文件

终端快捷键：

打开：ctrl+alt+T

关闭：ctrl+d

### 常用命令

移动：mv

复制：cp

创建文件：touch

删除：rm

查看文件内容cat

创建目录 mkdir

cat 查看全部    适合查看比较少的内容

more  查看文件80%

wc 查看文件数

grep 搜索文件内容

gedit 打开文件

echo 打印

关机重启：shutdown  -r

### 权限：

sodu  管理员权限

chmod  +/-rwx  文件名|目录

chmod -R 755 文件名|目录名 设置拥有者/组/其他用户权限

执行文件./文件名

输出重定向：">"文件存在则重写文件不存在则创建     ">>" 在文件末尾添加

| 管道命令

### vim （麻烦）：

vi  文件名 

i 写入模式 

esc 返回命令模式

w 保存

q 退出



### 用户操作

需要管理员权限，组名用户名，英文字母小写

添加组 groupadd

删除组 groupdel

添加用户 useradd  -m 用户 -g 组

设置密码 passwd 

设置管理员权限：修改配置文件vi  /etc/sudoers   强制保存退出

配置命令解释器： etc/passwd    用户默认sh 更改bash

切换用户：su

删除用户 useradal





### 查看和配置网卡信息

ifconfig    查看网卡信息

ping   

### ssh

ssh 用户名@地址

exit 退出

scp 远程拷贝文件的命令

使用大写-P

从本地拷贝到服务器：scp -P 端口号 01.py当前目录  user@ip：去向文件目录

从服务器拷贝到本地：scp -P 端口号  user@ip：文件目录  复制到当前目录下



### 免密登录：

ssh-keygen 生成公钥

home/.ssh  下有公钥和私钥。

密钥对访问：2边都有.ssh文件  服务器内authorized_keys,个人计算机公钥拷贝到这个文件夹

ssh-copy-id -p 端口号  user@ip



### 系统相关：

date 查看系统时间

df -h查看磁盘空间  

du -h查看目录下文件

whoami 查看当前用户

### 进程：

ps  -aux 

![image-20220704015947627](C:\Users\43549\AppData\Roaming\Typora\typora-user-images\image-20220704015947627.png)

top

kill 进程id    强行终止加-9

查找文件：

find -name 条件

START:S表示阻塞态  R运行态

### 软连接：（快捷方式）

ln -s 被连接的源文件 连接名字

硬连接：约等于复制，内存指向

文件数据和文件名是分开保存的

### 硬连接：

硬连接接连的是指向数据

### 打包和压缩：

![image-20220704021953019](C:\Users\43549\AppData\Roaming\Typora\typora-user-images\image-20220704021953019.png)



- tar -cvf  打包文件.tar  被打包的文件目录

- tar -xvf  解包

- 压缩（增加一个z）：tar -zcvf  打包文件.tar.gz 被打包的文件目录


### bzip2()

- 压缩文件：

- tar -jcvf 打包文件.tar.bz2  被打包的文件目录

- 解压到指定位置：

- tar -jxvf  解压的文件名 -C 解压路径 （目标路径必须存在）

### 软件安装

1. apt安装  sudo apt install

2. 卸载：sudo apt remove --

3. 更新：sudo apt upgrade

4. 删除缓存的软件安装包：sudo apt clean

### 新用户步骤：

- 创建用户—修改配置文件

### 终端启动python服务

1. 在文件第一行增加解释器说明：#！/usr/bin/python3
2. 修改文件权限为可执行
3. 可以创建快捷方式到主文件下，方便执行

## 数据管理

1.打开大文件使用循环读取，效率高，一般采用512，1024..

```
a = open("试试.txt", "r", encoding="utf-8")
while True:
    b=a.read(2)
    if not b:
        break
    print(b,end="")
a.close()
```

#### open函数缓冲区设置：

buffering=-1 系统默认

buffering=1 行缓冲（\n）

对象.flush   主动刷新缓冲

buffering=1024  大于字节刷新缓存，必须二进制打开

文件偏移量：

文件偏移量记录文件操作位置。rw方式打开在文件开头，a在末尾。一次open只能生成一个偏移量。

查看偏移量：obj.tell()

设置偏移量：seek[偏移量，相对位置[0,1,2]] 0相对于开头，1相对于当前位置，2相对于结尾 以字节为单位。

#### 文件管理：

os模块获取文件大小，文件夹下的所有文件名。判断文件是否存在，删除文件。

#### 正则表达式：

【+】【 1到多次】必须出现

 【*】【0到多次】可以没有，没有也会返回空

【 ？】【0到1次】可有可没有

【{3}】出现的次数 \b单词边界 \B非单词边界

![image-20220712212233301](C:\Users\43549\AppData\Roaming\Typora\typora-user-images\image-20220712212233301.png)

贪婪模式：符合匹配规则的下会尽可能多的匹配

非贪婪：符合规则的情况尽可能少的匹配内容

子组括号内？P<name> 给与名称，便于阅读

通过正则替换：re.sub(正则。替换内容，对象，替换数量（默认替换所有)

```
匹配结果替换：
a="alex:1998,sunny:1999"
b=re.sub("\w+","##",a,1)
print(b)

#结果：##:1998,sunny:1999
```

```
匹配结果分割：
a="alex:1998,sunny:1999"
b=re.split("\w+",a,1)
print(b)

#结果['', ':1998,sunny:1999']
```

re模块：findall()  search() match() split() sub() finditer()

## 多进程 multiprocessing

多进程：新的进程称会拷贝原有进程的全部内存空间，LIUX会自动挑选执行；原进程继续执行，新进程也执行。两个程序互不影响。各自执行，无先后。有阻塞的使用多进程；

windows  自上而下全部都会执行

LINUX  LIUX会自动挑选执行

变量生成后也会被拷贝到子进程

```
multiprocessing模块
```

创建进程对象”

```
process（target=函数名，[args=（）位置传参 kwarg=字典 关键字传参] daemod=布尔值 跟随父进程退出）
```

启动进程：

```
p.start
```

等待子进程退出：

```
子进程.join()  参数：最长等待时间   主程序阻塞等待子进程退出。  回收僵尸进程
```

文件操作的变量是向系统发起获得偏移量，父进程打开文件，子进程直接调用。那么他们共用一个偏移量。会造成影响；

#### os模块：

os.getpid() 获得当前进程pid

os.getppid() 获得父进程pid

sys模块：

sys.exit() 退出进程

#### 创建多个子进程：

当父进程退出时，僵尸进程会被回收。让有join时也会回收。start创建进程也会回收（需要一点时间）。不太重要python有自己的回收机制。

创建进程类：

1，继承process

2.重写__init__方法添加自己的属性。使用super加载父类属性 ,重写run函数。start调用时会调用run函数

```python
class MyProcessing(multiprocessing.Process):
    def __init__(self,value=None,fangfa=None):
        self.value=value
        self.fangfa=fangfa
        super().__init__()
    def func(self):
        for i in range(self.value):
            time.sleep(2)
            print("自己进程执行的内容")
    # 运行start 自动执行run方法，作为进程内容；
    def run(self):
        if self.fangfa:
            self.fangfa()
if __name__ == '__main__':
    p=MyProcessing(3)
    p.start()
    a=multiprocessing.Process()
```

阻塞状态下不会占cpu，

进程的三态：就绪态，进行态，等待/阻塞态



### 进程之间通信：

必要性：进程空间独立，资源不共享，此时需要进程间数据传输时就需要特定的手段进行通信。

常用的进程间通信的方法：消息队列，套接字（tcp，udp）等

### QUEUE 消息队列使用/网络通信方式：

通信原理：在内存中开辟空间，建立队列模型，进程通过队列消息存入忙活着总队列取出完成进程间通信。也可以使用网络通信方式传递消息

```
Q=Queue（）  创建队列对象，最多存放消息个数
```

```
q.put(存入的消息)  存消息，存入满了会出现阻塞；
```

```
q.gei() 从队列取出消息，先进先出
```

q.,full() 判断队列是否为满

q.empty（）判断队列是否为空

### 多线程：threading

操作方式与进程一样。

```python
import time
import threading
"""
    线程创建示例
"""
def fun():
    print("我要开始干活了")
    time.sleep(3)
    print("干完了")

def fun2():
    print("我也要开始干活了")
    time.sleep(3)
    print("我也干完了")

t=threading.Thread(target=fun)
t2=threading.Thread(target=fun2)
t.start()
t2.start()
t.join()   等待线程结束
```

线程共享进程内的资源。同步和互斥都是使用阻塞来完成的。

### 同步/互斥 

创造阻塞来控制线程间的执行顺序；

#### 线程EVENT模块:

```
"""
    EVENT创建示例
"""
#set 状态调用不阻塞，unset调用阻塞
e=threading.Event()  #参数最长阻塞时间，到时间返回FALSE，如果被设置set，返回TRUE；
#状态设置
e.wait()
e.set()
# 返回为未设置状态 
e.clear()
#判断状态
e.is_set()
```

```
#set 状态调用不阻塞，unset调用阻塞
"""
    EVENT使用示例
"""
e=threading.Event()  #  创建event对象
a=""
def ce():
    global a
    a = "这是个口号"
    e.set()     #通知主线程可以执行
t=threading.Thread(target=ce)
t.start()
e.wait()        #阻塞等待通知
if a =="这是个口号":
    print("口号正确")
else:
    print("口令错误")
#状态设置
```

#### 线程锁LOCK 互斥方法：

LOCK=LOCK()

lock.acquire() 上锁

lock.release（）解锁     

再次上锁会阻塞等待，上锁后需要等待解锁才能结束阻塞。

#### 避免死锁：互斥条件，请求保持条件，不剥夺条件，环路条件

结论：无阻塞任务使用进程。或不使用C作为解释器。阻塞场景较多的情况下使用多线程会比较合适。

#### 

  

I/O:
