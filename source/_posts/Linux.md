---
title: Linux
date: 2023-04-11 15:18:00
tags: Linux
categories: 学习笔记
index_img: /img/linux.png
---

Linux系统中一切皆文件

# Linux目录结构

- /bin（谨慎修改）
  - binery，放常用命令
- /boot（勿动）
  - boot分区（引导分区）
- /dev（勿动）
  - device，设备目录
- /etc（勿动）
  - 系统管理所需的配置文件
- /home（随便动）
  - 普通用户的主目录
- /lib（勿动）
  - library（依赖），库文件，类似system32
- /lib64（勿动）
  - 同样是依赖，类似system
- /media（勿动）
  - 媒体目录（可移动设备挂载点），识别可移动媒体设备，如U盘、光驱等，将其挂在到此目录中
- /mnt（勿动）
  - mount，移动设备的另一个挂载点
- /opt（随便动）
  - optional（可选目录），给第三方软件包安装的位置
- /proc（勿动）
  - process（进程目录，一个虚拟目录），存放系统进程信息
- /root（随便动）
  - 超级管理员的主目录
- /run（勿动）
  - 运行目录，系统运行期间所有运行信息，重启就没了
- /sbin（谨慎修改）
- /srv（勿动）
  - service，系统服务相关信息
- /sys（勿动）
  - system，系统硬件信息的相关文件
- /tmp（随便动）
  - temp（临时目录），可动
- /usr（勿动）
- /var（可以动）
  - varible（可变目录），一般存放不断扩充会变化的文件，如日志



可以随意使用的目录`/root`,`/home`,`/opt`,`/tmp`,`/var`

可能需要自行修改的目录`/bin`,`/etc`



# vim编辑器

文本编辑器

1.命令：`vim 目标文件`进入一般模式（主要操作：删除、复制、粘贴）

2.按`i`进入编辑模式，按`esc`退出编辑模式进入一般模式

3.输入`:`进入命令模式，`:w`保存，`:q`退出vim，`:wq`保存并退出，`u`撤销

## 普通模式

常用语法

| 语法             | 功能                                              |
| ---------------- | ------------------------------------------------- |
| yy               | 复制光标当前1行                                   |
| y数字y           | 复制一段（从光标当前行到后n行）                   |
| p                | 箭头移动到目的行粘贴                              |
| u                | 撤销上一步                                        |
| dd               | 删除光标当前行                                    |
| d数字d           | 删除光标（含）后多少行                            |
| x                | 剪切一个字符（当前光标），相当于del               |
| X                | 剪切一个字符（当前光标的前一个），相当于backspace |
| yw               | 复制一个词                                        |
| dw               | 删除一个词                                        |
| shift+^          | 光标移动到行头                                    |
| shift+$          | 光标移动到行尾                                    |
| d + shift + ^    | 删除光标移动到行头                                |
| d+shift+$        | 删除光标移动到行尾                                |
| y+shift+^        | 复制光标移动到行头                                |
| y+shift+$        | 复制光标移动到行尾                                |
| b                | 光标移动到单词头                                  |
| e                | 光标移动到单词尾                                  |
| gg  或 shift + h | 光标移动到文件头                                  |
| G 或 L           | 光标移动到文件尾                                  |

`:set nu` 显示行号，`:set nonu`关闭行号显示

## 编辑模式

1. 进入编辑模式常用语法

| 按键 | 功能               |
| ---- | ------------------ |
| i    | 当前光标前         |
| a    | 当前光标后         |
| o    | 当前光标行的下一行 |
| I    | 光标所在行最前     |
| A    | 光标所在行最后     |
| O    | 当前光标行的上一行 |

2. 退出编辑模式

按esc键

## 命令模式

基本语法

| 命令          | 功能                                 |
| ------------- | ------------------------------------ |
| :w            | 保存                                 |
| :q            | 退出                                 |
| :wq           | 保存并退出                           |
| :q!           | 不保存强制退出                       |
| /要查找的词   | n查找下一个，N往上查找               |
| :noh          | 取消高亮显示                         |
| :set nu       | 显示行号                             |
| :set nonu     | 关闭行号                             |
| :s/old/new    | 光标所在行中的第一个"old"替换为"new" |
| :s/old/new/g  | 光标所在行中所有"old"替换为"new"     |
| :%s/old/new   | 将每行中的第一个"old"替换为"new"     |
| :%s/old/new/g | 将所有"old"替换为"new"               |

# 网络配置和系统管理操作

VMware提供了三种网络连接模式：

桥接模式：

虚拟机直接连接外部物理网络的模式，主机起到了网桥的作用。这种模式下，虚拟机可以直接访问外部网络，并且对外部网络是可见的。

NAT模式：

虚拟机和主机构建一个专用网络，并通过虚拟网络地址转换（NAT）设备对IP进行转换。虚拟机通过共享主机IP可以访问外部网络，但外部网络无法访问虚拟机。

仅主机模式：

虚拟机只与主机共享一个专用网络，与外部网络无法通信。



## 修改主机名

hostname（查看当前服务器的主机名称）

修改主机名`vi /etc/hostname`重启后生效





# service服务管理

基本语法

centos 6：

`service 服务名 start | stop | restart | status`

比如：service network status



centos7：（重要）

`systemctl start | stop | restart | status 服务名`

比如：systemctl status firewalld



在终端使用命令`setup`进入图形化界面配置开机自启动服务，有*号表示开机自启动，按空格(space)选中或取消选中，按`tab`跳转按钮



## 运行级别

centos6运行级别：

Linux有7种运行级别(runlevel)：常用的是级别3和5

- 运行级别0：系统停机状态，系统默认运行级别不能设为0，否则不能正常启动
- 运行级别1：单用户工作状态，root权限，用于系统维护，禁止远程登陆（类似windows安全模式）
- 运行级别2：多用户状态（没有NFS），不支持网络
- 运行级别3：完全的多用户状态（有NFS），登陆后进入控制台命令行模式。（常用）
- 运行级别4：系统未使用，保留
- 运行级别5：X11控制台，登录后进入图形GUI模式。（常用）
- 运行级别6：系统正常关闭并重启，默认运行级别不能设为6，否则不能正常启动。



centos7运行级别进行了简化：

multi-user.target等价于原运行级别3（多用户有网，无图形界面）

graphical.target等价于原运行级别5（多用户有网，有图形界面）

查看系统默认运行级别：`systemct get-default`

设置系统运行级别：system set-default graphical.target



查看所有自启动服务`systemctl list-unit-files`

# 关机重启

基本语法：

1. sync	（功能描述：将数据由内存 同步到硬盘中）

2. halt       （功能描述：停机，关闭系统，但不断电）
3. poweroff  （功能描述：关机，停电）
4. reboot       （功能描述：就是重启，等同于shutdown -r now）
5. shutdown [选项] 时间



关机指令：

`shutdown`（默认1分钟后关机，可以使用命令`shutdown -c`取消）

`shutdown now`立刻关机

`shutdown 3`3分钟后关机

`shutdown 15:28` 15时28分关机（定时关机）



# 常用基本命令（重要）

## 帮助命令

### 1.man (manual)获得帮助信息

基本语法：

man [命令或配置文件]				（功能描述：获得帮助信息）



### 2.help获得shell内置命令的帮助信息

​	一部分基础功能的系统命令是直接内嵌在shell中的，系统加载启动之后会随着shell一起加载，常驻系统内存中。这部分命令被称为“内置(built-in)命令”；相应的其他命令被称为“外部命令”。

