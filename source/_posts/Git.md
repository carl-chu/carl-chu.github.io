---
title: Git
date: 2021-08-06 17:09:13
tags:
categories: 学习笔记
index_img: /img/git-logo.png
---

# Git（分布式版本控制系统）

> 版本控制

+ 实现多人协同开发
+ 追踪和记载一个或多个文件的历史纪录
+ 组织和保护源代码和文档
+ 统计工作量
+ 并行开发，提高工作效率
+ 跟踪记录整个软件的开发过程

> 启动Git

**Git Bash**：Unix与Linux风格的命令行

**Git CMD**：Windows风格的命令行

**Git GUI**：图形界面的Git

> Git配置

查看配置`git config -l`

![gitbash](image-20210629101204736.png)

查看不同级别的配置文件：

```bash
#查看系统config
git config --system --list

#查看当前用户（global）配置
git config --global --list
```

> 设置用户名与邮箱（用户标识）

```bash
#设置全局名称
git config --global user.name "CZH"
#设置全局邮箱
git config --global user.email 519110882@qq.com
```

# Git基本理论

> 工作区域

Git本地有三个工作目录：工作目录（Working Directory）、暂存区（Stage/Index）、资源库（Repository或Git Directory）。如果加上远程的git仓库（Remote Directory）就可以分为四个工作区域。文件在这四个区域的转换关系如下：

![git四个区域的转换关系](3e35ec8c825a6a1c04609de7cd5700d9.png)

+ Workspace：工作区，存放项目代码的地方
+ Index/Stage：暂存区，用于临时存放你的改动，它只是一个文件
+ Repository：仓库区（或本地仓库），就是安全存放数据的位置，这里有提交的所有版本的数据。其中HEAD指向最新放入仓库的版本
+ Remote：远程仓库，托管代码的服务器

> 工作流程

git的一般工作流程：

1. 在工作目录中添加、修改文件。
2. 将需要进行版本管理的文件放入暂存区域。（`git add .`）
3. 将暂存区域的文件提交到git仓库。(`git commit`)

因此，git管理的文件有三种状态：已修改（modified），已暂存（staged），已提交（committed）

![git工作流程](3985563-6b745d5fac15906c.png)

# Git项目搭建

> 创建工作目录与常用指令

![git常用指令和流程](6bf8a06e60bc5d25fc2e9c94fc9280e1.png)

> 本地仓库搭建

创建本地仓库的两种方法：一种是创建全新的仓库，另一种是克隆远程仓库。

1.创建全新的仓库，需要使用GIT管理的项目的根目录执行：

```bash
#在当前目录新建一个Git代码库
$ git init
```

2.执行后可以看到仅仅在项目目录多出了一个.git目录，关于版本等的所有信息都在这个目录里面。

> 克隆远程仓库

克隆远程目录，将远程服务器上的仓库完全镜像一份到本地。

```bash
$ git clone [url]
```



# Git文件操作

> 文件4种状态

+ **Untracked**:未跟踪，此文件在文件夹中，但并没有加入到git库中，不参与版本控制。通过`git add `将状态变为`Staged`。
+ **Unmodify**:文件已经入库，未修改，即版本库中的文件快照内容与文件夹中完全一致。这种类型的文件有两种去处，如果它被修改，则变为`Modified`。如果使用`git rm`移出版本库，则成为`Untracked`文件。
+ **Modified**:文件已修改，仅仅是修改，并没有进行其他的操作。这个文件有两个去处，通过`git add`可进入暂存`staged`状态，使用`git checkout`则丢弃修改，返回到`Unmodify`状态，这个`git checkout`即从库中取出文件，覆盖当前修改。
+ **Staged**:暂存状态，执行`git commit`则将修改同步到库中，这时库中的文件和本地文件又变为一致，文件为`Unmodify`状态。执行`git reset HEAD filename`取消暂存，文件状态为`Modified`。

> 查看文件状态

通过如下命令查看文件状态：

```bash
#查看指定文件状态
git status [filename]

#查看所有文件状态
git status

#添加所有文件到暂存器
git add .

#提交暂存区中的内容到本地仓库 -m 提交信息
git commit -m “消息内容”
```

> 忽略文件

有时不需要把某些文件纳入版本控制，比如前端node_modules、数据库文件、临时文件、设计文件等。

在主目录下建立“.gitignore”文件，此文件有如下规则：

1. 忽略文件中的空行或以井号（#）开始的行将会被忽略。
2. 可以使用Linux通配符。例如：星号（*）代表任意多个字符，问号（？）代表一个字符，方括号（[abc]）代表可选字符范围，大括号（{string1，string2，...}）代表可选的字符串等。
3. 如果名称的最前面有一个感叹号（！），表示例外规则，将不被忽略。
4. 如果名称的最前面是一个路径分隔符（/），表示要忽略的文件在此目录下，而子目录中的文件不忽略。
5. 如果名称的最后面是一个路径分隔符（/），表示要忽略的是此目录下该名称的子目录，而非文件（默认文件或目录都忽略）。

```bash
*.txt     #忽略所有.txt结尾的文件
!lib.txt  #但lib.txt除外
/temp        #仅忽略项目根目录下的TODO文件,不包括其它目录temp
build/       #忽略build/目录下的所有文件
doc/*.txt    #会忽略 doc/notes.txt 但不包括doc/server/arch.txt
```

# Git分支

![git分支](1c8d9b661018ac17dfb6522d774360d7.png)

git分支中的常用指令

```bash
#列出所有本地分支
git branch

#列出所有远程分支
git branch -r

#新建一个分支，但依然停留在当前分支
git branch [branch-name]

#切换分支
git checkout [branch-name]

#新建一个分支，并切换到该分支
git checkout -b [branch]

#合并指定分支到当前分支
git merge [branch]

#使用^向上移动1个提交记录
git checkout master^  #git checkout HEAD^

#使用~<num>向上移动多个提交记录
git checkout HEAD~4

#强制修改分支位置
git branch -f master HEAD~3  #将master分支强制指向HEAD的第3级父提交

#把分支记录回退几个提交记录实现撤销改动(仅对本地分支有效)
#reset(撤回到某个记录)
git reset HEAD~1

#远程分支撤销
#revert(撤回某个记录)
git revert HEAD

#复制提交记录到当前分支
git cherry-pick c2 c4 #将c2和c4复制到当前分支

#删除分支
git branch -d [branch-name]

#删除远程分支
git push origin --delete [branch-name]
git branch -dr [remote/branch]
```

