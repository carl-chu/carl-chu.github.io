---
title: 项目构建工具Gradle
date: 2021-09-27 11:02:00
tags: 项目构建工具
categories: 学习笔记
---

### 技术分享——项目构建工具Gradle

### **Gradle是什么？**

Java世界主要的三大构建工具：Ant、Maven和Gradle。

Gradle是一个基于Apache Ant和Apache Maven概念的项目自动化构建开源工具，使用基于Groovy的特定领域语言（DSL）来声明项目设置，抛弃了基于XML的各种繁琐配置。

Google选择gradle作为android项目的构建工具，因为gradle非常灵活，除了java外，gradle还支持C++、Groovy、Kotlin、Scala和Swift，并且计划未来支持更多语言。

目前有的spring项目已经从maven切换到gradle，而且安卓项目只支持gradle构建。

例如Spring团队在Spring Boot 2.3.0.M1版本中使用了Gradle来构建项目，这是Spring Boot使用Gradle而非Maven构建的第一个版本。（Spring Framework自2012年的3.2.0版本起就使用Gradle构建）。



**为什么从maven迁移到gradle?**

- 使用gradle和maven构建apache commons lang3比较

![gradle和maven构建速度比较](image-20210914201007764.png)

Gradle运行测试的速度提高了1.7倍，而在使用构建缓存时，构建速度提高了30倍



- 使用gradle和maven构建小项目（10个模块，每个模块50个源文件和50个测试文件）的比较

![gradle和maven构建速度比较](image-20210914201142468.png)

对于clean构建，Gradle 的速度要快 2-3 倍，增量构建的速度要快 7 倍，当 Gradle 任务被缓存时，速度会快 14 倍。



- 使用gradle和maven构建中等项目（100个模块，每个模块100个源文件和100个测试文件）的比较

![gradle和maven构建速度比较](image-20210914201524782.png)

对于clean构建，Gradle 的速度要快 4-5 倍，增量构建的速度要快 40 倍，当 Gradle 任务被缓存时，速度会快 13 倍。

- 使用gradle和maven构建大项目（500个模块，每个模块100个源文件和100个测试文件）的比较：

![gradle和maven构建速度比较](image-20210914201639981.png)

对于clean构建，Gradle 的速度要快 3-10 倍，增量构建的速度要快 85 倍，当 Gradle 任务被缓存时，速度会快 13 倍。

Spring团队考虑由Maven切换到Gradle的主要原因就是为了减少构建项目所需的时间。在开发和测试的过程中，由于减少了等待构建完成所花费的时间，花费在修复错误和实现新功能上所花费的时间也就变多了。

经过Spring团队的统计，使用Gradle构建Spring Boot项目的平均成功构建时间为9分22秒，而在CI和开发人员自己的机器上，基于Maven的完整构建需要一个小时或更长的时间。

