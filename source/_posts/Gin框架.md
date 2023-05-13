---
title: Gin框架
date: 2023-02-05 21:50:00
tags: Go
categories: 学习笔记
---

Gin框架

# Gin介绍

Gin是一个Go（Golang）编写的轻量级http web框架，运行速度非常快，Gin最擅长的就是Api接口的高并发。

Gin官网：https://gin-gonic.com/zh-cn

Gin Github地址：https://github.com/gin-gonic/gin

Gin框架中文文档：[文档 | Gin Web Framework (gin-gonic.com)](https://gin-gonic.com/zh-cn/docs/)

# Gin环境搭建

1. 下载并安装

```bash
go get -u github.com/git-gonic/gin
```

2. 将gin引入到代码中

```go
import "github.com/gin-gonic/gin"
```

3. （可选）如果使用诸如http.StatusOK之类的常量，则需要引入net/http包

```go
import "net/http"
```

4. 新建Main.go路由

```go
import (
	"github.com/gin-gonic/gin"
	"net/http"
)

func main() {

	//创建一个默认的路由引擎
	engine := gin.Default()
	//配置路由
	engine.GET("/", func(context *gin.Context) {
		context.String(http.StatusOK, "值：%v", "你好golang")
	})
	engine.GET("/get", func(context *gin.Context) {
		context.String(http.StatusOK, "你发送了一个Get请求")
	})
	engine.POST("/post", func(context *gin.Context) {
		context.String(http.StatusOK, "你发送了一个Post请求")
	})
	engine.PUT("/put", func(context *gin.Context) {
		context.String(http.StatusOK, "你发送了一个Put请求")
	})
	engine.DELETE("/delete", func(context *gin.Context) {
		context.String(http.StatusOK, "你发送了一个delete请求")
	})
	engine.Run(":8000") //启动一个web服务
}
```

# golang程序热加载

热加载就是当我们对代码进行修改时，程序能够自动重新加载并执行，省去了每次手动重新编译。

beego中可以使用官方提供的bee工具来热加载项目，但是gin中并没有官方提供的热加载工具，这个时候可以借助第三方工具。

工具1（推荐）：https://github.com/gravityblast/fresh

```bash
go get github.com/pilu/fresh
```

工具2：https://github.com/codegangsta/gin

```go
go get -u github.com/codegangsta/gin
```



# Gin框架中的路由

## 路由概述

路由（Routing）是由一个URI（或者叫路径）和一个特定的HTTP方法（GET、POST等）组成的，涉及到应用如何响应客户端对某个网站节点的访问。

RESTful API是目前比较成熟的一套互联网应用程序的API设计理论。

在RESTful架构中，每个网址代表一种资源，不同的请求方式表示执行不同的操作：

| GET（SELECT）    | 从服务器取出资源（一项或多项）                 |
| ---------------- | ---------------------------------------------- |
| POST（CREATE）   | 在服务器新建一个资源                           |
| PUT（UPDATE）    | 在服务器更新资源（客户端提供改变后的完整资源） |
| DELETE（DELETE） | 从服务器删除资源                               |

## c.String(),c.JSON(),c.JSONP(),c.XML(),c.HTML()

### 返回一个字符串

```go
//创建一个默认的路由引擎
	engine := gin.Default()
	//配置路由
	engine.GET("/", func(context *gin.Context) {
		context.String(http.StatusOK, "值：%v", "你好golang")
	})
	engine.GET("/get", func(context *gin.Context) {
		context.String(http.StatusOK, "你发送了一个Get请求")
	})
	engine.POST("/post", func(context *gin.Context) {
		context.String(http.StatusOK, "你发送了一个Post请求")
	})
	engine.PUT("/put", func(context *gin.Context) {
		context.String(http.StatusOK, "你发送了一个Put请求")
	})
	engine.DELETE("/delete", func(context *gin.Context) {
		context.String(http.StatusOK, "你发送了一个delete请求")
	})
```

### 返回json

```go
engine.GET("/json1", func(c *gin.Context) {
		c.JSON(http.StatusOK, gin.H{
			"success": true,
			"msg": "你好golang",
		})
	})

	engine.GET("/json2", func(c *gin.Context) {
		
		a := Article{
			Title:   "标题",
			Desc:    "描述",
			Content: "内容",
		}
		c.JSON(http.StatusOK, a)
	})
```

返回结构体json的时候，如果结构体中某个字段不想返回可以加上标签`json:"-"`



### 返回jsonp

```go
engine.GET("/jsonp", func(c *gin.Context) {

		a := Article{
			Title:   "标题-jsonp",
			Desc:    "描述",
			Content: "内容",
		}
		c.JSONP(http.StatusOK, a)
	})
```

### 返回xml

```go
engine.GET("/xml", func(c *gin.Context) {
		c.XML(http.StatusOK, gin.H{
			"success": true,
			"msg": "返回xml",
		})
	})
```

### 渲染模板

```go
//创建一个默认的路由引擎
engine := gin.Default()
//配置模板文件
engine.LoadHTMLGlob("templates/*")
engine.GET("/news", func(c *gin.Context) {
	c.HTML(http.StatusOK, "news.html", gin.H{
		"title": "后台数据",
	})
})
```

# Gin HTML模板渲染

## 全部模板放在一个目录里面的配置方法

1. 在项目根目录新建templates文件夹（文件夹名字自定义），在文件夹中新建index.html（前端页面模板）

2. Gin框架中使用c.HTML()可以渲染模板，渲染模板前需要使用LoadHTMLGlob()或者LoadHTMLFiles()方法加载模板。

## 模板放在不同目录里面的配置方法

定义模板的时候需要通过define定义名称

```html
{{ define "admin/index.html"}} <!--相当于给模板定义一个名称，define与end成对出现-->
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>Title</title>
</head>
<body>

</body>
</html>
{{ end }}
```

```go
func main(){
    //创建一个默认的路由引擎
	engine := gin.Default()
	//配置模板文件
	engine.LoadHTMLGlob("templates/**/*")
    engine.GET("/admin", func(c *gin.Context) {
		c.HTML(http.StatusOK, "admin/index.html", gin.H{
			"title": "首页",
		})
	})
	engine.Run(":8000") //启动一个web服务
}
```



## gin模板基本语法

### 输出数据

### 变量

可以在模板中声明变量，用来保存传入模板的数据或其他语句生成的结果。语法如下：

```html
{{$obj := .title}}
<h4>
   	{{$obj}}
</h4>
```

### 移除空格

有时候在使用模板语法的时候会不可避免地引入一下空格或者换行符，这样模板最终渲染出来的内容就可能和我们想的不一样，这个时候可以使用`{{-`语法去除模板内容左侧的所有空白符号，使用`-}}`去除模板内容右侧的所有空白符号。

```html
{{- .Name -}}
```

注意：-要紧挨{{和}}，同时与模板值之间需要使用空格分隔。

### 比较函数

布尔函数会将任何类型的零值视为假，其余视为真。

下面是定义为函数的二元比较运算的集合：

eq		如果arg1 == arg2 则返回真

ne		如果arg1 != arg2 则返回真

lt		如果arg1 < arg2 则返回真

le		如果arg1 <= arg2 则返回真

gt		如果arg1 > arg2 则返回真

ge		如果arg1 >= arg2 则返回真

### 条件判断

```html
//举例
{{if gt .score 60}}
及格
{{else}}
不及格
{{end}}


{{if gt .score 90}}
<p>
    优秀
</p>
{{else if gt .score 60}}
<p>
    及格
</p>
{{else}}
<p>
    不及格
</p>
{{end}}
```

### range

Go的模板语法中使用range关键字进行遍历，有以下两种写法，其中pipeline的值必须是数组、切片、字典或者通道。

```html
{{range $key,$value := .obj}}
	{{value}}
{{end}}
```

如果pipeline的值其长度为0，不会有任何输出

```html
{{range $key,$value := .obj}}
	{{value}}
{{else}}
	pipeline的值其长度为0
{{end}}
```

### With

```html
<h4>姓名：{{.user.Name}}</h4>
<h4>性别：{{.user.Gender}}</h4>
<h4>年龄：{{.user.Age}}</h4>
```

```html
<!--解构结构体-->
{{with .user}}
	<h4>姓名：{{.Name}}</h4>
	<h4>性别：{{.Gender}}</h4>
	<h4>年龄：{{.Age}}</h4>
{{end}}
```



### 预定义函数（了解）

执行模板时，函数从两个函数字典中查找：首先是模板函数字典，然后是全局函数字典。一般不在模板内定义函数，而是使用Funcs方法添加函数到模板里。

预定义的全局函数如下：

and：函数返回它的第一个empty参数或者最后一个参数；就是说“and x y”等价于“if x then y else x”；所有参数都会执行；

or：返回第一个非empty参数或者最后一个参数；“or x y”等价于“if x then x else y”；所有参数都会执行；

not：返回它的单个参数的布尔值的否定

len：返回它的参数的整数类型长度

```html
{{len .title}}
```

index：执行结果为第一个参数以剩下的参数为索引/键指向的值；

### 自定义函数模板

通过engine.SetFuncMap()设定模板函数

```go
//时间戳转换成日期
func UnixToTime(timeStamp int) string {
	t := time.Unix(int64(timeStamp), 0)
	date := t.Format("2006-01-02 15:04:05")
	return date
}

func main() {
	//创建一个默认的路由引擎
	engine := gin.Default()
    //自定义模板函数 注意要把这个函数放在加载模板前
	engine.SetFuncMap(template.FuncMap{
		"UnixToTime": UnixToTime,
	})
    //配置模板文件，必须放在配置路由上面
	engine.LoadHTMLGlob("templates/**/*")
    ...
}
```

前端

```html
{{UnixToTime .date}}
```



### 嵌套template

1.新建template/public/page_header.html

```html
{{ define "public/page_header.html" }}
<h1>公共标题</h1>
{{end}}
```

2.外部引入

注意：

- 引入的名字为page_header.html中定义的名字
- 引入的时候注意最后的点（.）

```html
{{template "public/page_header.html" .}}
```

```html
{{ define "admin/index.html"}}
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>Title</title>
</head>
<body>
	{{template "public/page_header.html" .}}
</body>
</html>
{{ end }}
```

# 静态文件服务

当我们渲染的HTML文件中引用了静态文件时，我们需要配置静态web服务

r.Static("/static", "./static") 前面的/static表示网站访问路径，后面的./static表示本地文件路径（相对于项目根目录）

```go
func main() {
	//创建一个默认的路由引擎
	engine := gin.Default()
	//自定义模板函数 注意要把这个函数放在加载模板前
	engine.SetFuncMap(template.FuncMap{
		"UnixToTime": UnixToTime,
	})
	//配置模板文件，必须放在配置路由上面
	engine.LoadHTMLGlob("templates/**/*")
	//配置静态web目录 第一个参数表示路由，第二个参数表示映射的目录
	engine.Static("/static", "./static")//表示访问路径/static的时候会去templates中的static目录中寻找
 	...   
}
```

```html
<head>
    <link ref="stylesheet" href="static/css/base.css">
</head>
```

# 路由详解

## 获取请求参数

### Get请求查询参数 Query

Get /user?username=20&age=1

```go
engine.GET("/user", func(c *gin.Context) {
		username := c.Query("username")
		age := c.DefaultQuery("age", "0")
		fmt.Printf("username=%v, age=%v\n", username, age)
	})
```

### 表单PostForm

通过c.PostForm()或c.DefaultPostForm()获取表单中的数据

（获取请求体中的生json数据，使用c.GetRawData()，然后使用json包解析成map或结构体，再把数据取出来）

```go
engine.POST("/postUser", func(c *gin.Context) {
		//username := c.PostForm("username")
		//age := c.DefaultPostForm("age", "0")
		body, _ := c.GetRawData()
		var jsonMap map[string]interface{}

		json.Unmarshal(body, &jsonMap)

		c.JSON(http.StatusOK, gin.H{
			"username" : jsonMap["username"],
			"age": jsonMap["age"],
		})

	})
```



### 获取Get和Post传递的数据绑定到结构体

为了能够更方便地获取请求相关参数，提高开发效率，可以基于请求的Content-Type识别请求数据类型并利用反射机制自动提取请求中QueryString、form表单、JSON、XML等参数到结构体中。.ShouldBind()能够基于请求自动提取JSON、form表单和QueryString类型的数据，并把值绑定到指定的结构体对象。

```go
type UserInfo struct {
	Username string `json:"username" form:"username"`
	Password string `json:"password" form:"password"`
}
```

```go
engine.GET("/getUser", func(c *gin.Context) {
		user := UserInfo{}
		if err := c.ShouldBind(&user); err == nil {
			c.JSON(http.StatusOK, user)
			fmt.Printf("%#v\n", user)
		} else {
			c.JSON(http.StatusOK, gin.H{
				"err": err.Error(),
			})
		}
	})
```

```go
engine.POST("/postUser2", func(c *gin.Context) {
		user := UserInfo{}
		if err := c.ShouldBind(&user); err == nil {
			c.JSON(http.StatusOK, user)
		} else {
			c.JSON(http.StatusInternalServerError, gin.H{
				"err": err.Error(),
			})
		}
	})
```



### bind绑定参数

gin中的bind可以很方便地将前端传递来的数据与`结构体`进行`参数绑定`，以及参数校验。

- 参数绑定：在使用这个功能的时候，需要给结构体加上Tag `json`,`form`,`uri`,`xml`,`yaml`
- Must Bind：不用，校验失败会改状态码
- Should Bind：可以绑定json，query，param，yaml，xml，如果校验不通过会返回错误。会根据请求头中的content-type去自动绑定，form-data的参数也用这个，tag用form
- BindJSON、BindQuery等本质是调用MustBind，一般不用这种方式，因为会修改状态码



### 常用验证器

```
//不能为空，并且不能没有这个字段
required: 必填字段，如： binding:"required"

//针对字符串的长度
min 最小长度，如：binding:"min=5"
max 最大长度，如：binding:"max=10"
len 长度，如：binding:"len=6" //长度只能为4

//针对数字的大小
eq 等于，如：binding:"eq=3"
ne 不等于，如：binding:"ne=12"
gt 大于，如：binding:"gt=10"
gte 大于等于，如：binding:"gte=10"
lt 小于，如：binding:"lt=30,gt=18"
lte 小于等于，如：binding:"lte=10"

//针对同级字段
eqfield 等于其他字段的值，如：Password string `binding:"eqfield=ConfirmPassword"`
nefield 不等于其他字段的值
- 忽略字段，如：binding:"-"
```



### gin内置验证器

```go
//枚举 只能是red或green
oneof=red green //binding:"required,oneof=red green"

//字符串
contains=czh	//包含czh的字符串
excludes //不包含
startswith //字符串前缀
endswith   //字符串后缀

//数组
dive 	//dive后面的验证就是针对数组中的每一个元素

//网络验证
ip
ipv4
ipv6
uri
url
//uri在于Identifier是统一资源标示区，可以唯一标识一个资源
//url在于Locater，是统一资源定位符，提供找到该资源的确切路径

//日期验证
datetime=2006-01-02
```





### 自定义验证的错误信息

当验证不通过的时候，会给出错误信息，但是原始的错误信息不太友好，不利于用户查看。

需要给结构体加一个msg（msg名字自定义）的tag，再使用反射

```go
type SignUserInfo struct {
	Name string `json:"name" binding:"required" msg:"name不能为空"`				//用户名
	Age int `json:"age"`					//年龄
	Sex string `json:"sex" binding:"oneof=man woman"`
	Password string `json:"password"`		//密码
	RePassword string `json:"re_password" binding:"eqfield=Password"`	//确认密码
}

//获取结构体中的msg参数
func GetValidMsg(err error, obj any) string {
	getObj := reflect.TypeOf(obj)

	//将err断言为具体的类型
	if errors, ok := err.(validator.ValidationErrors); ok {
		for _, e := range errors {
			//根据报错字段名获取结构体的具体字段
			if f, exist := getObj.Elem().FieldByName(e.Field()); exist{
				msg := f.Tag.Get("msg")
				return msg
			}
		}
	}
	return ""
}

func main() {
	router := gin.Default()
	
	router.POST("/", func(c *gin.Context) {
		var user SignUserInfo
		err := c.ShouldBindJSON(&user)
		fmt.Printf("%v -- %T\n", err, err)
		if err != nil {
			c.JSON(http.StatusOK, gin.H{
				"msg": GetValidMsg(err, &user),
			})
			return
		}

		c.JSON(http.StatusOK, gin.H{
			"data": user,
		})
	})
	router.Run(":8002")
}
```

### 自定义验证器

注册验证器函数

```go
func signValid(fl validator.FieldLevel) bool {
	var nameList = []string{"张三", "李四", "王五"}
	for _, nameStr := range nameList {
		name := fl.Field().Interface().(string)
		if name == nameStr {
			return false
		}
	}
	return true
}

func main() {
	router := gin.Default()
	if v, ok := binding.Validator.Engine().(*validator.Validate); ok {
		v.RegisterValidation("sign", signValid)
	}
	
	router.POST("/", func(c *gin.Context) {
		var user SignUserInfo
		err := c.ShouldBindJSON(&user)
		fmt.Printf("%v -- %T\n", err, err)
		if err != nil {

			c.JSON(http.StatusOK, gin.H{
				"msg": GetValidMsg(err, &user),
			})
			return
		}

		c.JSON(http.StatusOK, gin.H{
			"data": user,
		})
	})
	router.Run(":8002")
}
```



### 获取Post、XML数据

在API开发中，经常会用到JSON或XML来作为数据交换格式，这时可以使用c.GetRawData()获取数据。

```xml
<userinfo>
    <username>czh</username>
    <password>123456</password>
</userinfo>
```

```go
engine.POST("/postUserXml", func(c *gin.Context) {
		xmlRawData, _ := c.GetRawData()
		userInfo := UserInfo{}
		xml.Unmarshal(xmlRawData, &userInfo)
		fmt.Printf("%#v\n", userInfo)
	})
```

### 动态路由传值

域名/user/20

```go
engine.GET("/userParam/:uid", func(c *gin.Context) {
		uid := c.Param("uid")
		fmt.Printf("%v\n", uid)
	})
```

## Gin路由分组、Gin路由文件抽离

路由分组：

```go
apiRouters := engine.Group("/api")
	{
		apiRouters.GET("/", func(c *gin.Context) {
			c.String(http.StatusOK, "我是一个api接口")
		})
		apiRouters.GET("/userList", func(c *gin.Context) {
			c.String(http.StatusOK, "我是一个api接口")
		})
		apiRouters.GET("/cart", func(c *gin.Context) {
			c.String(http.StatusOK, "我是一个api接口")
		})
	}
```

文件抽离

1. 新建一个routers目录
2. 在routers目录中分别为/api,/admin等路由新建一个go文件

3. 写一个参数为*gin.Engin的路由初始化方法
4. 在main.go中调用路由初始化方法

```go
func main() {
	//创建一个默认的路由引擎
	engine := gin.Default()
	//自定义模板函数 注意要把这个函数放在加载模板前
	engine.SetFuncMap(template.FuncMap{
		"UnixToTime": UnixToTime,
	})
	//配置模板文件，必须放在配置路由上面
	engine.LoadHTMLGlob("templates/**/**/*")
	//配置静态web目录 第一个参数表示路由，第二个参数表示映射的目录
	engine.Static("/static", "./static")

	routers.TestRoutersInit(engine)

	routers.ApiRoutersInit(engine)

	routers.AdminRoutersInit(engine)

	routers.DefaultRoutersInit(engine)

	engine.Run(":8000") //启动一个web服务
}
```

```go
package routers

import (
	"github.com/gin-gonic/gin"
	"net/http"
)

func ApiRoutersInit(engine *gin.Engine)  {
	apiRouters := engine.Group("/api")
	{
		apiRouters.GET("/", func(c *gin.Context) {
			c.String(http.StatusOK, "我是一个api接口")
		})
		apiRouters.GET("/userList", func(c *gin.Context) {
			c.String(http.StatusOK, "我是一个api接口")
		})
		apiRouters.GET("/cart", func(c *gin.Context) {
			c.String(http.StatusOK, "我是一个api接口")
		})
	}
}
```



# Gin自定义控制器及实现控制器的继承

（抽离处理路由的方法）

1. 新建controllers目录

2. 在controllers目录内为不同路由分别新建一个目录，如/admin,/api将对应处理路由的方法抽离到此目录下

控制器的继承

1. 新建对应的结构体，将为处理路由的方法添加接收者为此结构体即可。

```go
//userController.go
type UserController struct {}

func (u UserController) UserIndex(c *gin.Context) {
	c.HTML(http.StatusOK, "admin/index.html", gin.H{
		"title": "首页",
	})
}

func (u UserController) User(c *gin.Context) {
	c.String(http.StatusOK, "用户列表")
}

func (u UserController) UserAdd(c *gin.Context) {
	c.String(http.StatusOK, "用户列表")
}

func (u UserController) UserEdit(c *gin.Context) {
	c.String(http.StatusOK, "用户列表")
}

func (u UserController) Article(c *gin.Context) {
	c.String(http.StatusOK, "文章")
}


//adminRouters.go
func AdminRoutersInit(engine *gin.Engine)  {
	adminRouters := engine.Group("/admin")
	{
		adminRouters.GET("/", admin.UserController{}.UserIndex)

		adminRouters.GET("/user", admin.UserController{}.User)

		adminRouters.GET("/user/add", admin.UserController{}.UserAdd)

		adminRouters.GET("/user/edit", admin.UserController{}.UserEdit)

		adminRouters.GET("/article", admin.UserController{}.Article)
	}
}
```

# 请求头相关

## 请求头参数获取

```go
router.GET("/", func(c *gin.Context){
    //字母不区分大小写，单词与单词之间用 - 连接
    c.GetHeader("User-Agent")//源码中调用c.Request.Header.Get()
    //字母不区分大小写
    c.Request.Header.Get("User-Agent")
    
    c.Request.Header["User-Agent"]//区分大小写
})
```



# 响应头相关

## 设置响应头

```go
router.GET("/", func(c *gin.Context){
    c.Header("Token", "123456")
    c.Header("Content-Type", "application/text; charset=utf-8")
})
```



# Gin文件上传

## 单文件上传

[单文件 | Gin Web Framework (gin-gonic.com)](https://gin-gonic.com/zh-cn/docs/examples/upload-file/single-file/)

form标签中需要添加`enctype="multipart/form-data"`

```go
func main() {
	router := gin.Default()
	// 为 multipart forms 设置较低的内存限制 (默认是 32 MiB)
	router.MaxMultipartMemory = 8 << 20  // 8 MiB
	router.POST("/upload", func(c *gin.Context) {
		// 单文件
		file, _ := c.FormFile("file")
		log.Println(file.Filename)

		dst := "./" + file.Filename
		// 上传文件至指定的完整文件路径
		c.SaveUploadedFile(file, dst)

		c.String(http.StatusOK, fmt.Sprintf("'%s' uploaded!", file.Filename))
	})
	router.Run(":8080")
}
```



## 多文件上传

```go
func main() {
	router := gin.Default()
	// 为 multipart forms 设置较低的内存限制 (默认是 32 MiB)
	router.MaxMultipartMemory = 8 << 20  // 8 MiB
	router.POST("/upload", func(c *gin.Context) {
		// Multipart form
		form, _ := c.MultipartForm()
		files := form.File["upload[]"]//前端<input type="file" name="upload[]">，前端有多个name为“upload[]”的input标签

		for _, file := range files {
			log.Println(file.Filename)

			// 上传文件至指定目录
			c.SaveUploadedFile(file, dst)
		}
		c.String(http.StatusOK, fmt.Sprintf("%d files uploaded!", len(files)))
	})
	router.Run(":8080")
}
```



## 按日期存储图片

```go
func (u UserController) DoUpload(c *gin.Context){
	//username := c.PostForm("username")
	//1.获取上传的文件
	file, err := c.FormFile("face")
	if err == nil {
		//2.获取后缀名 判断类型是否正确
		extName := path.Ext(file.Filename)
		allowExtName := map[string]bool{
			".jpg": true,
			".png": true,
			".gif": true,
			".jpeg": true,
		}

		if _, ok := allowExtName[extName]; !ok {
			c.String(http.StatusOK, "上传的文件类型不合法")
			return
		}
		//3.创建图片保存目录 static/upload/20230119
		day := models.GetDay()

		dir := "./static/upload/" + day

		//4.生成目录
		err := os.Mkdir(dir, 0755)
		if err != nil{
			c.String(http.StatusOK, "生成目录失败")
			return
		}

		unix := models.GetUnix()
		filename := strconv.FormatInt(unix,10) + extName

		//5.上传
		dst := path.Join(dir, filename)
		c.SaveUploadedFile(file, dst)
	}
}
```



# Gin文件下载

**直接响应一个路径下的文件**

```go
c.File("uploads/1.jpeg")
```

有些响应，比如图片，浏览器就会直接显示这个图片，而不是下载，所以我们需要使浏览器唤起下载行为。

```go
c.Header("Content-Type", "application/octet-stream") //表示是文件读，唤起浏览器下载，一般设置了这个就要设置文件名
c.Header("Content-Disposition","attachment; filename=" + "1.jpeg") //用于指定下载下来的文件名
c.Header("Content-Transfer-Encoding", "binary") //表示传输过程中的编码形式，乱码问题可能就是因为它
c.File("uploads/1.jpeg")
```

文件下载浏览器可能会有缓存，解决办法是加查询参数



**前后端模式下的文件下载**

如果是前后端模式下，后端只需要响应一个文件数据

文件名和其他信息就写在请求头中

```go
c.Header("filename","xxx.png")
c.File("uploads/1.png")
```



# Gin中间件

Gin框架允许开发者在处理请求的过程中，加入用户自己的钩子（Hook）函数。这个钩子函数就叫中间件，中间件适合处理一些公共的业务逻辑，比如登录认证、权限校验、数据分页、记录日志、耗时统计等。

通俗地讲：中间件就是匹配路由前和匹配路由完成后执行的一系列操作。

## 路由中间件

Gin中的中间件必须是一个gin.HandlerFunc类型，配置路由的时候可以传递多个func回调函数，最后一个func回调函数前面触发的方法都可以称为中间件。

```go
adminRouters.GET("/", func(c *gin.Context) {
			fmt.Println("我是一个中间件")
		}, admin.IndexController{}.Index)
```

GET方法的第二个参数是可变参数，参数类型是参数为`*gin.Context`类型的函数，最后一个函数（路由处理函数）执行之前会执行前面的函数，这些写在前面的函数就称为路由中间件，如：

func(ctx *gin.Context) {
			fmt.Println("我是一个中间件")
		}



## c.Next()调用该请求的剩余处理程序

中间件里面加上ctx.Next()可以在路由匹配完成后执行一些操作。

```go
func InitMiddleware(ctx *gin.Context) {
	fmt.Println("中间件1")
	start := time.Now().Unix()
	ctx.Next()
	end := time.Now().Unix()
	fmt.Printf("执行时间：%vs\n", end - start)
	fmt.Println("中间件2")
}

func AdminRoutersInit(engine *gin.Engine)  {
	adminRouters := engine.Group("/admin")
	{
		adminRouters.GET("/user", InitMiddleware, admin.UserController{}.User)
}
```



## c.Abort()

c.Abort表示终止调用该请求的剩余处理程序。



## 一个路由配置多个中间件的执行顺序





## 配置全局中间件

使用engine.Use()

```go
func initMiddlewareOne(ctx *gin.Context) {
	fmt.Println("全局-1-中间件-initMiddlewareOne")
	ctx.Next()
	fmt.Println("全局-2-中间件-initMiddlewareOne")
}

func initMiddlewareTwo(ctx *gin.Context)  {
	fmt.Println("全局-1-中间件-initMiddlewareTwo")
	ctx.Next()
	fmt.Println("全局-2-中间件-initMiddlewareTwo")
}

func main() {
	//创建一个默认的路由引擎
	engine := gin.Default()
	//自定义模板函数 注意要把这个函数放在加载模板前
	engine.SetFuncMap(template.FuncMap{
		"UnixToTime": UnixToTime,
	})
	//配置模板文件，必须放在配置路由上面
	engine.LoadHTMLGlob("templates/**/**/*")
	//配置静态web目录 第一个参数表示路由，第二个参数表示映射的目录
	engine.Static("/static", "./static")

	//全局中间件
	engine.Use(initMiddlewareOne, initMiddlewareTwo)

	routers.AdminRoutersInit(engine)

	engine.Run(":8000") //启动一个web服务
}
```

## 路由分组配置中间件

第一种：作为第二个参数写在Group方法中

```go
func AdminRoutersInit(engine *gin.Engine)  {
	adminRouters := engine.Group("/admin", middlewares.InitMiddlewareForGroup)//路由分组配置中间件

	{
		adminRouters.GET("/", admin.IndexController{}.Index)

		adminRouters.GET("/user", initMiddlewareOne, initMiddlewareTwo, admin.UserController{}.User)

		adminRouters.GET("/user/add", admin.UserController{}.UserAdd)

		adminRouters.GET("/user/edit", admin.UserController{}.UserEdit)

		adminRouters.GET("/article", admin.ArticleController{}.Article)
	}
}
```

第二种：使用Use()方法

```go
func AdminRoutersInit(engine *gin.Engine)  {
	adminRouters := engine.Group("/admin")
	adminRouters.Use(middlewares.InitMiddlewareForGroup)//路由分组配置中间件
	{
		adminRouters.GET("/", admin.IndexController{}.Index)

		adminRouters.GET("/user", initMiddlewareOne, initMiddlewareTwo, admin.UserController{}.User)

		adminRouters.GET("/user/add", admin.UserController{}.UserAdd)

		adminRouters.GET("/user/edit", admin.UserController{}.UserEdit)

		adminRouters.GET("/article", admin.ArticleController{}.Article)
	}
}
```

## 中间件和对应控制器之间共享数据

可以在中间件中使用c.Set("key", value)方法设置数据，在路由处理函数（handler）中使用c.Get("key")获取数据

## gin.New()

使用gin.Default()创建的默认引擎有Logger()和Recovery()两个中间件，其中：

- Logger中间件将日志写入gin.DefaultWriter，即配置了GIN_MODE=release。
- Recovery中间件会recover任何panic。如果有panic的话，会写入500响应码。

如果不想要默认的两个中间件，可以使用gin.New()创建引擎。（推荐使用gin.Default()）

```go
func Default() *Engine {
	debugPrintWARNINGDefault()
	engine := New()
	engine.Use(Logger(), Recovery())
	return engine
}
```

## gin中间件中使用goroutine

当在中间件或handler中启动新的gouroutine时，不能使用原始的上下文(c *gin.Context)，必须使用其只读副本（c.Copy()）

```go
func InitMiddlewareForGroup(c *gin.Context)  {
	fmt.Println(time.Now())
	fmt.Println(c.Request.URL)
	c.Set("name", "czh")

	cCp := c.Copy()

	go func() {
		fmt.Println("path:", cCp.Request.URL)
	}()
}
```



# Gin内置日志组件

## 输出日志到log文件

```go
func main() {

	file, _ := os.Create("gin.log")
	//gin.DefaultWriter = io.MultiWriter(file)	//只输出到文件
	gin.DefaultWriter = io.MultiWriter(file, os.Stdout)	//输出到文件和控制台

	router := gin.Default()
    router.POST("/", func(c *gin.Context){})
    
    router.Run()
```

## 定义路由格式

启动gin，它会显示所有的路由，默认格式如下

```
[GIN-debug] POST   /                         --> main.main.func1 (3 handlers)
[GIN-debug] POST   /upload                   --> main.main.func2 (3 handlers)
[GIN-debug] GET    /download                 --> main.main.func3 (3 handlers)
```

```go
gin.DebugPrintRouteFunc = func(httpMethod, absolutePath, handlerName string, nuHandlers int){
		log.Printf(
			"[czh] %v %v %v %v\n",
			httpMethod,
			absolutePath,
			handlerName,
			nuHandlers,
			)
	}
```

用处不大

## 查看路由

```go
router.Routes() //返回已注册的路由列表
```

```go
for _,info := range router.Routes(){
    fmt.Println(info.Path, info.method, info.Handler)
}
```



## 环境切换

```go
gin.SetMode(gin.ReleaseMode)
```



## 修改log的显示

默认显示如下：

```go
[GIN] 2023/02/05 - 14:21:36 | 200 |       375.5µs |             ::1 | POST     "/"
```

修改

```go
router := gin.New()
	router.Use(gin.LoggerWithFormatter(func(params gin.LogFormatterParams) string {
		return fmt.Sprintf("[CZH] %s |%d| %s %s\n",
			params.TimeStamp.Format("2006-01-02 15:04:05"),
			params.StatusCode,
			params.MethodColor() + params.Method + params.ResetColor(),
			params.Path,
			)
	}), gin.Recovery())
```





# Gin中的Cookie

## Cookie介绍

- Http是无状态协议。简单地说，当浏览了一个页面，然后转到同一个网站的另一个页面，服务器无法认识到这是同一个浏览器在访问同一个网站。每一次访问，都是没有任何关系的，如果要实现多个页面之间共享数据的话我们就可以使用cookie或者session实现。
- cookie是存储与访问者计算机的浏览器中，可以让我们用同一个浏览器访问同一个域名的时候共享数据。

## Cookie能实现的功能

1. 保持用户登录状态
2. 保存用户浏览的历史记录
3. 电商网站的加入购物车

## 设置和获取Cookie

[设置和获取 Cookie | Gin Web Framework (gin-gonic.com)](https://gin-gonic.com/zh-cn/docs/examples/cookie/)

### 设置Cookie

c.SetCookie(name, value string, maxAge int, path, domain string, secure, httpOnly bool)

- 第一个参数：key
- 第二个参数：value
- 第三个参数：过期时间，如果只想设置Cookie的保存路径而不想设置存活时间，可以在第三个参数中传递nil
- 第四个参数：cookie的路径
- 第五个参数：cookie的路径Domain作用域，本地调试配置成localhost，正式上线配置成域名
- 第六个参数：当secure为true时，cookie在http中是无效的，在https中才有效。
- 第七个参数：httpOnly是微软对Cookie做的扩展。如果在cookie中设置了“httpOnly”属性，则通过程序（JS脚本、applet等）将无法读取到Cookie信息，防止XSS攻击。

```go
func main() {

    router := gin.Default()

    router.GET("/cookie", func(c *gin.Context) {

        cookie, err := c.Cookie("gin_cookie")

        if err != nil {
            cookie = "NotSet"
            c.SetCookie("gin_cookie", "test", 3600, "/", "localhost", false, true)
        }

        fmt.Printf("Cookie value: %s \n", cookie)
    })

    router.Run()
}
```

### 获取Cookie

cookie, err := c.Cookie("name")

### 多个二级域名共享cookie

用户在a.xxx.com中设置cookie信息后在b.xxx.com中获取刚才设置的cookie，实现多个二级域名共享cookie。

  ```go
  c.SetCookie("gin_cookie", "test", 3600, "/", ".xxx.com", false, true)//把域名配置成.xxx.com
  ```

# Gin中的Session

## Session简单介绍

session是另一种记录客户状态的机制，不同的是cookie保存在客户端浏览器中，而session保存在服务器上。

## Session的工作流程

当客户端浏览器第一次访问服务器并发送请求时，服务器端会创建一个session对象，生成一个类似key，value的键值对，然后将value保存到服务器，将key（cookie）返回到浏览器端。浏览器下次访问时会携带key（cookie），找到对应的session（value）。

## Gin中使用session

Gin官方没有提供Session相关文档，可以使用第三方的Session中间件来实现。

https://github.com/gin-contrib/sessions

gin-contrib/sessions中间件支持的存储引擎：

- cookie
- memstore
- redis
- memcached
- mongodb

基于cookie

 ```go
 import (
   "github.com/gin-contrib/sessions"
   "github.com/gin-contrib/sessions/cookie"
   "github.com/gin-gonic/gin"
 )
 
 func main() {
   r := gin.Default()
     //创建基于cookie的存储引擎，secret参数是用于加密的密钥
   store := cookie.NewStore([]byte("secret"))
     //配置session的中间件，store是创建的存储引擎
   r.Use(sessions.Sessions("mysession", store))
 
   r.GET("/incr", func(c *gin.Context) {
       //设置session
     session := sessions.Default(c)
     var count int
     v := session.Get("count")
     if v == nil {
       count = 0
     } else {
       count = v.(int)
       count++
     }
     session.Set("count", count)
     session.Save()
     c.JSON(200, gin.H{"count": count})
   })
   r.Run(":8000")
 }
 ```

基于redis（session存储在redis）

  ```go
  func main() {
    r := gin.Default()
    store, _ := redis.NewStore(10, "tcp", "localhost:6379", "", []byte("secret"))
    r.Use(sessions.Sessions("mysession", store))
  
    r.GET("/incr", func(c *gin.Context) {
      session := sessions.Default(c)
      var count int
      v := session.Get("count")
      if v == nil {
        count = 0
      } else {
        count = v.(int)
        count++
      }
      session.Set("count", count)
        //保存session
      session.Save()
      c.JSON(200, gin.H{"count": count})
    })
    r.Run(":8000")
  }
  ```

# logrus日志框架

## 下载

```bash
go get github.com/sirupsen/logrus
```

## logrus常用方法

```go
func main()  {
	//设置日志级别
	logrus.SetLevel(logrus.DebugLevel)
	logrus.Error("出错了")
	logrus.Warning("警告")
	logrus.Info("信息")
	logrus.Debug("debug")
	logrus.Println("打印")

	//日志等级
	fmt.Println(logrus.GetLevel())
}
```

输出如下：

```
debug
time="2023-02-05T21:12:45+08:00" level=error msg="出错了"
time="2023-02-05T21:12:45+08:00" level=warning msg="警告"
time="2023-02-05T21:12:45+08:00" level=info msg="信息"
time="2023-02-05T21:12:45+08:00" level=debug msg=debug
time="2023-02-05T21:12:45+08:00" level=info msg="打印"
```

日志等级

```go
const (
	// PanicLevel level, highest level of severity. Logs and then calls panic with the
	// message passed to Debug, Info, ...
	PanicLevel Level = iota // 会抛一个异常
	// FatalLevel level. Logs and then calls `logger.Exit(1)`. It will exit even if the
	// logging level is set to Panic.
	FatalLevel	//打印日志后就会退出
	// ErrorLevel level. Logs. Used for errors that should definitely be noted.
	// Commonly used for hooks to send errors to an error tracking service.
	ErrorLevel
	// WarnLevel level. Non-critical entries that deserve eyes.
	WarnLevel
	// InfoLevel level. General operational entries about what's going on inside the
	// application.
	InfoLevel
	// DebugLevel level. Usually only enabled when debugging. Very verbose logging.
	DebugLevel
	// TraceLevel level. Designates finer-grained informational events than the Debug.
	TraceLevel	//低级别
)
```

## 更改日志级别

```go
logrus.SetLevel(logrus.DebugLevel)
```



## 设置特定字段

```go
log := logrus.WithField("app", "study").WithField("service", "logrus")
log = logrus.WithFields(logrus.Fields{
	"user": "czh",
})

log.Errorf("你好")
```



## 显示样式Text和Json

默认的是以text的形式展示，也可以设置为json

```go
logrus.SetFormatter(&logrus.JSONFormatter{})
log := logrus.WithField("app", "study").WithField("service", "logrus")
log.Errorf("你好")
//{"app":"study","level":"error","msg":"你好","service":"logrus","time":"2023-02-05T21:32:22+08:00"}
```

## 自定义颜色

如果我们想要在控制中显示颜色，一般的做法都是使用ANSI控制码，用于设置文本颜色。\033是控制码的开始，是八进制数字，[31m表示将文明设置为红色。ANSI控制码是用于在终端和控制台中控制文本格式和颜色的一种标准。它们通常用于在命令行界面（CLI）程序中输出彩色文本或者在文本模式下的图形界面（GUI）中输出文本。

```go
func main(){
    //前景色
	fmt.Println("\033[30m 黑色 \033[0m")
	fmt.Println("\033[31m 红色 \033[0m")
	fmt.Println("\033[32m 绿色 \033[0m")
	fmt.Println("\033[33m 黄色 \033[0m")
	fmt.Println("\033[34m 蓝色 \033[0m")
	fmt.Println("\033[35m 紫色 \033[0m")
	fmt.Println("\033[36m 青色 \033[0m")
	fmt.Println("\033[37m 灰色 \033[0m")

	//背景色
	fmt.Println("\033[40m 黑色 \033[0m")
	fmt.Println("\033[41m 红色 \033[0m")
	fmt.Println("\033[42m 绿色 \033[0m")
	fmt.Println("\033[43m 黄色 \033[0m")
	fmt.Println("\033[44m 蓝色 \033[0m")
	fmt.Println("\033[45m 紫色 \033[0m")
	fmt.Println("\033[46m 青色 \033[0m")
	fmt.Println("\033[47m 灰色 \033[0m")
}
```

logrus支持颜色输出，在配置中去进行开启

```go
logrus.SetFormatter(&logrus.TextFormatter{
    ForceColors: true
})
```

还有一些其他配置

```go
ForceColors:是否强制使用颜色输出
DisableColors:是否禁用颜色输出
ForceQuote:是否强制引用所有值
DisableQuote:是否禁用引用所有值
DisableTimestamp:是否禁用时间戳记录
FullTimestamp:是否在连接到TTY时输出完成的时间戳
TimestampFormat:用于输出完整时间戳的时间戳格式
```







# Gin中使用GORM操作mysql数据库

gorm官方支持的数据库类型有：Mysql，PostgreSQL，SQlite，SQL Server

[GORM 指南 | GORM - The fantastic ORM library for Golang, aims to be developer friendly.](https://gorm.io/zh_CN/docs/index.html)

在实际项目中定义数据库模型注意以下几点：

1.结构体的名称必须首字母大写，并和数据库表名称对应。例如：表名称为user的结构体名称定义成User，表名称为article_cate结构体名称定义成ArticleCate

2.结构体中的字段名称首字母必须大写，并和数据库表中的字段一一对应。例如结构体中的Id和数据库中的id对应，Username和数据库中的username对应，Age和数据库中的age对应，Email和数据库中的email对应，AddTime和数据库中的add_time字段对应。

3.默认情况表名是结构体名称的复数形式。如果我们的结构体名称定义成User，表示这个模型默认操作的是users表。

4.可以使用结构体中的自定义方法TableName改变结构体的默认表名称，如下：

```go
func (User) TableName() string {
    return "user"
}
```

## 创建



## 查询

[查询 | GORM - The fantastic ORM library for Golang, aims to be developer friendly.](https://gorm.io/zh_CN/docs/query.html)

## 更新

[更新 | GORM - The fantastic ORM library for Golang, aims to be developer friendly.](https://gorm.io/zh_CN/docs/update.html)

## 删除





# 微服务架构和微服务

微服务架构：微服务架构是一种具体的设计实现或者设计方案，是将复杂的系统使用组件化的方式进行拆分，并使用轻量级通讯方式进行整合的一种设计方法。

微服务：微服务是微服务架构具体的实现方案，是通过微服务架构设计方法拆分出来的一个独立的组件化的小应用。



## RPC调用

服务端

```go
//定义一个远程调用的方法
type Hello struct {

}

/**
1.方法只能有两个可序列化的参数，其中第二个参数是指针类型
req 表示获取客户端传过来的数据
res 表示客户端返回数据
2.方法要返回一个error类型，同时必须是公开的方法

Go中的类型比如：channel（通道）、complex（复数类型）、func（函数）均不能进行序列化，因此req和res不能是上述类型
 */
func (Hello) SayHello(req string, res *string) error {
	fmt.Println(req)
	*res = "你好" + req
	return nil
}

func main()  {
	fmt.Println("你好 server")
	//1.注册rpc服务
	err1 := rpc.RegisterName("hello", new(Hello))
	if err1 != nil {
		fmt.Println(err1)
	}

	//2.监听端口
	listener, err2 := net.Listen("tcp", "127.0.0.1:8001")
	if err2 != nil {
		fmt.Println(err2)
	}

	//3.应用退出的时候关闭监听端口
	defer listener.Close()

	for {
		fmt.Println("开始建立连接")
		//4.建立连接
		conn, err3 := listener.Accept()

		if err3 != nil {
			fmt.Println(err3)
		}

		//5.绑定服务
		rpc.ServeConn(conn)
	}
}
```

客户端

```go
func main() {
	fmt.Println("client")
	//1.用rpc连接服务器 --Dial()
	conn, err1 := rpc.Dial("tcp", "127.0.0.1:8001")
	if err1 != nil {
		fmt.Println(err1)
	}
	//2.当客户端退出的时候关闭连接
	defer conn.Close()

	//3.调用远程函数
	var reply string
	err2 := conn.Call("hello.SayHello", "我是客户端", &reply)
	if err2 != nil {
		fmt.Println(err2)
	}
	//4.获取微服务返回的数据
	fmt.Println(reply)
}
```

## net/rpc/jsonrpc库以及RPC跨语言

标准库的RPC默认采用Go语言特有的gob编码，没法实现跨语言调用。golang官方还提供了`net/rpc/jsonrpc`库实现RPC方法，JSON RPC采用JSON进行数据编解码，因此支持跨语言调用。但目前的jsonrpc库是基于tcp协议实现的，暂不支持使用http进行数据传输。

## Linux命令之nc创建tcp服务测试数据传输

nc是netcat的简写，是一个功能强大的网络工具，有着网络界的瑞士军刀美誉

nc命令的主要作用如下：

- 实现任意TCP/UDP端口的监听，nc可以作为server以TCP或UDP方式监听指定端口
- 端口的扫描，nc可以作为client发起TCP或UDP连接
- 机器之间传输文件
- 机器之间网络测速

centos中如果找不到nc命令可以使用`yum install -y nc`安装

使用nc作为微服务server端接收客户端数据

```go
nc -l 192.168.80.135 8001
```



使用`net/rpc/jsonrpc`库中的rpc.ServeCodec函数替代rpc.ServeConn函数，传入的参数是针对服务端的json编码器

```go
//5.绑定服务
//rpc.ServeConn(conn)

//服务端
rpc.ServeCodec(jsonrpc.NewServerCodec(conn))
```

```go
//客户端
func main() {
	fmt.Println("client")
	//1.用rpc连接服务器 --Dial()
	//conn, err1 := rpc.Dial("tcp", "192.168.80.135:8001")
    
    //1.用net.Dial和rpc微服务建立连接
	conn, err1 := net.Dial("tcp", "192.168.80.135:8001")
	if err1 != nil {
		fmt.Println(err1)
	}
	//2.当客户端退出的时候关闭连接
	defer conn.Close()

	client := rpc.NewClientWithCodec(jsonrpc.NewClientCodec(conn))

	//3.调用远程函数
	var reply string
	//err2 := conn.Call("hello.SayHello", "我是客户端", &reply)
	err2 := client.Call("hello.SayHello", "我是客户端", &reply)
	if err2 != nil {
		fmt.Println(err2)
	}
	//4.获取微服务返回的数据
	fmt.Println(reply)
}
```



## ProtoBuf认识与使用

### protobuf简介

Protobuf是Protocol Buffers的简称，它是Google公司开发的一种数据描述语言，是一种轻便高效的结构化数据存储格式，可以用于结构化数据串行化，或者说序列化。它很适合做**数据存储**或**RPC数据交换格式**。可用于通讯协议、数据存储等领域的语言无关、平台无关、可扩展的序列化数据格式。是一种灵活，高效，自动化的机制，用于序列化结构化数据，对比于XML和JSON，它更小、更快、更简单。

Protobuf刚开源时的定位类似于XML、JSON等数据描述语言，通过附带工具生成代码并实现将结构化数据序列化的功能。这里我们更关注的是Protobuf作为接口规范的描述语言，可以作为设计安全的跨语言RPC接口的基础工具。

protobuf的优势和劣势：

优势：

1. 序列化后体积相比json和xml很小，适合网络传输
2. 支持跨平台多语言
3. 消息格式升级和兼容性很好
4. 序列化和反序列化速度很快，快于json的处理速度

劣势：

1. 应用不够广（相比xml和json）
1. 二进制格式导致可读性差
1. 缺乏自描述



### protobuf安装

[Releases · protocolbuffers/protobuf · GitHub](https://github.com/protocolbuffers/protobuf/releases)

windows系统选择win64.zip，解压后将bin目录放入系统环境变量Path

查看版本： `protoc --version`

protobuf的go语言插件protoc-gen-go插件

`go install github.com/golang/protobuf/protoc-gen-go@latest`



### protobuf简单语法

https://developers.google.com/protocol-buffers/docs/proto3

```protobuf
syntax = "proto3";		//指定版本信息，不指定会报错，默认是proto2
option_go_package = "./proto;helloworld";	//分号前面的表明go文件生成到哪个目录（相对当前目录），分号后面表示生成go文件的包名（可以省略，省略后包名与分号前面所写的目录名相同）
//message定义一种消息类型，关键字message定义结构，并且结构中可以嵌套定义结构，message定义的内容和生成一个结构体
message Person{
	//名字
	string name = 1;//1为编号，表示处于第1位，不能重复
	//年龄
	int32 age = 2;
	//爱好
	repeated string hobby = 3;	//数组 golang中会生成string类型的切片
}
```



### protobuf高级用法

#### message嵌套

message除了能放简单数据类型外，还能存放另外的message类型：

```protobuf
message Order{
  int64 id = 1;
  double price = 2;
  string name = 3;
  string tel = 4;
  string address = 5;
  string addTime = 6;
  //message可以嵌套
//  message OrderItem{
//    int64 goodsId = 1;
//    string title = 2;
//    double price = 3;
//    int32 num = 4;
//  }
  OrderItem orderItem = 7;
}

message OrderItem{
  int64 goodsId = 1;
  string title = 2;
  double price = 3;
  int32 num = 4;
}
```



#### repeated关键字

repeated关键字在go语言中相当于切片



#### 默认值

解析数据时，如果编码的消息不包含特定的单数元素，则解析对象中的相应字段将设置为该字段的默认值。不同类型的默认值不同，如下：

- 对于字符串，默认值为空字符串
- 对于字节，默认值为空字节
- 对于bools，默认值为false
- 对于数字类型，默认值为0
- 对于枚举，默认值是第一个定义的枚举值，该值必须为0
- repeated字段默认值是空列表
- message字段的默认值为空对象



#### enum关键字

```protobuf
enum PhoneType {
  MOBILE = 0;
  HOME = 1;
  WORK = 2;
}
```



#### 定义RPC服务

如果需要将message与RPC一起使用，则可以在.proto文件中定义RPC服务接口，protobuf编译器将根据选择的语言生成RPC代码接口，示例如下：

```protobuf
//定义service服务
service goodsService {
  rpc AddGoods(AddGoodsReq) returns (AddGoodsRes);
}

message AddGoodsReq{
  string title = 1;
  double price = 2;
  string content = 3;
}

message AddGoodsRes{
  string message = 1;
  bool success = 2;
}
```

编译命令：`protoc --go_out=plugins=grpc:./ */proto`



### protobuf基本编译

protobuf编译是通过编译器protoc进行的，通过这个编译器，我们可以把.proto文件生成go、Java、Python、C++、Ruby、JavaNano、Objective-C或者C#代码，生成命令如下：

```bash
protoc --proto_path=IMPORT_PATH --go_out=DST path/to/file.proto
```

1. --proto_path=IMPORT_PATH,IMPORT_PATH是.proto文件所在的路径，如果忽略则默认当前目录。如果有多个目录则可以多次调用--proto_path，它们将会顺序得被访问并执行导入。
2. --go_out=DST_DIR，指定了生成go语言代码文件放入的文件夹。
3. 允许使用`protoc --go_out=./ *.proto`的方式一次性编译多个.proto文件。
4. go语言编译时，protobuf编译器会把.proto文件编译成.pd.go文件





### protobuf序列化反序列化

序列化：`protobuf.Marshal()`

反序列化：`protobuf.Unmarshal()`

```go
func main() {
	userinfo := userService.Userinfo{
		Username: "czh",
		Age:      23,
		Hobby:    []string{"唱", "跳", "rap", "篮球"},
	}
	fmt.Printf("%+v\n", userinfo)

	bytes, _ := proto.Marshal(&userinfo)
	fmt.Println(bytes)

	user := userService.Userinfo{}
	proto.Unmarshal(bytes, &user)
	fmt.Printf("%+v\n", user)
}
```



## GRPC框架

GRPC框架是Google公司基于Protobuf开发的跨语言的开源RPC框架。它是一个高性能、开源和通用的RPC框架，基于HTTP/2协议设计，带来诸如双向流、流控、头部压缩、单TCP连接上的多复用请求等待，对于移动设备更加友好，更省电和节省空间占用。目前提供C、Java和Go语言版本，分别是：grpc，grpc-java，grpc-go。其中C版本支持C++，Node.js，Python，Ruby，Objective-C，PHP和C#支持。

GRPC特点：

1. 提供几乎所有主流语言的实现，打破语言隔阂。
2. 基于HTTP/2标准设计，带来诸如双向流、流控、头部压缩、单TCP连接上的多路复用请求等待。
3. 默认使用Protocol Buffers序列化，性能相较于RESTFUL json好很多
4. 工具链成熟，代码生成便捷，开箱即用。
5. 支持双向流式的请求和响应，对批量处理、低延时场景友好。

这些特性使得其在移动设备上表现更好，更省电和节省空间占用。

有了grpc，我们可以一次性地在一个.proto文件中定义服务并使用任何支持它的语法去实现客户端和服务器，grpc默认使用protocol buffers，它是google开源的一套成熟的结构数据序列化机制（也可以使用其他数据格式如json），可以用proto files创建grpc服务，用protocol buffers消息类型来定义方法参数和返回类型。

在grpc客户端可以直接调用不同服务器上的远程程序，就像调用本地程序一样，很容易构建分布式应用和服务。和很多rpc系统一样，服务负责实现定义好的接口并处理客户端请求，客户端根据接口描述直接调用需要的服务。客户端和服务端可以分别使用grpc支持不同语言实现。



proto代码

```protobuf
syntax = "proto3";
option go_package = "./greeter";

service Greeter{
  rpc SayHello(HelloReq) returns (HelloRes);
}

message HelloReq{
  string name = 1;
}

message HelloRes{
  string message = 1;
}
```

通过`protoc --go_out=plugins=grpc:./ */proto`生成go代码

服务端代码

```go
//1.定义远程调用的结构体和方法，这个结构体需要实现GreaterServer的接口
type Hello struct {}

func (Hello) SayHello(c context.Context, req *greeter.HelloReq) (*greeter.HelloRes, error)  {
	return &greeter.HelloRes{
		Message: "你好" + req.GetName(),
	}, nil
}

func main()  {
	//1.初始化grpc对象
	grpcServer := grpc.NewServer()
	//2.注册服务
	greeter.RegisterGreeterServer(grpcServer, &Hello{})
	//3.设置监听
	listener, err := net.Listen("tcp", "127.0.0.1:8001")
	if err != nil {
		fmt.Println(err)
	}
	//4.退出监听
	defer listener.Close()
	//5.启动服务
	grpcServer.Serve(listener)
}
```

客户端代码

```go
func main()  {
	//1.连接服务端
	/**
	credential.NewClientTLSFromFile:从输入的证书文件中为客户端构造TLS凭证。
	grpc.WithTransportCredentials:配置连接级别的安全凭证（例如，TLS/SSL），返回一个DialOption，用于连接服务器
	*/
	grpcClient, err := grpc.Dial("127.0.0.1:8001", grpc.WithTransportCredentials(insecure.NewCredentials()))
	if err != nil {
		fmt.Println(err)
	}
	//2.注册客户端
	client := greeter.NewGreeterClient(grpcClient)
    //3.调用服务
	res, err := client.SayHello(context.Background(), &greeter.HelloReq{Name: "czh"})
	fmt.Printf("%#v %T\n", res, res)
	fmt.Println(res.Message)
}
```



## go-micro框架

Go Micro是一个简化分布式开发的微服务生态系统，该系统为开发分布式应用程序提供了高效，便捷的模块构建，主要目的是简化分布式系统的开发。它默认实现了consul作为服务发现（2019年源码修改了默认使用mdns），通过http进行通信，通过protobuf和json进行编码。

学习完该框架以后，可以方便开发者们非常简单地开发出微服务架构的项目，并且随着业务模块的增加和功能的增加，Go Micro还能够提供管理微服务环境的工具和功能。





