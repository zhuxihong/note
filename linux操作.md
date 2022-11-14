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

### vim 编辑模式（麻烦）：

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

ps 

![image-20220704015947627](C:\Users\43549\AppData\Roaming\Typora\typora-user-images\image-20220704015947627.png)

top

kill 进程id    强行终止加-9

查找文件：

find -name 条件

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

- 创建用户—修改配置文件，默认用户的命令行要改一下；

### 终端启动python服务

1. 在文件第一行增加解释器说明：#！/usr/bin/python3
2. 修改文件权限为可执行
3. 可以创建快捷方式到主文件下，方便执行