基本语法：help 命令		（功能描述：获得shell内置命令的帮助信息）

查看cd命令的帮助信息：help cd



使用`type`命令查看命令类型



### 3.常用快捷键

ctrl + c：退出当前进程

ctrl + l：清屏

tab键：提示

上下键：查找执行过的命令

## 文件目录类

### 1.pwd显示当前工作目录的绝对路径

pwd:print working directory 打印工作目录

### 2.ls列出目录的内容

ls:list 列出目录内容

参数: 

- -a 全部文件（包括隐藏文件，即以.开头的文件）
- -l 长数据串列出，包含文件的属性与权限等等数据，等价于`ll`命令

### 3.cd切换目录

cd:change directory

cd：进入当前用户的主目录

cd -：回到上一次所在的目录

### 4.mkdir创建一个新的目录

### 5.rmdir删除一个空的目录

### 6.touch创建空文件

### 7.cp复制文件或目录

### 8.rm删除文件或目录

### 9.mv移动文件与目录或重命名

### 10.cat查看文件内容

查看文件内容，从第一行开始显示

基本语法：cat [选项] 要查看的文件

cat -n a.txt

### 11.more文件内容分屏查看器

基本语法：more 要查看的文件

操作说明：

| 操作            | 功能说明                             |
| --------------- | ------------------------------------ |
| 空白键（space） | 代表向下翻一页                       |
| Enter           | 代表向下翻一行                       |
| q               | 代表立刻离开more，不再显示该文件内容 |
| Ctrl + F        | 向下滚动一屏                         |
| Ctrl + B        | 返回上一屏                           |
| =               | 输出当前行的行号                     |
| :f              | 输出文件名和当前行的行号             |



### 12.less分屏显示文件内容

less指令用来分屏查看文件内容，功能与more类似，但是比more指令更加强大，支持各种显示终端。less指令在显示文件内容时，并不是一次将整个文件加载之后才显示，而是根据显示需要加载的内容，对于显示大型文件具有较高的效率。

基本语法：less 要查看的文件

操作说明：

| 操作       | 功能说明                                         |
| ---------- | ------------------------------------------------ |
| 空白键     | 向下翻动一页                                     |
| [pagedown] | 向下翻动一页                                     |
| [pageup]   | 向上翻动一页                                     |
| /字串      | 向下搜寻【字串】的功能；n：向下查找；N：向上查找 |
| ?字串      | 向上搜寻【字串】的功能；n：向上查找；N：向下查找 |



### 13.echo

echo输出内容到控制台

基本语法：echo [选项] [输出内容]

选项：

-e：支持反斜线控制的字符转换

| 控制字符 | 作用      |
| -------- | --------- |
| \\\      | 输出\本身 |
| \n       | 换行符    |
| \t       | 制表符    |

输出所有系统环境变量：echo $ (按tab，不要按回车)

查看某个环境变量：echo $PATH

### 14.head显示文件头部内容

head用于显示文件的开头部分内容，默认情况下head指令显示文件的前10行内容

### 15.tail输出文件尾部内容

tail用于显示文件尾部的内容，默认情况下tail指令显示文件的后10行内容

基本语法

1. tail 文件
2. tail -n 5 文件          （功能描述：查看文件尾部5行内容，5可以是任意行数）
3. tail -f 文件               （功能描述：实时追踪该文档的所有更新，f即follow）

ctrl + s暂停监听，ctrl+q继续监听



### 16.>输出重定向和>>追加

基本语法：

1. ls -l > 文件						（功能描述：列表的内容写入文件a.txt（覆盖写））
2. ls -al >> 文件               （功能描述：列表的内容追加到文件aa.txt的末尾）
3. cat 文件1 > 文件2        （功能描述：将文件1的内容覆盖到文件2）
4. echo "内容" >> 文件     （功能描述：将“内容”追加到文件）

echo $HOSTNAME >> info

### 17.ln软连接

​	软连接也称为符号链接，类似于windows里的快捷方式，有自己的数据块，主要存放了链接其他文件的路径

基本语法：ln -s [原文件或目录] [软链接名]      	（功能描述：给原文件创建一个软连接）

**不使用-s参数是创建硬链接！！！**

经验技巧：

删除软链接：`rm -rf 软链接名`，而不是`rm -rf 软链接名/`，如果使用`rm -rf 软链接名/`删除，会把软链接对应的真实目录下内容删掉。

### 18.history查看已经执行过的历史命令

history显示输入过的命令

使用`![执行history命令后显示的对应命令的行数]`可以执行历史命令

history -c 清理历史记录

## 时间日期类

### 1.date显示当前时间

1. date					（功能描述：显示当前时间）
2. date +%Y         （功能描述：显示当前年份）
3. date +%m         （功能描述：显示当前月份）
4. date +%d         （功能描述：显示当前是哪一天）
5. date +"%Y-%m-%d %H:%M:%S"         （功能描述：显示年月日时分秒）

%s表示时间戳

需要把+写上

### 2.date显示非当前时间

### 3.date设置系统时间

### 4.cal查看日历

## 用户管理命令

### 1.useradd添加新用户

基本语法：

`useradd 用户名`                        （功能描述：添加新用户）

`useradd -g 组名 用户名`			（功能描述：添加新用户到某个组）

### 2.passwd设置用户密码

### 3.id查看用户是否存在

`id czh`



### 4.cat /etc/passwd 查看创建了哪些用户

### 5.su切换用户

### 6.userdel删除用户

`userdel czh`会删除掉此用户，但不会删除此用户的主目录，如果要删除则需要另外删除`rm -rf czh`

如果要删除此用户及其主目录，则加上参数-r

### 7.who查看登录用户信息

`who am i`查看最外层用户名（在使用su切换用户后相当于会话间的嵌套）

`whoami`查看当前会话的用户名

### 8.sudo设置普通用户具有root权限

若普通用户想要查看/root目录中的文件则使用命令`sudo ls`，然后输入当前用户的密码。

但是前提是root用户在/etc/sudoers文件中赋予了当前用户权限。

`vim /etc/sudoers`

修改/etc/sudoers文件，在root下面添加一行

```
root	ALL=(ALL)		ALL
czh		ALL=ALL			ALL
```

或者配置成采用sudo命令时，不需要输入密码

```
root	ALL=(ALL)		ALL
czh		ALL=ALL			NOPASSWD:ALL
```



### 9.usermod修改用户

基本语法

usermod -g 用户组 用户名

## 用户组管理命令

### 1.groupadd新增组

### 2.groupdel删除组

### 3.groupmod修改组

### 4.cat /etc/group查看创建了哪些组

## 文件权限类

### 1.文件属性

Linux系统是一种典型的多用户系统，不同的用户处于不同的地位，拥有不同的权限。为了保护系统的安全性，Linux系统对不同的用户访问同一文件（包括目录文件）的权限做了不同的规定。在Linux中我们可以使用ll或者ls -l 命令来显示一个文件的属性以及文件所属的用户和组。

从左到右10个字符表示：

| 文件类型 | ←    | 属主权限（u） | →    | ←    | 属组权限（g） | →    | ←    | 其他用户权限（o） | →    |
| -------- | ---- | :------------ | ---- | ---- | ------------- | ---- | ---- | ----------------- | ---- |
| 0        | 1    | 2             | 3    | 4    | 5             | 6    | 7    | 8                 | 9    |
| d        | r    | w             | x    | r    | -             | x    | r    | -                 | x    |
| 文件目录 | 读   | 写            | 执行 | 读   | 写            | 执行 | 读   | 写                | 执行 |