[Migrating Spring Boot's Build to Gradle](https://spring.io/blog/2020/06/08/migrating-spring-boot-s-build-to-gradle)



### 为什么gradle速度更快?什么是增量构建和构建缓存？

gradle引入增量构建的概念，使用构建缓存、守护进程方式提高构建速度，结果就是gradle的构建速度要远超maven，平均构建速度比maven快好几倍，而且项目越大差距越明显。

+ 引入增量构建

  Gradle通过跟踪任务的输入和输出，只运行必要的内容，并在可能的情况下只处理有变化的文件，从而避免多余的工作。

  向Gradle提供关于任务的输入和输出的信息。可以将一个任务配置为只有输出。在执行任务之前，Gradle会检查输出，如果输出没有变化，就会跳过任务的执行。在实际构建中，一个任务通常也有输入，包括源文件、资源和属性。Gradle在执行任务之前会检查输入和输出是否有变化。

  **在一个任务第一次执行之前，Gradle会对输入文件进行快照。这个快照包含了一组输入文件和每个文件的内容的哈希值。然后，Gradle执行该任务。如果任务成功完成，Gradle 会获取输出的快照。这个快照包含了输出文件的集合和每个文件内容的哈希值。Gradle将这两个快照持久化，以便在下次执行任务时使用。**

  **Gradle比较的是输入和输出的校验和，而不是只比较文件的时间戳。即使任务每次运行都会覆盖所有的输出文件，但内容并没有改变，任务也不需要再次运行。**

+ 引入构建缓存

  Gradle 通过对任务的所有输入进行哈希计算来构建缓存密钥并将其用来唯一地识别一个任务的输出。启用构建缓存功能后，如果Gradle能在构建缓存中找到该密钥，Gradle将跳过此任务的执行，直接将缓存中的输出复制到构建目录中。

  **缓存可以跨机器共享，如果使用的是持续集成服务器，可以配置Gradle将任务输出推送到共享构建缓存中。当开发者构建时，已经在CI上构建的任务输出被复制到开发者的机器上，可以极大地加快本地构建的速度。**

+ 守护进程

  守护进程是一个一直运行的进程，可以避免每次JVM启动的开销，还可以在内存中缓存项目结构、文件、task等信息，从而提升运行速度。
  
  守护进程是默认启用的，可以使用命令`gradle --stop`来停止守护进程。守护进程会监控内存使用情况，并在系统内存不足时停止空闲状态的守护进程。若守护进程在3个小时或更短时间内不活动，它就会自动终止自己。



### **gradle项目目录结构**

[The Directories and Files Gradle Uses](https://docs.gradle.org/current/userguide/directory_layout.html#dir:project_root)

|————.gradle (1)

|————gradle

|				|————wrapper (2)

|————gradlew (3)

|————gradlew.bat （3）

|————settings.gradle (4)

|————subproject-one (5)

|				|————build.gradle (6)

|————build.gradle (7)

（1）由gradle生成的项目缓存（最多每24小时检查一次，7天未使用则删除）

（2）gradle wrapper 的jar文件和配置

（3）shell脚本和windows批处理脚本，使用gradle-wapper来执行构建

（4）项目的设置文件，定义子项目列表

（5）子项目

（6）子项目配置文件

（7）根项目配置文件



gradle-wapper是一个脚本，用来调用一个已声明版本的gradle。使用wapper可以快速启动和运行gradle项目，无需遵循人工流程。

gradle-wrapper.properties

| 字段名           | 说明                                               |
| ---------------- | -------------------------------------------------- |
| distributionBase | 下载的Gradle压缩包解压后存储的主目录               |
| distributionPath | 相对于distributionBase的解压后的Gradle压缩包的路径 |
| zipStoreBase     | 同distributionBase，只不过是存放zip压缩包的        |
| zipStorePath     | 同distributionPath，只不过是存放zip压缩包的        |
| distributionUrl  | Gradle发行版压缩包的下载地址                       |



//**build.gradle**文件构建脚本块

[Gradle DSL Version 7.2](https://docs.gradle.org/current/dsl/)



java插件

[The Java Plugin (gradle.org)](https://docs.gradle.org/current/userguide/java_plugin.html#sec:java_project_layout)



### **Gradle与Maven的异同点**

Maven:

- 三个内置的构建生命周期(lifecycle)：clean, default, site。[Maven - 构建生命周期简介 (apache.org)](https://maven.apache.org/guides/introduction/introduction-to-the-lifecycle.html)
- 最小的运行单元是目标(goal)
- 插件可以把自己的目标绑定在生命周期的某个阶段(phase)上

Gradle:

+ 构建时有三个不同的阶段：初始化、配置和执行。[详解Gradle项目构建的生命周期](https://www.pianshen.com/article/536459444/)
+ 最小的运行单元是任务(task)，任务之间相互依赖。
+ 可以动态创建任务。

依赖冲突：

gradle：默认选择版本更高的依赖。

maven：默认路径最近原则。



Gradle与Maven的项目目录结构基本相同:

- 生产代码存放在src/main/java
- 测试代码存放在src/test/java

不同之处：
Maven项目依赖由pom.xml定义

```xml
<dependencies>
    <dependency>
        <groupId>log4j</groupId>
        <artifactId>log4j</artifactId>
        <version>1.2.12</version>
    </dependency>
</dependencies>
```

Gradle项目依赖由build.gradle定义

```gradle
dependencies {
    implementation 'log4j:log4j:1.2.12'
}
```

灵活：gradle项目的配置文件是一个groovy脚本文件，在其中可以以编程方式自定义一些构建任务，因为使用了编程方式，这就带给了我们极大的灵活性和便捷性。例如，在打jar包的时候输出jar文件的大小，在gradle中仅需在构建脚本中编写几行代码即可，但在maven中则需要编写maven插件来实现，复杂程度不在一个水平。



### **gradle和maven优缺点总结**

gradle优点：

1. 使用构建缓存、守护进程等方式提高构建速度，项目构建速度快。
2. 采用代码逻辑的方式进行构建，使其非常灵活。
3. 配置代码减少，大约为maven的pom配置的四分之一。

缺点：

1. 由于gradle配置过于灵活，导致它的构建比基于maven构建更难以维护和理解。
2. 更新速度很快，语法变化较大。

缓存带来的问题：

gradle内部存在缓存机制，当文件输入和输出都没改变的情况下，认为这就是没变的代码，直接进行输出。但当你改变的依赖包版本，它有时并没更新，这也是缓存机制的问题。



maven缺点:

1. 配置文件是xml格式的，如果项目依赖的包比较多，xml文件会变得非常长。
2. xml文件不太灵活，如果需要在构建过程中添加自定义逻辑会非常麻烦。
3. 虽然maven稳定但对新版java支持不足，需要更新内置的maven插件。
4. 使用maven构建的项目都会经过几个生命流程，内部没有缓存机制，随着项目越来越大重新构建所花费的时间也就越长。

maven优点：

1. maven项目比较容易看懂，上手简单。
2. maven非常稳定



### 从maven迁移到gradle

[从阿帕奇马文迁移构建 (gradle.org)](https://docs.gradle.org/current/userguide/migrating_from_maven.html)

使用`gradle init`命令，此命令通常用于新项目的初始化，但也可以使用此命令自动将现有的Maven构建转换为Gradle，包括解析现有的pom并生成相应的gradle构建脚本，如果是多项目，还会创建一个设置脚本。

不一定会有转换问题，但是还是需要做一些手动工作



### 总结与展望

总结：

Ant：Ant的功能虽然强大，但过于灵活，规范性不足，对目录结构及build.xml没有默认约定，且没有统一的项目依赖管理。

Maven：Maven解决了规范性的问题，也顺带解决了依赖项统一管理的问题，但由于规范性太强，灵活性不足，pom.xml采用Xml结构，项目一大，Xml就显得冗长。

Gradle：综合了Ant和Maven的优点，吸收了Ant中task的思想，然后把Maven的目录规范以及仓库思想也融合了进来，但允许用户自由的修改默认的规范（如，可随意修改源码目录），配置文件则采用Groovy语言来书写，Groovy是一门可编程语言，配置文件本身就可以视为一份源代码，并最终交由Gradle来处理执行。

展望：

虽然目前大家的电脑性能比较强劲，在做项目构建的时候对构建性能的优势不是那么迫切，但是对大型项目来说，一次构建可能会需要很长时间，尤其是对于自动化构建和CI环境来说，当然是构建地越快越好。从Spring 和 Spring Boot等项目从Maven迁移到Gradle来看，Gradle有可能成为未来主流的项目构建工具。

