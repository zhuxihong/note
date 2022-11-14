# 1. 软件项目开发

### 1.1 软件项目开发流程

需求分析----》概要设计 ---》项目计划 --->详细设计 ---》编码测试 ---》项目测试 ---》调试修改 --》项目发布 ---》后期维护

### GIT:

工作区，暂存区，本地仓库，远程仓库 （别人计算机上的）

安装：

```
sudo apt install git
```

配置用户名和邮箱：

```
git config --global user.name zhuxihong

git config --global user.email 435498423@qq.com
```

初始化git仓库：

```
sudo apt install git
```

查看状态

```
git remote add origin git@gitee.com:zhuxihong/aid2002.git

```

添加跟踪文件：

```
git add 文件名
```

移出暂存区

```
git rm --cached  <文件>.
```

保存到仓库

```
git commit -m '第一次提交的内容'
```

所有操作都必须在仓库目录下，

文件非项目文件又要使用，可以添加不管理创建【.gitignore】文件到仓库下。把文件类型添加进去

```
git reset --hard  回滚
```

```
git reflog 查看所有操作
```

创建标签:

```
git tag  [标签名] [commit_id] -m “备注名”    
```

删除标签：

```
git tag -d
```

分支：

分支：

```
git branch   【后面加名称创建分支】
```

切换分支

```
git checkout 分支名称
```

```
git merge 分支名称  合并分支
```

克隆项目：

```
git clone 项目地址
```

```
git push --force  强推
```

```
git push -u 地址 新分支名称
```