文件类型：

- -：文件
- d：目录
- c：字符设备文件（如键盘）
- b：块设备文件（如硬盘）

属主权限（u）：创建者（该文件的所有者）拥有的权限

属组权限（g）：文件所属的组用户（所有者的同组用户）拥有的权限

其他用户权限（o）：其他用户的权限

#### rwx作用文件和目录的不同解释

（1）作用到文件：

- r：代表可读：可以读取，查看
- w：代表可写：可以修改，但是不代表可以删除该文件，删除一个文件的前提条件是对该文件所在的目录有写权限，才能删除该文件。
- x：代表可执行：可以被系统执行

（2）作用到目录：

- r：代表可读：可以读取，ls查看目录内容
- w：代表可写：可以修改，目录内创建+删除+重命名目录
- x：代表可执行：可以进入该目录

```shell
-rw-------. 1 root root 1837 10月  2 17:27 anaconda-ks.cfg
-rw-r--r--. 1 root root 1885 10月  2 17:28 initial-setup-ks.cfg
drwxr-xr-x. 2 root root   34 10月  7 17:06 test
drwxr-xr-x. 2 root root    6 10月  2 17:29 公共
drwxr-xr-x. 2 root root    6 10月  2 17:29 模板
drwxr-xr-x. 2 root root    6 10月  2 17:29 视频
drwxr-xr-x. 2 root root    6 10月  2 17:29 图片
drwxr-xr-x. 2 root root    6 10月  2 17:29 文档
drwxr-xr-x. 2 root root    6 10月  2 17:29 下载
drwxr-xr-x. 2 root root    6 10月  2 17:29 音乐
drwxr-xr-x. 2 root root    6 10月  7 17:05 桌面
```

权限后的数字表示**链接数**：

- 如果查看的是文件：链接数指的是硬链接个数
- 如果查看的是文件夹：链接数指的是子文件夹个数

第一个root表示属主，第二个root表示属组，之后的数字表示文件大小。 

### 2.chmod改变权限

第一种方式变更权限：

chmod [{ugoa} {+-=} {rwx}] 目录或文件，`chmod g=rw`，`chmod g+r`

+表示增加哪个权限，-表示减少哪个权限

第二种方式变更权限：

chmod [mode=421] [文件或目录]

r=4, w=2, x=1

如果修改目录权限希望该目录下所有文件或文件夹都有相同权限，则使用参数-R



### 3.chown改变所有者

基本语法：chown [选项] [最终用户] [文件或目录]		（功能描述：改变文件或者目录的所有者）

### 4.chgrp改变所属组

## 搜索查找类（常用）

### 1.find查找文件或者目录

find指令将从指定目录向下递归地遍历其各个子目录，将满足条件的文件显示在终端

基本语法：find [搜索范围] [选项]

选项说明：

| 选项            | 功能                                                         |
| --------------- | ------------------------------------------------------------ |
| -name<查询方式> | 按照指定的文件名查找模式查找文件                             |
| -user<用户名>   | 查找属于指定用户名所有文件                                   |
| -size<文件大小> | 按照指定的文件大小查找文件，单位为：b-块（512字节），c-字节，w-字（2字节），k-千字节，M-兆字节，G-G字节 |

示例：`find /root -name "*.cfg"`,`find /root -size +10M`（大于等于10M）

### 2.locate快速定位文件路径

locate指令利用事先建立的系统中所有文件名称及路径的locate数据库实现快速定位给定的文件。Locate指令无需遍历整个文件系统，查询速度较快。为了保证查询结果的准确性，管理员必须定期更新locate时刻

基本语法：locate 搜索文件

经验技巧：由于locate指令基于数据库进行查询，所以第一次运行前，必须使用updatedb指令创建locate数据库。

案例实操：

```bash
[root@study ~]# updatedb
[root@study ~]# locate tmp
```



查找命令在哪`which ls`，`whereis ls`

### 3.grep过滤查找及“|”管道符

管道符，“|”，表示将前一个命令的处理结果输出传递给后面的命令处理

基本语法：grep 选项 查找内容 源文件

选项说明：

| 选项 | 功能             |
| ---- | ---------------- |
| -n   | 显示匹配行及行号 |

查找某文件在第几行

`ls|grep -n test`

查找boot在initial-setup-ks.cfg中第几行

`grep -n boot initial-setup-ks.cfg`

`grep -n boot initial-setup-ks.cfg | wc`(wc:word count，输出结果为： 几行出现 总个数  总字节大小)

## 压缩解压类

### 1.gzip/gunzip压缩

基本语法：

gzip 文件				（功能描述：压缩文件，只能将文件压缩为*.gz文件）

gunzip 文件.gz		（功能描述：解压文件命令）

经验技巧：

1. 只能压缩文件，不能压缩目录
2. 不会保留原来的文件
3. 同时多个文件会产生多个压缩包

### 2.zip/unzip压缩

基本语法：

zip [选项] XXX.zip 将要压缩的内容			（功能描述：压缩文件和目录的命令）

unzip [选项] XXX.zip 			（功能描述：解压缩文件）

选项说明：

| zip选项 | 功能     |
| ------- | -------- |
| -r      | 压缩目录 |

| unzip选项 | 功能                     |
| --------- | ------------------------ |
| -d<目录>  | 指定解压后文件存放的目录 |

经验技巧：

zip压缩命令在windows/linux都通用，可以压缩目录且保留源文件

`zip -r myroot.zip /root`

`unzip -d /tmp myroot.zip`

### 3.tar打包

基本语法：

tar [选项] XXXX.tar.gz 将要打包进去的内容		（功能描述：打包目录，压缩后的文件格式.tar.gz）

选项说明：

| 选项 | 功能               |
| ---- | ------------------ |
| -c   | 产生.tar打包文件   |
| -v   | 显示详细信息       |
| -f   | 指定压缩后的文件名 |
| -z   | 打包同时压缩       |
| -x   | 解包.tar文件       |
| -C   | 解压到指定目录     |

打包

`tar -zcvf test.tar.gz test.txt`



解包

`tar -zxvf test.tar.gz -C /tmp`

## 磁盘查看和分区类

### 1.du查看文件和目录占用的磁盘空间

du即disk usage 磁盘占用情况

基本语法：`du 目录/文件`	（功能描述：显示目录下每个子目录的磁盘使用情况）

选项说明：

| 选项          | 功能                                                 |
| ------------- | ---------------------------------------------------- |
| -h            | 以人们较易阅读的GBytes，MBytes，KBytes等格式自行显示 |
| -a            | 不仅查看子目录大小，还要包括文件                     |
| -c            | 显示所有的文件和子目录大小后，显示总和               |
| -s            | 只显示总和                                           |
| --max-depth=n | 指定统计子目录的深度为第n层                          |

案例实操：

查看当前用户主目录占用的磁盘空间大小：`du -sh`

只显示当前一级目录子目录及子文件的大小：`du --max-depth=1 -ah`

### 2.df查看磁盘空间使用情况

df即disk free 空余磁盘

基本语法：`df 选项`	（功能描述：列出文件系统的整体磁盘使用量，检查文件系统的磁盘空间占用情况）

选项说明：

| 选项 | 功能                                                 |
| ---- | ---------------------------------------------------- |
| -h   | 以人们较易阅读的GBytes，MBytes，KBytes等格式自行显示 |

