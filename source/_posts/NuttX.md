---
title: Nuttx
date: 2023-04-30 18:00:00
tags: RTOS
categories: 学习笔记
index_img: /img/nuttx_logo.jpg
---

# NuttX

https://nuttx.apache.org/docs/latest/quickstart/index.html

## 安装

1.安装软件包

```bash
sudo apt install \
bison flex gettext texinfo libncurses5-dev libncursesw5-dev xxd \
gperf automake libtool pkg-config build-essential gperf genromfs \
libgmp-dev libmpc-dev libmpfr-dev libisl-dev binutils-dev libelf-dev \
libexpat-dev gcc-multilib g++-multilib picocom u-boot-tools util-linux
```

2.安装工具链

```bash
apt install gcc-arm-none-eabi binutils-arm-none-eabi
```

3.安装KConfig

```bash
#Ubuntu20.04以上系统
apt install kconfig-frontends
```

4.下载源码

```bash
mkdir nuttxspace
cd nuttxspace
git clone https://github.com/apache/nuttx.git nuttx
git clone https://github.com/apache/nuttx-apps apps
```

## 编译

### 初始化配置

1.初始化配置，列出所有支持的配置

```bash
cd nuttx
./tools/configure.sh -L | less
```

2.选择配置指明主机平台

```bash
cd nuttx
./tools/configure.sh -l stm32f4discovery:nsh
```

3.使用菜单来自定义配置

```bash
cd nuttx
make menuconfig
```



### 构建Nuttx

1.构建

构建会在目录中生成二进制文件。通常包括NuttX ELF文件和一个`nuttx.bin`可以被烧录到板上

```bash
cd nuttx
make
```

加速构建可以使用`-jN`参数，多线程并行编译，`N`通常取决于电脑的处理器个数。（make -j8）

2.清理构建

```bash
make clean
```



## 运行

### 烧录

### 连接NuttShell



## 配置

1.初始化模拟器配置

```bash
./tools/configure.sh -l sim:nsh
```

2.构建和运行

```bash
make clean
#make报错注意看报错信息，可能需要安装依赖，pip install pyelftools cxxfilt
make
#执行
./nuttx
```

默认有用户名和密码：username=admin, password=Administrator

可以通过配置关闭，Application Configuration->NSH Library->Console Login

修改完配置需要重新make

3.关闭模拟器

```bash
#新建一个终端
pkill nuttx

#或者在nsh下执行poweroff
```

可以使用`make menuconfig`修改配置重新make再执行。



## 调试





## NuttX架构

- NSH(nuttx shell)、各种app：实际的应用层，应用程序可以作为是我们在终端中运行的东西，或者可以看作是一个终端。

- Virtual File System(虚拟文件系统)：虚拟文件系统，符合Linux系统的思想，这一层的主要作用是**将设备的操作转化为对虚拟文件的操作**。
- Upper Half Drivers(上层驱动)：这些驱动可以看作为是对各种设备的操作接口的定义，比如PWM、Network等等。这一层也可以视作为系统的API。在这里你可以不需要管底层的实现，直接调用就可以了。
- Lower Half Drivers(下层驱动)：这一层是上层驱动的具体化，在这里是每种芯片都有不一样的寄存器操作，这一层的驱动提供的API直接操作各种芯片，各种芯片的驱动在这里都是不相同的。

- Microcontroller + Peripherals(外设)：这一层是各种硬件以及外设。位于`Nuttx/arch/*`。

## NuttX目录结构

https://nuttx.apache.org/docs/latest/quickstart/organization.html

### nuttx/arch

该目录包含多个子目录，每个子目录包含特定于体系结构的逻辑。

### nuttx/binfmt

包含将文件系统中的二进制文件以可用于执行它们的形式加载到内存中的逻辑。

### nuttx/audio

包含nuttx音频子系统。

### nuttx/boards

包含电路板特定的配置文件。

其子目录结构为/\<arch-name>/\<chip-name>/\<board-name>

### nuttx/crypto

包含nuttx加密子系统

### nuttx/drivers

包含与体系结构无关的设备驱动程序

### nuttx/fs

包含nuttx的文件系统

### nuttx/graphics

包含nuttx下的图形、视频支持

### nuttx/include

包含nuttx头文件

### nuttx/

这是一个（几乎是）空的目录，它有一个存放生成的静态库的地方。NuttX构建系统在编译阶段会在这个目录中生成一系列静态库。这些库在最后的链接阶段被放在一个已知的地方，在那里它们被访问以生成最终的二进制文件。

### nuttx/libs/libc

### nuttx/libs/libxx

包含一个微小的、最小的标准 C++，可用于在 NuttX 中构建一些简单的 C++ 应用程序。

### nuttx/mm

NuttX 内存管理器。

### nuttx/net

含 NuttX 网络层的实现，包括内部套接字 API。

### nuttx/sched

构成 NuttX RTOS 核心的文件位于此处。

### nuttx/syscall

如果 NuttX 构建为单独编译的内核（使用 `CONFIG_BUILD_PROTECTED=y`或`CONFIG_BUILD_KERNEL=y`），则构建该目录的内容。该目录包含一个系统调用接口，可用于用户模式应用程序和内核模式 RTOS 之间的通信。

### nuttx/tools

该目录包含一组工具和脚本，用于简化配置、构建和维护 NuttX。

### nuttx/wireless

该目录包含对独立于硬件的无线支持。

