---
title: SpringCloud Alibaba
date: 2022-09-29 19:35:00
tags: 
categories: 学习笔记
index_img: /img/springcloud.png
---

# SpringCloud Alibaba

[版本说明 · alibaba/spring-cloud-alibaba Wiki · GitHub](https://github.com/alibaba/spring-cloud-alibaba/wiki/版本说明)

```xml
<!--spring cloud 2021.0.1-->
            <dependency>
                <groupId>org.springframework.cloud</groupId>
                <artifactId>spring-cloud-dependencies</artifactId>
                <version>${spring.cloud.version}</version>
                <type>pom</type>
                <scope>import</scope>
            </dependency>
            <!--Spring cloud alibaba 2021.0.1.0-->
            <dependency>
                <groupId>com.alibaba.cloud</groupId>
                <artifactId>spring-cloud-alibaba-dependencies</artifactId>
                <version>${spring.cloud.alibaba.version}</version>
                <type>pom</type>
                <scope>import</scope>
            </dependency>
```



## Nacos

[Nacos discovery · alibaba/spring-cloud-alibaba Wiki · GitHub](https://github.com/alibaba/spring-cloud-alibaba/wiki/Nacos-discovery)

```xml
<dependency>
    <groupId>com.alibaba.cloud</groupId>
    <artifactId>spring-cloud-starter-alibaba-nacos-discovery</artifactId>
</dependency>
```

下载客户端

默认集群模式（单机需要进行修改）：编辑nacos\bin\startup.cmd，将`set MODE="cluster"`改为`set MODE="standalone"`

可在`nacos\conf\application.properties`中添加数据源

```properties
### If use MySQL as datasource:
spring.datasource.platform=mysql

### Count of DB:
db.num=1

### Connect URL of DB:
db.url.0=jdbc:mysql://127.0.0.1:3306/nacos_config?characterEncoding=utf8&connectTimeout=1000&socketTimeout=3000&autoReconnect=true&useUnicode=true&useSSL=false&serverTimezone=UTC
db.user.0=root
db.password.0=17221273
```

账号密码默认：nacos



集群部署：

使用nginx进行反向代理，编辑nginx.conf文件

```conf
upstream nacoscluster {
	server 127.0.0.1:8848;
	server 127.0.0.1:8849;
	server 127.0.0.1:8850;
}
server {
	listen 8847;	#nginx服务监听的端口
	server_name localhost;
	
	location /nacos/{	#/nacos值的是路径前缀，localhost:8847/nacos
		proxy_pass http://nacoscluster/nacos/;
	}
}
```



需要修改启动模式为cluster，或使用命令` -m -standalone`

添加`cluster.conf`文件写入其他nacos的地址

将项目中nacos注册的地址`spring.clou.nacos.server-addr`修改为nginx监听的地址

## 负载均衡Ribbon





## 负载均衡LoadBalancer

```xml
<dependency>
            <groupId>org.springframework.cloud</groupId>
            <artifactId>spring-cloud-starter-loadbalancer</artifactId>
        </dependency>
```



[微服务生态组件之Spring Cloud LoadBalancer详解和源码分析_Firstlucky77的博客-CSDN博客_spring.cloud.loadbalancer](https://blog.csdn.net/Firstlucky77/article/details/124969555)



## OpenFeign

```xml
<dependency>
            <groupId>org.springframework.cloud</groupId>
            <artifactId>spring-cloud-starter-openfeign</artifactId>
        </dependency>
```

配置类上使用`@EnableFeignClients`开启openfeign

### OpenFeign各种问题

[(1条消息) 谈谈Spring Cloud OpenFeign远程调用性能优化_java熬夜党的博客-CSDN博客](https://blog.csdn.net/weixin_62421895/article/details/126481931)

### 日志配置

全局配置：

使用`@Configuration`会将配置的作用所有的服务提供方



局部配置：

1.通过配置类：如果针对某一个服务进行配置，就不要加@Configuration

```java
//@Configuration
public class FeignConfig {
	
    //Logger是feign包下的
    @Bean
    public Logger.Level feignLoggerLevel(){
        return Logger.Level.FULL;
    }

}


@FeignClient(name = "service-bravo", configuration = FeignConfig.class)
public interface BravoFeign {

    @GetMapping("/bravo-test/getBravoTestString")
    String getBravoTestString();

}
```



2.通过配置文件

```yaml
feign:
  client:
    config: 
   	 #服务名
      service-alpha:
        loggerLevel: FULL
```

### 契约配置

老版本springcloud使用的是feign，netflix停更后springcloud改用openFeign且支持了springmvc注解，契约配置是对老版本SpringCloud使用的feign的注解的兼容

注意：修改契约配置后，feign接口不再支持springmvc的注解，需要使用feign原生的注解



### 超时时间配置

全局配置：
```java
@Bean
    public Request.Options options(){
        return new Request.Options(5, TimeUnit.SECONDS,3, TimeUnit.SECONDS, true);
    }
```



yaml配置

```yaml
feign:
  client:
    config:
      service-alpha: #服务名
        loggerLevel: FULL
        #连接超时时间，默认2秒
        connectTimeout: 5000
#        请求超时时间，默认5秒
        readTimeout: 5000
```



### 自定义拦截器

```java
@Slf4j
public class FeignRequestInterceptor implements RequestInterceptor {
    @Override
    public void apply(RequestTemplate requestTemplate) {

        ServletRequestAttributes servletRequestAttributes = (ServletRequestAttributes)RequestContextHolder.getRequestAttributes();
        HttpServletRequest request = servletRequestAttributes.getRequest();
        Enumeration<String> headerNames = request.getHeaderNames();
        while (headerNames.hasMoreElements()){
            String s = headerNames.nextElement();
            String header = request.getHeader(s);
            System.out.println("[header] key:" + s + ", value:" + header);
        }

        log.info("feign拦截器：{}", requestTemplate.toString());
    }
}
```





## 配置中心Nacos-config

权限功能的开启需要在nacos配置文件中配置`nacos.core.auth.enabled=true`

```xml
<dependency>
            <groupId>com.alibaba.cloud</groupId>
            <artifactId>spring-cloud-starter-alibaba-nacos-config</artifactId>
        </dependency>
```

spring-cloud-dependencies 2020.0.0 版本不在默认加载bootstrap 文件，如果需要加载bootstrap 文件需要手动添加依赖。

```xml
<dependency>
            <groupId>org.springframework.cloud</groupId>
            <artifactId>spring-cloud-starter-bootstrap</artifactId>
        </dependency>
```

配置一旦修改非properties格式的配置，需要在bootstrap.yaml中设置`file-extension`

```yaml
#bootstrap.yaml
spring:
  application:
    name: service-alpha
  cloud:
    nacos:
      server-addr: localhost:8848
      username: nacos
      password: nacos
      config:
        namespace: public
        file-extension: yaml
```

注意：`spring.application.name=service-alpha`必须写在`bootstrap.properties`中才能动态感知配置的变化，写在`application.properties`中无法感知。



使用`@Value`获取的配置信息无法动态感知，需要配合使用`@RefreshScope`



## 服务限流\熔断\降级sentinel

[annotation-support (sentinelguard.io)](https://sentinelguard.io/zh-cn/docs/annotation-support.html)

### @SentinelResource(value="", blockHandler="")

#### value：

- 资源名称，必需项（不能为空）

#### entryType：

- entry 类型，可选项（默认为 `EntryType.OUT`），作用：标记流量的方向，指明是出口流量，还是入口流量；取值 IN/OUT ,默认是OUT。

#### blockHandler/blockHandlerClass

- blockHandler 函数访问范围需要是 `public`
- **返回类型需要与原方法相匹配**，**参数类型需要和原方法相匹配并且最后加一个额外的参数**，类型为 `BlockException`。
- blockHandler 函数默认需要和原方法在同一个类中。若希望使用其他类的函数，则可以指定 `blockHandlerClass` 为对应的类的 `Class` 对象，注意对应的函数必需为 static 函数，否则无法解析。

#### fallback

可选项，用于在抛出异常的时候提供 fallback 处理逻辑。fallback 函数可以针对所有类型的异常（除了 `exceptionsToIgnore` 里面排除掉的异常类型）进行处理。fallback 函数签名和位置要求：

- 返回值类型必须与原函数返回值类型一致；
- 方法参数列表需要和原函数一致，或者可以额外多一个 `Throwable` 类型的参数用于接收对应的异常。
- fallback 函数默认需要和原方法在同一个类中。若希望使用其他类的函数，则可以指定 `fallbackClass` 为对应的类的 `Class` 对象，注意对应的函数必需为 static 函数，否则无法解析。



dashboard去github上下载jar包

启动使用命令`java -jar sentinel-dashboard-1.8.3.jar`

可以加入参数`-Dserver.port=8858`等



整合SpringCloudAlibaba

```xml
<dependency>
            <groupId>com.alibaba.cloud</groupId>
            <artifactId>spring-cloud-starter-alibaba-sentinel</artifactId>
        </dependency>
```



统一异常处理实现`BlockExceptionHandler`接口

使用了@SentinelResource注解的方法不会走统一异常处理接口，需要自行指定blockhandler

### 流控模式

#### 直接

对所填的资源直接进行流控

#### 关联

`关联资源`达到`单机阈值`触发资源名所填资源的流控

#### 链路

资源名所填资源达到`单机阈值`后对`入口资源`进行流控

**使用链路流控模式需要进行以下配置**

```properties
spring.cloud.sentinel.web-context-unify=false
```



### 流控效果

#### 快速失败

指的是流量到达单机阈值后，剩余的流量被直接拒绝

#### Warm Up（预热）

即预热\冷启动方式，可以设置一个预热时长。当系统长期处于低水位的情况下，当流量突然增加时，直接把系统拉升到高水位可能瞬间把系统压垮。通过“冷启动”让通过的流量缓慢增加，在一定时间内逐渐增加到阈值上限，给冷系统一个预热的时间，避免冷系统被压垮。

冷加载因子：codeFactor默认是3，即请求QPS从threshold/3开始，经预热时长逐渐升至设定的QPS阈值。

#### 排队等待

可以设置超时时间，达到单机阈值后，进行排队



### 熔断策略

#### 慢调用比例(SLOW_REQUEST_RATIO)

选择以慢调用比例作为阈值，需要设置允许的慢调用RT（即最大的响应时间），请求的响应时间大于该值则统计为慢调用。当单位统计时长（statIntervalMs）内请求数目大于设置的最小请求数目，并且慢调用的比例大于阈值，则接下来的熔断时长内请求会自动被熔断。经过熔断时长后熔断器会进入探测恢复状态（HALF-OPEN状态），若接下来的一个请求响应时间小于设置的慢调用RT则结束熔断，若大于设置的慢调用RT则会再次被熔断。



#### 异常比例



#### 异常数



### 整合Openfeign

```properties
feign.sentinel.enabled: true
```

写一个类实现feign接口，@FeignClient注解写上fallback的参数

```java
@FeignClient(name = "service-bravo", configuration = FeignConfig.class, fallback = BravoFeignFallback.class)
public interface BravoFeign {

    @GetMapping("/bravo-test/getBravoTestString")
    String getBravoTestString();

}

@Component
public class BravoFeignFallback implements BravoFeign{
    @Override
    public String getBravoTestString() {
        return "BravoFeign invoke method getBravoTestString fallback";
    }
}
```





### 热点参数流控

dashboard配置热点参数流控中的资源名必须是@SentinelResource(value="资源名")中配置的资源名， 热点规则必须配合使用@SentinelResource注解

### 系统保护规则

全局规则

Load自适应（仅对Linux/Unix-like机器生效）

CPU usage（1.5.0+版本）：当系统CPU使用率超过阈值即触发系统保护（取值范围0.0-1.0），比较灵敏。

平均RT：当单台机器上所有入口流量的平均RT达到阈值即触发系统保护，单位是毫秒。

并发线程数：当单台机器上所有入口流量的并发线程数达到阈值即触发系统保护。

入口QPS：当单台机器上所有入口流量的QPS达到阈值即触发系统保护。

### 规则持久化

Sentinel规则的推送有三种模式：

| 推送模式 | 说明                                                         | 优点                         | 缺点                                                         |
| -------- | ------------------------------------------------------------ | ---------------------------- | ------------------------------------------------------------ |
| 原始模式 | API将规则推送至客户端并直接更新到内存中，扩展写数据源(WritableDataSource) | 简单，无任何依赖             | 不保证一致性；规则保存在内存中，重启即消失，严重不建议用于生产环境。 |
| Pull模式 | 扩展写数据源(WritableDataSource)，客户端主动向某个规则管理中心定期轮询拉取规则，这个规则中心可以是RDBMS、文件等 | 简单，无任何依赖；规则持久化 | 不保证一致性；实时性不保证，拉取过于频繁也可能会有性能问题。 |
| Push模式 | 扩展读数据源(ReadableDataSource)，规则中心统一推送，客户端通过注册监听器的方式时刻监听变化，比如使用Nacos、Zookeeper等配置中心。这种方式有更好的实时性和一致性保证。生产环境下一般采用Push模式的数据源。 | 规则持久化；一致性；快速     | 引入第三方依赖                                               |

Push模式：

```xml
<dependency>
            <groupId>com.alibaba.csp</groupId>
            <artifactId>sentinel-datasource-nacos</artifactId>
        </dependency>
```

在nacos中新建配置json格式

```json
[
    {
        "resource": "/alpha-test/getTestString",
        "controlBehavior":,
        "count": 2,
        "grade": 1,
        "limitApp": "default",
        "strategy": 0
    }
]
```

[如何使用 · alibaba/Sentinel Wiki · GitHub](https://github.com/alibaba/Sentinel/wiki/如何使用)

添加配置

```yaml
spring:
	cloud:
		sentinel:
			datasource:
				flow-rule: #flow-rule为自定义名字
					nacos:
						server-addr: 127.0.0.1:8848
						username: nacos
						password: nacos
						dataId: order-sentinel-flow-rule
						ruleType: flow #从RuleType类中查看的类型
```



## 分布式事务Seata

Seata是一款开源的分布式事务解决方案，致力于提供高性能和简单易用的分布式事务服务。Seata为用户提供了AT、TCC、SAGA和XA事务模式，为用户打造一站式的分布式解决方案。AT模式是阿里首推的模式，阿里云上有商用版本的GTS（Globle Transaction Service全局事务服务）

官网：[Seata](https://seata.io/zh-cn/)

### Seata的三大角色

TC(Transaction Coordinator)- 事务协调者

- 维护全局和分支事务的状态，驱动全局事务提交或回滚

TM(Transaction Manager)- 事务管理器

- 定义全局事务范围：开始全局事务、提交或回滚全局事务。

RM(Resource Manager)- 资源管理器

- 管理分支事务处理的资源，与TC交谈以注册分支事务和报告分支事务的状态，并驱动分支事务提交或回滚。

其中TC为单独部署的Server服务端，TM和RM为嵌入到应用中的Client客户端。

### AT模式

AT模式是一种无侵入的分布式事务解决方案。

阿里seata框架实现了该模式。

在AT模式下，用户只需关注自己的“业务SQL”，用户的“业务SQL”作为一阶段，Seata框架会自动生成事务的二阶段提交和回滚操作。

AT模式如何做到对业务的无侵入：

- 一阶段

在一阶段，Seata会拦截“业务SQL”，首先解析SQL语义，找到“业务SQL”要更新的业务数据，在业务数据被更新前，将其保存成“before image”，然后执行“业务SQL”更新业务数据，在业务数据更新之后，再将其保存成“after image”，最后生成行锁。以上操作全部在一个数据库事务内完成，这样保证了一阶段操作的原子性。

- 二阶段提交

二阶段如果是提交的话，因为“业务SQL”在一阶段已经提交至数据库，所以Seata框架只需要将一阶段保存的快照数据和行锁删掉，完成数据清理即可。

- 二阶段回滚

二阶段如果是回滚的话，Seata就需要回滚一阶段已经执行的“业务SQL”，还原业务数据。回滚的方式便是用“before image”还原业务数据；但在还原之前要首先要校验脏写，对比“数据库当前业务数据”和“after image”，如果两份数据完全一致就说明没有脏写，可以还原业务数据，如果不一致就说明有脏写，出现脏写就需要转人工处理。

### TCC模式

1.侵入性比较强，并且得自己实现相关事务控制逻辑

2.整个过程基本没有锁，性能更高

TCC模式需要用户根据自己的业务场景实现Try、Confirm和Cancel三个操作；事务发起方在一阶段执行Try方式，在二阶段提交执行confirm方法，二阶段回滚执行cancel方法。



### Seata快速开始

Seatac Server（TC）环境搭建

Server端存储模式（store.mode）支持三种：

- file：单机模式，全局事务会话信息内存中读写并持久化本地文件root.data，性能较高（默认）
- db：(mysql5.7+)高可用模式，全局事务会话信息通过db共享，相应性能差些
  - 打开conf/file.conf
  - 修改mode="db"
  - 修改数据库连接信息（url/username/password）
  - 创建数据库seata
  - 新建表：可以去seata提供的资源信息中下载：
    - [seata/script at master · seata/seata · GitHub](https://github.com/seata/seata/tree/master/script)
- redis：Seata-Server1.3及以上版本支持，性能较高，存在事务信息丢失的风险，需提前配置适合当前场景的redis持久化配置

资源目录

- client：存放client端sql脚本，参数配置
- config-center：各个配置中心参数导入脚本，config.txt（包含server和client，原名nacos-config.sh）为通用参数文件
- server：server端数据库脚本及各个容器配置

### db存储模式+Nacos（注册&配置中心）部署（高可用）

1. 修改conf/registry.conf中注册中心和配置中心 `type`和nacos相关配置（config也可以使用本地文件）
2. [seata/script at master · seata/seata · GitHub](https://github.com/seata/seata/tree/master/script)下载config-center中的文件，修改`config.txt`中的相关配置：修改`store.mode=DB`及修改`store.db`相关配置。
3. 执行nacos/nacos-config.sh（可以使用git-bash.exe，需要设置参数如-u username和-w password）将`config.txt`中的配置注册到配置中心，执行后应该会产生4个失败，分别是config.txt中4个未配置的参数，不影响单机使用（seata1.4.2版本不需要）。
4. 启动seata，执行seata解压目录中bin/seata-server.bat

seata支持的启动参数

| 参数 | 全写         | 作用                       | 备注                                                         |
| ---- | ------------ | -------------------------- | ------------------------------------------------------------ |
| -h   | --host       | 指定在注册中心注册的IP     | 不指定时获取当前的IP，外部访问部署在云环境和容器中的server建议指定 |
| -p   | --port       | 指定server启动的端口       | 默认为8091                                                   |
| -m   | --storeMode  | 事务日志存储方式           | 支持file，db，redis，默认为file，redis需seata-server1.3版本以上 |
| -n   | --serverNode | 用于指定seata-server节点ID | 如1，2，3...，默认为1                                        |
| -e   | --seataEnv   | 指定seata-server运行环境   | 如dev，test等，服务启动时会使用registry-dev.conf这样的配置   |



### Seata Client快速开始

第一步：添加pom依赖

```xml
<dependency>
            <groupId>com.alibaba.cloud</groupId>
            <artifactId>spring-cloud-starter-alibaba-seata</artifactId>
        </dependency>
```

第二步：各微服务对应数据库中添加undo_log表

[seata/script at master · seata/seata · GitHub](https://github.com/seata/seata/tree/master/script)

/client/at/db/mysql.sql



第三步：Client端加入对应的配置中心

```yaml
seata:
  registry:
    type: nacos
    nacos:
      server-addr: 127.0.0.1:8848
      application: seata-server
      username: nacos
      password: nacos
      group: SEATA_GROUP
  config:
    type: nacos
    nacos:
      server-addr: 127.0.0.1:8848
      group : "SEATA_GROUP"
      namespace: ""
      username: "nacos"
      password: "nacos"
```

第四步：修改seata解压目录中 registry.conf，配置nacos作为registry.type和config.type，并修改配置中心相关配置。

第五步：为seata新建库表

第六步：启动seata



## 网关gateway

前端直接访问微服务会存在诸多的问题：

- 每个业务都会需要鉴权、限流、权限校验、跨域等逻辑，可以将这些逻辑抽离出来统一放到一个地方做
- 如果业务量比较简单的话，这种方式前期不会有什么问题，但随着业务越来越复杂，比如淘宝、亚马逊打开一个页面可能会涉及到数百个微服务协同工作，如果每一个微服务都分配一个域名的话，一方面客户端代码会很难维护，涉及到数百个域名，另一方面是连接数瓶颈，想象一下你打开一个APP，通过抓包发现涉及到了数百个远程调用，这在移动端下会显得非常低效。
- 服务拆分麻烦

所谓的API网关，就是指系统的统一入口，它封装了应用程序的内部结构，为客户端提供统一的服务，一些与业务本身功能无关的公共逻辑可以在这里实现，比如认证、鉴权、监控、路由转发等等。



### Spring Cloud Gateway功能特性

- 基于Spring Framework 5，Project Reactor和Spring Boot 2.0进行构建；
- 动态路由：能够匹配任何请求属性；
- 支持路径重写；
- 集成Spring Cloud服务发现功能（Nacos、Eruka）；
- 可集成流控降级功能（Sentinel、Hystrix）；
- 可以对路由指定易于编写的Predicate（断言）和Filter（过滤器）；



### 核心概念

- 路由（route）

路由是网关中最基础的部分，路由信息包括一个ID、一个目的URL、一组断言工厂、一组Filter组成。如果断言为真，则说明请求的URL和配置的路由匹配。

- 断言（predicate）

Java8中的断言函数，SpringCloud Gateway中的断言函数类型是Spring5.0框架中的ServerWebExchange。断言函数允许开发者去定义匹配的Http request中的任何信息，比如请求头和参数等。

- 过滤器（Filter）

SpringCloud Gateway中的Filter分为Gateway Filter和Global Filter。Filter可以对请求和响应进行处理。

### SpringCloud Gateway快速开始

引入依赖

```xml
<dependency>
            <groupId>org.springframework.cloud</groupId>
            <artifactId>spring-cloud-starter-gateway</artifactId>
        </dependency>
```

添加配置

```yaml
spring:
  cloud:
    gateway:
      routes: #数组路由[路由 就是指定当前请求满足什么条件的时候转到哪个微服务]
        - id: gateway
          uri: lb://service-alpha   #请求要转发到的地址[http://127.0.0.1:8001]，或从注册中心获取服务
          order: 1    #路由的级别，数字越小级别越高
          predicates: #断言（就是路由转发要满足的条件），当请求路径满足Path指定的规则时，才进行路由转发
            - Path=/service-alpha/**
          filters:
            - StripPrefix=1 #转发之前去掉第一层路径
```

注意：需要引入openfeign依赖才能在uri处使用负载均衡进行服务调用

```yaml
spring:
  cloud:
    gateway:
#      routes: #数组路由[路由 就是指定当前请求满足什么条件的时候转到哪个微服务]
#        - id: gateway
#          uri: lb://service-alpha   #请求要转发到的地址[http://127.0.0.1:8001]，或从注册中心获取服务
#          order: 1    #路由的级别，数字越小级别越高
#          predicates: #断言（就是路由转发要满足的条件），当请求路径满足Path指定的规则时，才进行路由转发
#            - Path=/service-alpha/**
#          filters:
#            - StripPrefix=1 #转发之前去掉第一层路径
      discovery:
        locator:
          enabled: true
```

使用`spring.cloud.gateway.discovery.locator.enabled=true`会自动根据路径中的第一层路径寻找服务，且会自动去掉此路径（虽然简单但不太常用，不直观）



### 路由断言工厂（Route Predicate Factories）配置

作用：当请求gateway的时候，使用断言对请求进行匹配，如果匹配成功就路由转发，如果匹配失败就返回404.

SpringCloud Gateway包括许多内置的断言工厂，所有这些断言都与HTTP请求的不同属性匹配。具体如下：

- 基于DateTime类型的断言工厂

此类型的断言根据时间做判断，主要有三个：

AfterRoutePredicateFactory：接收一个日期参数，判断请求日期是否晚于指定日期

BeforeRoutePredicateFactory：接收一个日期参数，判断请求日期是否早于指定日期

BetweenRoutePredicateFactory：接收两个日期参数，判断请求日期是否在指定时间段

```yaml
predicates:
	- After=2022-09-25T18:02:59.789+08:00[Asia/Shanghai]
```

- 基于远程地址的断言工厂

RemoteAddrRoutePredicateFactory：接收一个ip地址段，判断请求主机地址是否在地址段中

```yaml
predicates:
	- RemoteAddr=192.167.1.1/24
```

- 基于Cookie的断言工厂

CookieRoutePredicateFactory：接收两个参数，cookie名字和一个正则表达式，判断请求cookie是否具有给定名称且值与正则表达式匹配。

```yaml
- Cookie=chocolate,ch.
```

- 基于Header的断言工厂

HeaderRoutePredicateFactory：接收两个参数，标题名称和正则表达式。判断请求Header是否具有给定名称且值与正则表达式匹配。

```yaml
- Header=X-Request-Id, \d+
```

- 基于Host的断言工程

HostRoutePredicateFactory：接收一个参数，主机名模式。判断请求的Host是否满足匹配规则。

```yaml
- Host=**.testhost.org
```

- 基于Method请求方法的断言工厂

MethodRoutePredicateFactory：接收一个参数，判断请求类型是否跟指定类型匹配。

```yaml
- Method=GET
```

- 基于Path请求路径的断言工厂

PathRoutePredicateFactory：接收一个参数，判断请求的URI部分是否满足路径规则。

```yaml
- Path=/foo/{segment}
```

- 基于Query请求参数的断言工厂

QueryRoutePredicateFactory：接收两个参数，请求param和正则表达式，判断请求参数是否具有给定名称且值与正则表达式匹配。

```yaml
-Query=bar, ba.
```

- 基于路由权重的断言工厂

WeightRoutePredicateFactory：接收一个[组名，权重]，然后对于同一个组内的路由按照权重转发。

```yaml
routes:
	-id: weight_route1
	 uri: host1
     predicates:
     	-Path=/product/**
     	-Weight=group3,1
     -id: weight_route2
	 uri: host2
     predicates:
     	-Path=/product/**
     	-Weight=group3,9
```

### 自定义断言工厂

[Spring Cloud Gateway](https://docs.spring.io/spring-cloud-gateway/docs/3.1.1/reference/html/)

自定义路由断言工厂需要继承`AbstractRoutePredicateFactory`类，重写apply方法的逻辑。在apply方法中可以通过exchange.getRequest()拿到ServletHttpRequest对象，从而可以获取到请求的参数、请求方式、请求头等信息。

1. 必须为spring组件

2. 类命名必须加上RoutePredicateFactory作为结尾。

3. 必须继承`AbstractRoutePredicateFactory`
4. 必须声明静态内部类，声明属性来接收配置文件中对应的断言的信息

参考官方的写法



### 局部过滤器

[Spring Cloud Gateway](https://docs.spring.io/spring-cloud-gateway/docs/3.1.1/reference/html/#gatewayfilter-factories)



### 全局过滤器

针对所有路由请求，一旦定义就会投入使用

写一个类实现`GlobalFilter`接口



### 跨域处理

配置文件方式

```yaml
spring:
  cloud:
    gateway:
      globalcors:
        cors-configurations:
          '[/**]': #允许跨域访问的资源
            allowedOrigins: "*" #跨域允许来源
            allowedMethods:
            - GET
```

### 整合Sentinel流控降级

添加依赖：

```xml
<dependency>
	<groupId>com.alibaba.cloud</groupId>
	<artifactId>spring-cloud-starter-alibaba-sentinel</artifactId>
</dependency>
<dependency>
	<groupId>com.alibaba.cloud</groupId>
	<artifactId>spring-cloud-alibaba-sentinel-gateway</artifactId>
</dependency>
```

添加配置：

```yaml
sentinel:
	transport:
		dashboard: 127.0.0.1:8080
```





## 链路追踪SkyWalking

skywalking8.7.0之后的版本，agent的相关代码被抽离出skywalking当中，需要自行下载agent。



执行startup.cmd启动skywalking客户端

启动成功后会启动两个服务，一个是skywalking-oap-server，一个是skywalking-web-ui

skywalking-oap-server服务启动后会暴露11800 和 12800 两个端口，分别为收集监控数据的端口11800和接受前端请求的端口12800，修改端口可以修改config/applicaiton.yml
https://blog.csdn.net/u012267926/article/details/119793247

skywalking-web-ui服务会占用 8080 端口， 修改端口可以修改webapp/webapp.yml



windows的idea中在VM  options中添加以下参数后启动

```
#skywalking-agent.jar的磁盘路径
-javaagent:D:\programming\apache-skywalking-java-agent-8.10.0\skywalking-agent\skywalking-agent.jar
#在skywalking上显示的服务名
-DSW_AGENT_NAME=service-gateway
#skywalking的collector服务的ip和端口
-DSW_AGENT_COLLECTOR_BACKEND_SERVICES=127.0.0.1:11800
```

linux命令

```
java -javaagent:/user/skywalking-agent.jar -DSW_AGENT_NAME=gateway -DSW_AGENT_COLLECTOR_BACKEND_SERVICES=127.0.0.1:11800 -jar service-gateway.jar
```

### 在拓扑图中显示gateway服务

需要从/skywalking-agent/optional-plugins中复制一份`apm-spring-cloud-gateway`插件到/skywalking-agent/plugins中



### 使用MySQL持久化

修改config/application.yml，将`storage.selector`的h2修改为mysql，并修改MySQL配置

建好数据库后skywalking启动时会自动把表建好

**注意：oap服务依赖中没有mysql驱动，需要自行复制一份mysql驱动放到/oap-libs目录中**



### 自定义链路追踪

添加依赖

```xml
<dependency>
	<groupId>org.apache.skywalking</groupId>
	<artifactId>apm-toolkit-trace</artifactId>
    <version>9.1.0</version>
</dependency>
```

在业务方法上添加`@Trace`注解

记录方法参数和返回值使用`@Tag`/`@Tags`注解（需要配合`@Trace`），给一个key（自定义），需要记录返回值则对应key的value="returnedObj"，若需要记录参数则value="arg[0]"

```java
@Tags({@Tag(key="param", value="arg[0]"),
       @Tag(key="returnVal", value="returnedObj")})
```

**返回的是一个对象的话需要重写该对象的toString方法，否则显示的返回值为该对象的地址**



### 集成日志框架

[ SpringCloud链路追踪SkyWalking-第六章-日志采集_Blueeyedboy521的博客-CSDN博客_skywalking采集业务日志](https://blog.csdn.net/Blueeyedboy521/article/details/126509520)

1. 引入依赖

```xml
<dependency>
	<groupId>org.apache.skywalking</groupId>
	<artifactId>apm-toolkit-logback-1.x</artifactId>
    <version>9.1.0</version>
</dependency>
```



2. logback配置文件中<appender><encoder><layout class=....TraceIdPatternLogbackLayut>,layout指定SkyWalking中的Layout，可以在<pattern>中使用%tid输出trace_id

如果需要使用skywalking记录所有项目的日志可以在logback配置中加入以下配置按需打印

```xml
<appender name="grpc-log" class="ch.qos.logback.core.ConsoleAppender">
        <encoder class="ch.qos.logback.core.encoder.LayoutWrappingEncoder">
            <layout class="org.apache.skywalking.apm.toolkit.log.logback.v1.x.TraceIdPatternLogbackLayout">
                <Pattern>%d{yyyy-MM-dd HH:mm:ss.SSS} [%tid] [%thread] %-5level %logger{36} -%msg%n</Pattern>
            </layout>
        </encoder>
    </appender>
```