案例实操：

查看磁盘使用情况：`df -h`

使用`free`命令查看物理内存和虚拟内存使用情况：`free -h`

### 3.lsblk查看设备挂载情况

lsblk即list block （功能描述：查看设备挂载情况）

选项说明：

| 选项 | 功能                                     |
| ---- | ---------------------------------------- |
| -f   | 查看详细的设备挂载情况，显示文件系统信息 |



### 4.mount/umount 挂载/卸载

对于Linux用户来讲，不论有几个分区，分别分给哪一个目录使用，它总归就是一个根目录、一个独立且唯一的文件结构。

Linux中每个分区都是用来组成整个文件系统的一部分，它在用一种叫做“挂载”的处理方法，它整个文件系统中包含了一整套的文件和目录，并将一个分区和一个目录联系起来，要载入的那个分区将使它的存储空间在这个目录下获得。

基本语法：

`mount [-t vfstype] [-o options] device dir`	（功能描述：挂载设备）

`umount 设备文件名或挂载点`						（功能描述：卸载设备）

参数说明：

| 参数       | 功能                                                         |
| ---------- | ------------------------------------------------------------ |
| -t vfstype | 指定文件系统的类型，通常不必指定。mount会自动选择正确的类型。常用类型有：<br>光盘或光盘镜像：iso9660<br />DOS fat16文件系统：msdos<br />Windows 9x fat32 文件系统：vfat<br />Windows NT ntfs 文件系统：ntfs<br />Mount Windows文件网络共享：smbfs<br />UNIX(LINUX) 文件网络共享：nfs |
| -o options | 主要用来描述设备或归档的挂接方式。常用参数有：<br />loop：用来把一个文件当成硬盘分区挂接上系统<br />ro：采用只读方式挂接设备<br />rw：采用读写方式挂接设备<br />iocharset：指定访问文件系统所用字符集 |
| device     | 要挂接(mount)的设备                                          |
| dir        | 设备在系统上的挂载点(mount point)                            |

```bash
mkdir /mnt/cdrom
mount /dev/cdrom /mnt/cdrom/
umount /dev/cdrom
```

### 5.fdisk分区

基本语法：

`fdisk -l`	（功能描述：查看磁盘分区详情）

`fdisk 硬盘设备名`	（功能描述：对新增硬盘进行分区操作）

选项说明：

| 选项 | 功能                   |
| ---- | ---------------------- |
| -l   | 显示所有硬盘的分区列表 |

经验技巧：该命令必须在root用户下才能使用



添加硬盘后先使用`fdisk`进行分区，再使用`mkfs`命令进行格式化设置文件系统

`mkfs -t xfs /dev/sdb1`

`mount /dev/sdb1 /home/czh/`（进行挂载）

## 进程管理类（重要）

进程是正在执行的一个程序或命令，每一个进程都是一个运行的实体，都有自己的地址空间，并占用一定的系统资源

### 1.ps查看当前系统进程状态

ps即process status进程状态

基本语法：

`ps aux | grep xxx`			（功能描述：查看系统中所有进程）

`ps -ef | grep xxx`			（功能描述：可以查看子父进程之间的关系）

选项说明：

| 选项 | 功能                                       |
| ---- | ------------------------------------------ |
| a    | 列出带有终端的所有用户的进程               |
| x    | 列出当前用户的所有进程，包括没有终端的进程 |
| u    | 面向用户友好的显示风格                     |
| -e   | 列出所有进程                               |
| -u   | 列出某个用户关联的所有进程                 |
| -f   | 显示完整格式的进程列表                     |

（不带-的选项是UNIX风格，带-的选项是BSD风格）

功能说明：

ps aux显示信息说明

USER：该进程是由哪个用户产生的

PID：进程的ID号

%CPU：该进程占用CPU资源的百分比，占用越高，进程越耗费资源；

%MEM：该进程占用物理内存的百分比，占用越高，进程越耗费资源；

VSZ：该进程占用虚拟内存的大小，单位KB；

RSS：该进程占用实际物理内存的大小，单位KB；

TTY：该进程是在哪个终端中运行的。对于CentOS来说，tty1是图形化终端，tty2-tty6是本地的字符界面终端。pts/0-255代表虚拟终端。?表示后台

STAT：进程状态。常见的状态有：R：运行状态、S：睡眠状态、T：暂停状态、Z：僵尸状态、s：包含子进程、l：多线程、+：前台显示

START：该进程的启动时间

TIME：该进程占用CPU的运算时间，注意不是系统时间

COMMAND：产生此进程的命令名



经验技巧：

如果想查看进程的CPU占用率和内存占用率，可以使用aux，

如果想查看进程的父进程ID可以使用 -ef



### 2.kill终止进程

基本语法：

`kill [选项] 进程号`			（功能描述：通过进程号杀死进程）

`killall 进程名称`					（功能描述：通过进程名称杀死进程，也支持通配符，这在系统因负载过大而变得很慢时很有用）（谨慎使用）

选项说明：

| 选项 | 功能                 |
| ---- | -------------------- |
| -9   | 表示强迫进程立即停止 |



### 3.pstree查看进程树

基本语法：

`pstree [选项]`

选项说明：

| 选项 | 功能               |
| ---- | ------------------ |
| -p   | 显示进程的PID      |
| -u   | 显示进程的所属用户 |

### 4.top实时监控系统进程状态

基本命令：

`top [选项]`

选项说明：

| 选项    | 功能                                                         |
| ------- | ------------------------------------------------------------ |
| -d 秒数 | 指定top命令每隔几秒更新。默认是3秒在top命令的交互模式当中可以执行的命令 |
| -i      | 使top不显示任何闲置或者僵死进程                              |
| -p      | 通过指定监控进程ID来仅仅监控某个进程的状态                   |

操作说明：

| 操作      | 功能                          |
| --------- | ----------------------------- |
| P（大写） | 以CPU使用率排序，默认就是此项 |
| M         | 以内存使用率排序              |
| N         | 以PID排序                     |
| q         | 退出top                       |
| u         | 显示某个用户的进程            |
| k         | 杀死某个进程                  |



### 5.netstat显示网络统计信息和端口占用信息

基本语法：

`netstat -anp | grep 进程号`  	（功能描述：查看该进程网络信息）

`netstat -nlp | grep 端口号`	（功能描述：查看网络端口号占用情况）

选项说明：

| 选项 | 功能                                                 |
| ---- | ---------------------------------------------------- |
| -a   | 显示所有正在监听（listen）和未监听的套接字（socket） |
| -n   | 拒绝显示别名，能显示数字的全部转化为数字             |
| -l   | 仅列出在监听的服务状态                               |
| -p   | 表示显示哪个进程在调用                               |

案例实操：

通过进程号查看sshd进程的网络信息：`netstat -anp | grep sshd`

## crontab系统定时任务

### 1.crontab服务管理

重新启动crond服务

`systemctl restart crond`

### 2.crontab定时任务设置

基本语法：

`crontab [选项]`

选项说明：

| 选项 | 功能                          |
| ---- | ----------------------------- |
| -e   | 编辑crontab定时任务           |
| -l   | 查询crontab任务               |
| -r   | 删除当前用户所有的crontab任务 |

（1）参数说明

使用`crontab -e`命令进入crontab编辑界面，会打开vim编辑你的工作。

\* \* \* \* \*执行的任务

