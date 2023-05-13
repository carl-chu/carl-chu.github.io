---
title: ADB学习
date: 2023-02-10 20:54:00
tags: Android
categories: 学习笔记
---

ADB学习

adb（android debug bridge）安卓调试桥，用于完成电脑和android手机间的通信控制。（使用xcode来完成对于ios设备的操控，前提是有个mac电脑）



# adb的安装

本身adb是android SDK其中自带的工具，用于完成电脑和手机之间的通信控制。

最简单的使用方法：将adb的三个必备文件，放到自己的电脑目录上，配置环境变量path就可以用。

推荐安装sdk：http://tools.android-studio.org/index.php/sdk

- 下载zip包，解压即可
- 解压之后，将sdk的platform-tools配置到环境变量path里。
- adb version用于查看版本，验证配置成功



# 将设备与电脑连接

建议练习时使用android模拟器，mumu 或 夜神

1. 打开手机上的开发者选项，通过点击手机版本号7次。
2. 开发者选项中，打开usb调试。

3. 连接模拟器设备：`adb connect 127.0.0.1:xxxxx`

4. 连接完成之后，用`adb devices`命令查看当前连接的设备，注意后面的device表示连接成功。



# 常用的adb命令

adb shell：用于进入android系统进行操作，exit退出。



## 文件的基本操作

adb install [电脑上apk包路径]：安装apk安装包。

adb uninstall [安卓系统中应用的包名（package）]：卸载应用



## 包管理（pm）操作

adb shell pm list package：列出手机中安装的所有应用的package名字。

adb shell pm list package -3：查看安装的第三方应用。

在执行的时候，实际上先通过adb shell进入安卓手机系统，然后调用手机中的pm命令：放在system/bin目录下的pm脚本。

（如果已经通过adb shell进入到手机，则直接使用`pm list package`）



## 下载和上传文件

adb pull [手机上的文件路径] [电脑上的目标目录]：将文件从手机下载到电脑。

adb push [电脑上的文件] [手机上目标目录]：将文件从电脑上传到手机。



## 页面管理操作

am:activity manager，手机应用中的每个页面就是一个activity。

启动应用，需要通过应用的启动activity来完成调用。

adb shell am start -W -S [包名]/[启动activity名]：启动对应的应用



获取应用的activity：

1. adb shell dumpsys activity | grep "mFocuse"（使用此命令时需要打开被测应用，实际上获取的是当前应用打开的activity名，有可能并不是启动用的activity）

2. 使用sdk中的aapt工具来进行apk包的解析。（获取到的一定是启动用的activity）

3. adb shell monkey -p [被测包名] -v -v -v 1 （通过查看日志信息中，找到using开头的那一句，其中activity名字就是启动页面）



交互型命令：

adb shell input text [文本]



# adb完成自动化操作

1. 先获取包名：`adb shell pm list package -3`
2. 根据包名获取应用的启动activity：`adb shell monkey -p [被测包名] -v -v -v 1`

3. 根据获取到的activity名字，启动应用：`adb shell am start -W -S [包名]/[启动activity名]`



互联互通，多设备下通信，

设备：tv，音响，嵌入式设备，

系统：android

app1：调用系统底层

app2：sdk，进程通信，异步收消息

通信设备：蓝牙

接口调用时序

安卓版本影响、wifi适配器影响（接口调用时间）