### nuttx/Makefile

位于`$(TOPDIR)`目录的顶层`Makefile`包含构建nuttx的所有顶层控制逻辑。

## NSH应用程序

进入nuttxspace/nuttx，执行`make menuconfig`进入配置

1. 配置：File Systems :arrow_right: ROMFS file system（开启此选项，默认开启）

2. 到达：`Application Configuration → NSH Library → Scripting Support -> Support ROMFS start-up script`，可以看到这个系统的默认启动脚本为`init.d/rc.sysinit`，可以通过这个地方进行修改，但是最好还是不要改了，避免有bug，可以进入系统的文件中修改（进入`/nuttxspace/nuttx/boards/sim/sim/sim/src/etc/init.d`，打开rcS文件，进行修改）



NuttxShell是一个简单的Shell应用程序，支持各种命令，也可以自定义命令，可以添加新命令、更改初始化序列等，其shell语句具体实现在`/nuttxspace/apps/nshlib`（这个是它的库）



### 内置应用程序

> 内置应用程序主要是支持从NSH命令行执行应用程序

在`/apps/builtin`可以找到内置应用程序逻辑

- 用来支持注册机制，以便内置应用程序可以在构建时动态注册自己
- 用于查找、列出和执行内置应用程序的实用函数



内置应用程序逻辑导出的实用函数原型在 ：`nuttx/include/nuttx/lib/builtin.h`和`apps/include/builtin.h`

- `int builtin_isavail(FAR const char *appname);`在构建期间检查以appname注册的应用程序的可用性
- `const char *builtin_getname(int index);`返回指向应用程序名称的指针，可以在输入"nsh> help"时列出可用的内置函数。

- `int exec_builtin(FAR const char *appname, FAR const char **argv);`执行在编译时注册的内置应用程序



**自动生成头文件**

构建Nuttx时，应用程序入口被收集在两个文件中：

- `apps/builtin/builtin_proto.h`:应用程序任务入口点的原型。
- `apps/builtin/builtin_list.h`:应用特定信息和启动要求。



**内置程序的注册**

在执行不同的构建目标时，NuttX的构建分为几个阶段：

（1）建立配置时的上下文

（2）生成目标依赖时的依赖

（3）默认（全部）的内置程序当正常编译和链接操作时被执行

内置应用程序信息是在make context构建阶段收集的。

以`apps/examples/hello`目录为例，其主程序为`apps/example/hello/hello_main.c`

```c
int main(int argc, FAR char *argv[])
{
  printf("Hello, World!!\n");
  return 0;
}
```

这是一个在NuttX构建上下文阶段注册的内置函数，由`apps/examples/hello/Makefile`执行注册逻辑。

构建系统的构建逻辑：

1. 顶级的上下文编译目标在nuttx/Makefile中。所有构建目标都依赖于上下文构建目标。对于app/目录，该构建目标将执行app/Makefile中的上下文目标。

2. apps/Makefile反过来会执行所有配置的子目录中的上下文目标。在我们的例子中，将包括apps/examples中的Makefile。

3. 最后，apps/examples/Makefile将在所有配置好的examples子目录中执行上下文目标。

注意：由于上下文构建阶段只能执行一次，因此任何后续配置都不会反映在构建序列中。在实例化配置之前，需要先删除旧配置，最简单的方法是：

```bash
make distclean
```

但是这种方法需要重新配置NuttX，如果只想在`apps/`子目录中重新构建配置执行如下命令即可：

```bash
make apps_distclean
```



` apps/examples/hello/Makefile`会把hello_main()应用注册到`apps/builtin`里的`builtin_proto.h`和`builtin_list.h`文件中

### 同步内置应用程序

默认情况下，从NSH命令行启动的内置命令将与NSH异步运行。如果想强制执行命令然后等待命令执行可以在`/home/leiyongke/nuttxspace/nuttx/boards/sim/sim/sim/configs/nsh/defconfig`(以sim中的nsh为例)添加该配置：`CONFIG_SCHED_WAITPID=y`来启用该功能，启用后特定应用程序仍然可以通过在NSH命令后添加符号(&)强制异步执行。

## 自定义NSH初始化

三种方法自定义NSH启动行为，难度依次递增

1. 可以扩展`boards/arm/stm32/stm3240g-eval/src/stm32_appinit.c`中的初始化逻辑，每次启动NSH时都会调用该逻辑，特别是对任何与设备相关的初始化来说是个好地方。
2. 你可以用你想要的启动逻辑替换`app/system/nsh/nsh_main.c中`的示例代码。apps/system/nsh只是一个很小的启动函数（CONFIG_INIT_ENTRYPOINT()）的例子，它可以立即运行并说明如何启动NSH，如果你想要其他东西立即运行，那么你可以写你自己的自定义CONFIG_INIT_ENTRYPOINT()函数，然后从你的自定义CONFIG_INIT_ENTRYPOINT()启动其他任务。(CONFIG_INIT_ENTRYPOINT在defconfig中配置)
3. NSH还支持在首次运行时执行一个启动脚本。这种机制的优点是启动脚本可以包含任何NSH命令，因此可以用很少的代码完成大量的工作。缺点是，创建启动脚本要复杂得多。



## 直接启动自己的应用程序而不是启动NSH

以"hello"为例，修改ENTRYPOINT调用"hello_main"而不是"nsh_main"

RTOS Features -> Tasks and Scheduling -> (hello_main) Application entry point