| 项目      | 含义                 | 范围                  |
| :-------- | -------------------- | --------------------- |
| 第一个“*” | 一小时当中的第几分钟 | 0-59                  |
| 第二个“*” | 一天当中的第几小时   | 0-23                  |
| 第三个“*” | 一个月当中的第几天   | 1-31                  |
| 第四个“*” | 一年当中的第几月     | 1-12                  |
| 第五个“*” | 一周当中的星期几     | 0-7（0和7都代表周日） |

（2）特殊符号

| 特殊符号 | 含义                                                         |
| -------- | ------------------------------------------------------------ |
| *        | 代表任何时间。比如第一个*就代表一小时中每分钟都执行一次的意思 |
| ,        | 代表不连续的时间。比如“0 8,12,16 * * * 命令”，就代表在每天的8点0分，12点0分，16点0分都执行一次命令 |
| -        | 代表连续的时间范围。比如“0 5 * * 1-6”，代表在周一到周六的凌晨5点0分执行命令。 |
| */n      | 代表每隔多久执行一次。比如“*/10 * * * * 命令”代表每隔10分钟就执行一遍命令。 |

（3）特定时间执行命令

| 时间              | 含义                                                         |
| ----------------- | ------------------------------------------------------------ |
| 45 22 * * * 命令  | 每天22点45分执行命令                                         |
| 0 17 * * 1 命令   | 每周一的17点0分执行命令                                      |
| 0 5 1,15 * * 命令 | 每月1号和15号的凌晨5点0分执行命令                            |
| */10 4 * * * 命令 | 每天凌晨4点，每隔10分钟执行一次命令                          |
| 0 0 1,15 * 1 命令 | 每月1号和15号，每周1的0点0分都会执行命令。注意：星期几和几号最好不要同时出现，因为他们的定义都是天，非常容易让管理员混乱。 |



## 软件包管理

### 1.RPM

#### RPM概述

RPM（RedHat Package Manager），RedHat软件包管理工具，类似windows里面的setup.exe，是Linux这系列操作系统里面的打包安装工具，它虽然是RedHat的标志，但理念是通用的。

RPM包的格式名称

Apache-1.3.23-11.i386.rpm

- “apache”软件名称
- “1.3.23-11”软件的版本号，主版本和此版本
- “i386”是软件所运行的硬件平台，Intel32位处理器的统称
- “rpm”文件扩展名，代表rpm包

#### RPM查询命令（rpm -qa）

基本语法

`rpm -qa`				（功能描述：查询所安装的所有rpm软件包）

rpm -qa | grep firefox

rpm -qi firefox

#### RPM卸载命令（rpm -e）

基本语法：

`rpm -e RPM软件包`

`rpm -e --nodeps`

选项说明

| 选项    | 功能                                                         |
| ------- | ------------------------------------------------------------ |
| -e      | 卸载软件包                                                   |
| -nodeps | 卸载软件时，不检查依赖。这样的话，那些使用该软件包的软件在此之后可能不能正常工作 |



#### RPM安装命令（rpm -ivh）

基本语法：

`rpm -ivh RPM包全名`

选项说明

| 选项     | 功能                    |
| -------- | ----------------------- |
| -i       | install，安装           |
| -v       | --verbose，显示详细信息 |
| -h       | --hash，进度条          |
| --nodeps | 安装前不检查依赖        |



rpm的问题：1.安装软件必须指定**全名**。2.依赖关系：例如软件A依赖b和c，如果系统中没有b或c，则安装过程报错。

（yum可以全自动化自动处理软件依赖，一键安装）

### 2.YUM

#### YUM概叙

YUM（全称为Yellow dog Updater，Modified）是一个在Fedora和RedHat以及CentOS中的shell前端软件包管理器。基于RPM包管理，能够从指定的服务器自动下载RPM包并且安装，<font style="color:red">可以自动处理依赖性关系</font>，并且一次安装所有依赖的软件包，无需频繁地一次次下载、安装。

#### YUM的常用命令

基本语法：

`yum [选项] [参数]`

选项说明

| 选项 | 功能                  |
| ---- | --------------------- |
| -y   | 对所有提问都回答“yes” |

参数说明

| 参数            | 功能                          |
| --------------- | ----------------------------- |
| install（重要） | 安装rpm软件包                 |
| update（重要）  | 更新rpm软件包                 |
| check-update    | 检查是否有可用的更新rpm软件包 |
| remove（重要）  | 删除指定的rpm软件包           |
| list            | 显示软件包信息                |
| clean           | 清理yum过期的缓存             |
| deplist         | 显示yum软件包的所有依赖关系   |

案例实操：

采用yum方式安装firefox：yum -y install firefox



#### 修改网络YUM源

默认的系统YUM源，需要连接国外apache网站，网速比较慢，可以修改关联的网络YUM源为国内镜像的网站，比如网易163，aliyun等

（1）安装wget，wget用来从指定的URL下载文件

`yum install wget`

（2）在/etc/yum.repos.d/目录下，备份默认的repos文件

```bash
[root@study yum.repos.d] pwd
/etc/yum.repos.d
[root@study yum.repos.d] cp CentOS-Base.repo CentOS-Base.repo.backup
```

（3）下载网易163或者是aliyun的repos文件，任选其一

```bash
[root@study yum.repos.d] wget http://mirrors.aliyun.com/repo/Centos-7.repo
[root@study yum.repos.d] wget http://mirrors.163.com/.help/CentOS7-Base-163.repo
```

（4）使用下载好的repos文件替换默认的repos文件

`mv CentOS7-Base-163.repo CentOS-Base.repo`

（5）清理旧缓存，缓存新数据

`yum clean all`

`yum makecache`

（6）测试

`yum list | grep firefox`

`yum -y install firefox`

# 克隆虚拟机



# shell编程

## shell概述

Shell是一个<u>***命令解释器***</u>，它接收应用程序/用户命令，然后调用操作系统内核。Shell还是一个功能相当强大的编程语言，易编写、易调试、灵活性强。

Linux提供的Shell解释器有

```bash
[root@study ~]# less /etc/shells
```

/bin/sh

/bin/bash

/usr/bin/sh

/usr/bin/bash

/bin/tcsh

/bin/csh

## shell脚本入门

### 1.脚本格式

脚本以`#!/bin/bash` 开头（指定解析器）

### 2.第一个Shell脚本：helloworld.sh

（可以不加任何后缀，只要格式符合shell编程即可执行）

需求：创建一个Shell脚本，输出helloworld

案例实操：

```bash
[root@study test2]# touch helloworld.sh
[root@study test2]# vim helloworld.sh 

在helloworld.sh中输入如下内容
#!/bin/bash
echo "helloworld"
```

### 3.脚本的常用执行方式

第一种：采用bash或sh+脚本的相对路径或绝对路径（不用赋予脚本+x权限）

第二种：采用输入脚本的绝对路径或相对路径执行脚本（必须具有可执行权限+x）

`chmod a+x helloworld.sh`

（了解）第三种：在脚本的路径前加上“.”或者source

`source helloworld.sh`

`. helloworld.sh`

区别：

- 前两种方式是在当前shell中打开一个子shell来执行脚本内容，当脚本内容结束，则子shell关闭，回到父shell中。

- 第三种，也就是使用在脚本路径前加“.”或者source的方式，可以使脚本内容在当前shell里执行，而无需打开子shell，这也是为什么我们每次修改完/etc/profile文件以后，需要source一下的原因。

开子shell与不开子shell的区别就在于，环境变量的继承关系，如在子shell中设置的当前变量，父shell是不可见的。

## 变量

### 系统预定义变量

常用系统变量：$HOME、$PWD、$SHELL、$USER等

`echo $`

`env | less`

`printenv | less`（查看全局变量），`printenv USER`（不用加$）

查看当前定义的全部变量（全局和局部）：`set`



### 自定义变量

基本语法：

（1）定义变量：变量名=变量值，<u>注意：=号后面不能有空格</u>

（2）撤销变量：unset 变量名

（3）声明只读变量：readonly 变量，<u>注意：不能unset</u>

局部变量提升为全局变量

- 先定义局部变量
- `export my_var`，即导出为全局变量

注意：在子shell中修改了全局变量不会影响父shell中对应的全局变量（export导出也不行）

（局部变量只能在当前shell中访问到，提升为全局变量后，自己及子shell可以获取到）



变量定义规则：

（1）变量名称可以由字母、数字和下划线组成，但是不能以数字开头，<u>环境变量名建议大写</u>。

（2）等号两侧不能有空格。

（3）在bash中，变量默认类型都是字符串类型，无法直接进行数值运算。

（4）变量的值如果有空格，需要使用双引号或单引号括起来。



### shell字符串

单引号

- 单引号里的任何字符都会原样输出，单引号字符串中的变量是无效的；
- 单引号字串中不能出现单独一个的单引号（对单引号使用转义符后也不行），但可成对出现，作为字符串拼接使用。

双引号：

- 双引号里可以有变量
- 双引号里可以出现转义字符



获取字符串长度

```bash
string="abcd"
echo ${#string} 
# 当变量为数组时，${#string}等价于${#string[0]}
```



### shell数组

定义数组的一般形式

```bash
数组名=(值1 值2 值3)
array_name=(value0 value1 value2)

array_name[0]=value0
array_name[1]=value1
```

读取数组

```bash
${数组名[下标]}
value_n=${array_name[n]}

#使用@或*可以获取数组中的所有元素
echo ${array_name[@]}
```

获取数组长度

```bash
# 取得数组元素的个数
length=${#array_name[@]}
# 或者
length=${#array_name[*]}
# 取得数组单个元素的长度
lengthn=${#array_name[n]}
```

单行注释：

```bash
#
```

多行注释

```bash
:<<EOF
注释内容
EOF

:<<'
注释内容
'

:<<!
注释内容
!
```



### 特殊变量

#### $n

基本语法：

$n（功能描述：n为数字，$0代表该脚本名称，$1-$9代表第一到第九个参数，十以上的参数需要用大括号包含，如${10}（花括号为限定变量的范围））



#### $#

基本语法：

$#（功能描述：获取所有<u>输入参数的个数</u>常用于循环，判断参数的个数是否正确以及加强脚本的健壮性）。 



#### $*、$@

基本用法：

$\*（功能描述：这个变量代表命令行中所有的参数，<u>$\*把所有的参数看成一个整体</u>）

$@（功能描述：这个变量也代表命令行中所有的参数，不过<u>$@把每个参数区分对待</u>）



#### $?

基本语法：

$?（功能描述：最后一次执行的命令的返回状态。如果这个变量的值为0，证明上一个命令正确执行；如果这个变量的值为非0（具体是哪个数，由命令自己决定），则证明上一个命令执行不正确了。）

## 运算符

基本语法：

“$((运算式))”或“$[运算式]”

案例实操：

计算（2+3）*4的值

```bash
echo $[(2+3)*4]
echo $(((2+3)*4))
expr 5 \* 4
echo `expr 5 \* 4`
echo $(expr 5 \* 4)
```

反引号内写可执行的表达式

### 算数运算符

假定变量 a 为 10，变量 b 为 20

| 运算符 | 说明                                          | 举例                          |
| :----- | :-------------------------------------------- | :---------------------------- |
| +      | 加法                                          | `expr $a + $b` 结果为 30。    |
| -      | 减法                                          | `expr $a - $b` 结果为 -10。   |
| *      | 乘法                                          | `expr $a \* $b` 结果为  200。 |
| /      | 除法                                          | `expr $b / $a` 结果为 2。     |
| %      | 取余                                          | `expr $b % $a` 结果为 0。     |
| =      | 赋值                                          | a=$b 把变量 b 的值赋给 a。    |
| ==     | 相等。用于比较两个数字，相同则返回 true。     | [ $a == $b ] 返回 false。     |
| !=     | 不相等。用于比较两个数字，不相同则返回 true。 | [ $a != $b ] 返回 true。      |

### 关系运算符

关系运算符只支持数字，不支持字符串，除非字符串的值是数字。

假定变量 a 为 10，变量 b 为 20

| 运算符 | 说明                                                  | 举例                       |
| :----- | :---------------------------------------------------- | :------------------------- |
| -eq    | 检测两个数是否相等，相等返回 true。                   | [ $a -eq $b ] 返回 false。 |
| -ne    | 检测两个数是否不相等，不相等返回 true。               | [ $a -ne $b ] 返回 true。  |
| -gt    | 检测左边的数是否大于右边的，如果是，则返回 true。     | [ $a -gt $b ] 返回 false。 |
| -lt    | 检测左边的数是否小于右边的，如果是，则返回 true。     | [ $a -lt $b ] 返回 true。  |
| -ge    | 检测左边的数是否大于等于右边的，如果是，则返回 true。 | [ $a -ge $b ] 返回 false。 |
| -le    | 检测左边的数是否小于等于右边的，如果是，则返回 true。 | [ $a -le $b ] 返回 true。  |

### 布尔运算符

假定变量 a 为 10，变量 b 为 20

| 运算符 | 说明                                                | 举例                                     |
| :----- | :-------------------------------------------------- | :--------------------------------------- |
| !      | 非运算，表达式为 true 则返回 false，否则返回 true。 | [ ! false ] 返回 true。                  |
| -o     | 或运算，有一个表达式为 true 则返回 true。           | [ $a -lt 20 -o $b -gt 100 ] 返回 true。  |
| -a     | 与运算，两个表达式都为 true 才返回 true。           | [ $a -lt 20 -a $b -gt 100 ] 返回 false。 |

### 逻辑运算符

假定变量 a 为 10，变量 b 为 20

| 运算符 | 说明       | 举例                                       |
| :----- | :--------- | :----------------------------------------- |
| &&     | 逻辑的 AND | [[ $a -lt 100 && $b -gt 100 ]] 返回 false  |
| \|\|   | 逻辑的 OR  | [[ $a -lt 100 \|\| $b -gt 100 ]] 返回 true |



### 字符串运算符

假定变量 a 为 "abc"，变量 b 为 "efg"

| 运算符 | 说明                                         | 举例                     |
| :----- | :------------------------------------------- | :----------------------- |
| =      | 检测两个字符串是否相等，相等返回 true。      | [ $a = $b ] 返回 false。 |
| !=     | 检测两个字符串是否不相等，不相等返回 true。  | [ $a != $b ] 返回 true。 |
| -z     | 检测字符串长度是否为0，为0返回 true。        | [ -z $a ] 返回 false。   |
| -n     | 检测字符串长度是否不为 0，不为 0 返回 true。 | [ -n "$a" ] 返回 true。  |
| $      | 检测字符串是否不为空，不为空返回 true。      | [ $a ] 返回 true。       |



### 文件测试运算符

| 操作符  | 说明                                                         | 举例                      |
| :------ | :----------------------------------------------------------- | :------------------------ |
| -b file | 检测文件是否是块设备文件，如果是，则返回 true。              | [ -b $file ] 返回 false。 |
| -c file | 检测文件是否是字符设备文件，如果是，则返回 true。            | [ -c $file ] 返回 false。 |
| -d file | 检测文件是否是目录，如果是，则返回 true。                    | [ -d $file ] 返回 false。 |
| -f file | 检测文件是否是普通文件（既不是目录，也不是设备文件），如果是，则返回 true。 | [ -f $file ] 返回 true。  |
| -g file | 检测文件是否设置了 SGID 位，如果是，则返回 true。            | [ -g $file ] 返回 false。 |
| -k file | 检测文件是否设置了粘着位(Sticky Bit)，如果是，则返回 true。  | [ -k $file ] 返回 false。 |
| -p file | 检测文件是否是有名管道，如果是，则返回 true。                | [ -p $file ] 返回 false。 |
| -u file | 检测文件是否设置了 SUID 位，如果是，则返回 true。            | [ -u $file ] 返回 false。 |
| -r file | 检测文件是否可读，如果是，则返回 true。                      | [ -r $file ] 返回 true。  |
| -w file | 检测文件是否可写，如果是，则返回 true。                      | [ -w $file ] 返回 true。  |
| -x file | 检测文件是否可执行，如果是，则返回 true。                    | [ -x $file ] 返回 true。  |
| -s file | 检测文件是否为空（文件大小是否大于0），不为空返回 true。     | [ -s $file ] 返回 true。  |
| -e file | 检测文件（包括目录）是否存在，如果是，则返回 true。          | [ -e $file ] 返回 true。  |

其他检查符：

- **-S**: 判断某文件是否 socket。
- **-L**: 检测文件是否存在并且是一个符号链接。

## 条件判断

1）基本语法：

（1）test condition

（2）[ confition ]（***<u>注意 condition 前后要有空格</u>***）

注意：条件非空即为true，[ czh ]返回true，[  ]返回false



2）常用判断条件

（1）两个整数之间比较

-eq 等于（equal）

-ne 不等于（not equal）

-lt 小于（less than）

-le 小于等于（less equal）

-gt 大于（greater than）

-ge 大于等于（greater equal）

注：如果是字符串之间的比较，用等号“=”判断相等；用“!=”判断不等

（2）按照文件权限进行判断

-r 有读的权限（read）

-w 有写的权限（write）

-x 有执行的权限（execute）

（3）按照文件类型进行判断

-e 文件存在（existence）

-f 文件存在并且是一个常规的文件（file）

-d 文件存在并且是一个目录（directory）



```bash
[root@study test2]# [ -r add.sh ]
[root@study test2]# echo $?
0
[root@study test2]# [ -w add.sh ]
[root@study test2]# echo $?
0
[root@study test2]# [ -x add.sh ]
[root@study test2]# echo $?
0
```

多条件判断（&&表示前一条命令执行成功时，才执行后一条命令，||表示上一条命令执行失败后，才执行下一条命令）

`[ czh ] && echo OK || echo notOK`（实现三目运算）

## 流程控制（重点）

### if判断

基本语法

（1）单分支

```bash
if [ 条件判断式 ];then
	程序
fi
```

或者

```bash
if [ 条件判断式 ]
then
	程序
fi
```

if单分支组合判断

```bash
if [ 条件判断式 -a 条件判断式]
then
	程序
fi
#-a 即 and
```

或

```bash
if [ 条件判断式 ] && [ 条件判断式]
then
	程序
fi
```



（2）多分支

```bash
if [ 条件判断式 ]
then
	程序
elif [ 条件判断式 ]
then
	程序
else
	程序
fi
```



### case语句

基本语法：

```bash
case $变量名 in
"值1")
	如果变量的值等于值1，则执行程序1
;;
"值2")
	如果变量的值等于值2，则执行程序2
;;
	...省略其他分支...
*)
	如果变量的值都不是以上值，则执行此程序
;;
esac
```

注意事项：

- case行尾必须为单词“in”，每一个模式匹配必须以右括号“)”结束。
- 双分号“;;”表示命令序列结束，相当于java中的break
- 最后的“*)”表示默认模式，相当于java中的default

### for循环

基本语法1：

```bash
for ((初始值;循环控制条件;变量变化))
do
	程序
done
```

双小括号内可以直接写数学运算式，如i<=100，无需使用-le

基本语法2：

```bash
for 变量 in 值1 值2 值3...
do
	程序
done

```

```bash
for os in linux windows macos
do
	echo $os
done
```

```bash
for i in {1..100}
do
	sum=$[$sum+$i]
done
```

比较$*和$@的区别

​	$*和$@都表示传递给函数或脚本的所有参数，不被双引号“”包含时，都以$1 $2 ...$n的形式输出所有参数。

​	当它们被双引号“”包含时，$*会将所有的参数作为一个整体，以“$1 $2 ...$n”的形式输出所有参数；$@会将各个参数分开，以“$1”“$2”...“$n”的形式输出所有参数。



### while循环

基本语法：

```bash
while [ 条件判断式 ]
do
	程序
done
```





```bash
#!/bin/bash

#用for实现
for ((i=1;i<=$1;i++))
do
        s1=$[ $s1+$i ]
done
echo "sum1=$s1"

for j in {1..100}
do
        s2=$[$s2+$j]
done
echo "sum2=$s2"

#用while实现
k=1
while [ $k -le 100 ]
do
#       s3=$[$s3+$k]
#       k=$[$k+1]
        let s3+=k
        let k++
done
echo "sum3=$s3"
```



## read读取控制台输入

基本语法：

read （选项） （参数）

①选项：

​	-p：指定读取值时的提示符；

​	-t：指定读取值时等待的时间（秒）如果-t不加表示一直等待

②参数：

​	变量：指定读取值的变量名



## 函数

### 系统函数

#### basename

基本语法：

​	basename [string / pathname] [suffix]	（功能描述：basename命令会删掉所有的前缀包括最后一个（‘/’）字符，然后将字符串显示出来）。

​	basename可以理解为取路径里的文件名称

​	选项：suffix为后缀，如果suffix被指定了，basename会将pathname或string中的suffix去掉



#### dirname

基本语法：

dirname 文件绝对路径（功能描述：从给定的包含绝对路径的文件名中去除文件名（非目录的部分），然后返回剩下的路径（目录的部分））

dirname 可以理解为取文件路径的绝对路径名称

### 自定义函数

基本语法：

```bash
[ function ] funname[()]
{
	Action;
	[return  int;]
}
```

`[ function ]`、`[return int;]`：[]是指可选的，可以省略

经验技巧

（1）必须在调用函数地方之前，先声明函数，shell脚本是逐行运行。不会像其他语言一样先编译。

（2）函数返回值，只能通过$?系统变量获得，可以显式加`return`返回，如果不加，将以最后一条命令运行结果作为返回值。return后跟数值n(0-255)



## 正则表达式入门（仅入门）

正则表达式使用单个字符串来描述、匹配一系列符合某个语法规则的字符串。在很多文本编辑器里，正则表达式通常被用来检索、替换那些符合某个模式的文本。在Linux中，grep，sed，awk等文本处理工具都支持通过正则表达式进行模式匹配。

### 常规匹配

一串不包含特殊字符的正则表达式匹配它自己

### 常用特殊字符

1）特殊字符：^

^匹配一行的开头，例如

```bash
cat /etc/passwd | grep ^a
```

会匹配出所有以a开头的行



2）特殊字符：$

$匹配一行结束，例如

```bash
cat /etc/passwd | grep t$
```

会匹配出所有以t结尾的行

^$表示匹配空行



3）特殊字符：.

.匹配一个任意的字符，例如

```bash
cat /etc/passwd | grep r..t
```

会匹配包含rabt，rbbt，rxdt，root等的所有行



4）特殊字符：*

*不单独使用，他和上一个字符连用，表示匹配上一个字符0次或多次，例如

```bash
cat /etc/passwd | grep ro*t
```

会匹配rt，rot，root，rooot，roooot等所有行

.*表示任意字符任意次，即表示任意字符串

```bash
cat /etc/passwd | grep ^a.*var.*in$
表示以a开头，以bash结尾的任意字符串
```



5）字符区间（中括号）：[]

[]表示匹配某个范围内的一个字符，例如

[6,8]------匹配6或8（,可以去掉）

[0-9]------匹配一个0-9的数字

[0-9]*------匹配任意长度的数字字符串

[a-z]-------匹配一个a-z之间的字符

[a-z]*-------匹配任意长度的字母字符串

[a-c,e-f]-----匹配a-c或者e-f之间的任意字符

```bash
cat /etc/passwd | grep r[a,b,c]*t
```

会匹配rt，rat，rbt，rabt，rbact等等所有行



6）特殊字符：\

\表示转义，并不会单独使用。由于所有特殊字符都有其特定匹配模式，当我们想匹配某一特殊字符本身时（例如，我想找出所有包含'$'的行），就会碰到困难。此时我们就要将转义字符和特殊字符连用，来表示特殊字符本身，例如

```bash
cat /etc/passwd | grep 'a\$b'
```

就会匹配所有包含a$b的行。**<u>注意需要使用单引号将表达式引起来</u>**



特殊字符：{},+,?

[0-9]{2}---------表示长度为2的数字，{}中的数字表示出现次数

+：与*类似，表示出现次数>=1

?：与*类似，表示出现次数为0或1



## 文本处理工具

### cut

cut的工作就是“剪”，具体的说就是在文件中负责剪切数据用的。cut命令从文件的每一行剪切字节、字符和字段并将这些字节、字符和字段输出。

1）基本用法：

cut [选项参数] filename

说明：默认分隔符是制表符

2）选项参数说明

| 选项参数 | 功能                                           |
| -------- | ---------------------------------------------- |
| -f       | 列号，提取第几列                               |
| -d       | 分隔符，按照指定分隔符分割列，默认是制表符“\t” |
| -c       | 按字符进行切割，后加n表示取第几列，比如-c 1    |

```bash
[root@study test]# vim cut.txt
[root@study test]# cat cut.txt
dong shen
guan zhen
wo wo
lai lai
le le
[root@study test]# cut -d " " -f 1 cut.txt 
dong
guan
wo
lai
le
```

```bash
[root@study test]# cat /etc/passwd | grep bash$
root:x:0:0:root:/root:/bin/bash
czh:x:1000:1000:czh:/home/czh:/bin/bash
[root@study test]# cat /etc/passwd | grep bash$ | cut -d ":" -f 1,6,7
root:/root:/bin/bash
czh:/home/czh:/bin/bash

```

`cat /etc/passwd | grep bash$ | cut -d ":" -f 1,6-`，-表示6列及之后的列，1-4列就用1-4

`ifconfig ens33 | grep netmask | cut -d " " -f 10`截取ip地址

### awk

一个强大的文本分析工具，把文件逐行地读入，以空格为默认分隔符将每行切片，切开的部分再进行分析处理。

1）基本用法

​	awk [选项参数] ‘/pattern1/{action1} /pattern2/{action2}...’ filename

​	pattern：表示awk在数据中查找的内容，就是匹配模式（正则表达式）

​	action：在找到匹配内容时所执行的一系列命令

2）选项参数说明

| 选项 | 功能                 |
| ---- | -------------------- |
| -F   | 指定输入文件分隔符   |
| -v   | 赋值一个用户定义变量 |

 

案例实操：

1. 数据准备

```bash
cp /etc/passwd ./
passwd数据的含义
用户名:密码（加密过后的）:用户id:组id:注释:用户家目录:shell解析器
```

2. 搜索passwd文件以root关键字开头的所有行，并输出该行的第7列。

```bash
cat /etc/passwd | awk -F ":" '/^root/{print $7}'
```

3. 搜索passwd文件以root关键字开头的所有行，并输出该行的第1列和第7列，中间以“,”分割。

```bash
cat /etc/passwd | awk -F ":" '/^root/{print $1","$7}'
```

4. 只显示/etc/passwd的第一列和第七列，以逗号分割，且在所有行前面添加列名user,shell 在最后一行添加“end of file”

```bash
cat /etc/passwd | awk -F ":" 'BEGIN{print "user,shell"} {print $1","$7} END{print "end of file"}'
```

5. 将passwd文件中的用户id增加数值1并输出

```bash
awk -v i=1 -F ":" '{print $3+i}' passwd
```



awk的内置变量

| 变量     | 说明                                   |
| -------- | -------------------------------------- |
| FILENAME | 文件名                                 |
| NR       | 已读的记录数（行号）                   |
| NF       | 浏览记录的域的个数（切割后，列的个数） |



## 综合应用案例

### 归档文件

实际生产应用中，往往需要对重要数据进行归档备份。

需求：实现一个每天对指定目录归档备份的脚本，输入一个目录名称（末尾不带/），将目录下所有文件按天归档保存，并将归档日期附加在归档文件名上，放在/root/archive下。

​	这里用到了归档命令：tar

​	后面可以加上-c选项表示归档，加上-z选项表示同时进行压缩，得到的文件后缀名为tar.gz。

```shell
#!/bin/bash

# 首先判断输入参数个数是否为1
if [ $# -ne 1 ]
then
	echo "参数个数错误！应该输入一个参数，作为归档目录名"
	exit
fi

# 从参数中获取目录名称
if [ -d $1 ]
then
	echo
else
	echo "目录不存在！"
	exit
fi

DIR_NAME=$(basename $1)
DIR_PATH=$(cd $(dirname $1); pwd)

# 获取当前日期
DATE=$(date +%y%m%d)

# 定义生成的归档文件名称
FILE=archive_${DIR_NAME}_$DATE.tar.gz
DEST=/root/archive/$FILE

# 开始归档目录文件
echo "开始归档..."

tar -zcf $DEST $DIR_PATH/$DIR_NAME

if [ $? -eq 0 ]
then
	echo "归档成功！"
	echo "归档文件为：$DEST"
else
	echo "归档出现问题！"
fi

exit
```



### 冒泡排序

```bash
#!/bin/bash

function bubbleSort()
{
	arr=($1)
	echo "start sort:${arr[*]}"
	length=$[${#arr[*]}-1]
	for ((i=0;i<$length;i++))
	do
		for ((j=0;j<$length-i;j++))
		do
			if [ ${arr[j]} -gt ${arr[j+1]} ]
			then
				temp=${arr[j]}
				arr[j]=${arr[j+1]}
				arr[j+1]=$temp
			fi
		done
	done
	echo "after sort:${arr[*]}"
}


echo "开始进行冒泡排序"
count=$#

echo "输入的数字个数为："$count

#声明数组并初始化
array=("$@")

echo ${array[*]}

bubbleSort "${array[*]}"
```


