---
title: Golang
date: 2023-01-27 20:25:00
tags: Go
categories: 学习笔记
index_img: /img/go.jpg
---

# Go语言基本语法

## 行分隔符

在Go程序中，一行就代表一个语句的结束，无需使用“;”结尾，如果打算将多个语句写在同一行，则需要使用";"人为区分（不鼓励这种写法）

## 注释

单行注释：//

多行注释：/*   */

## 标识符

一个标识符是一个或是多个字母（A-Za-z）数字（0-9）、下划线_组成的序列，但是***<u>第一个字符必须是字母或下划线</u>***而不能是数字。



## 字符串连接

go语言的字符串连接可以通过“+”实现

```go
package main
import "fmt"

func main(){
	fmt.Println("hello" + "world")
}
```

## 关键字

go代码中会使用到的25个关键字或保留字

| break    | default     | func   | interface | select |
| -------- | ----------- | ------ | --------- | ------ |
| case     | defer       | go     | map       | struct |
| chan     | else        | goto   | package   | switch |
| const    | fallthrough | if     | range     | type   |
| continue | for         | import | return    | var    |

## 格式化字符串

Go语言中使用`fmt.Sprintf`或`fmt.Printf`格式化字符串并赋值给新串：

- **Sprintf** 根据格式化参数生成格式化的字符串并返回该字符串。
- **Printf** 根据格式化参数生成格式化的字符串并写入标准输出。

```go
package main
import "fmt"

func main(){
	var code = 123
	var enddate = "2022-12-31"
	var url = "Code=%d&endDate=%s"
	//fmt.Println("hello" + "world")
	fmt.Printf(url, code, enddate)
}
```

# Go语言数据类型

go语言中数据类型分为：**基本数据类型**和**复合数据类型**

基本数据类型有：

整型、浮点型、布尔型、字符串

复合数据类型有：

数组、切片、结构体、函数、map、通道（channel）、接口等。



## 整型

整型分为一下两大类：
有符号整型按长度分为：int8、int16、int32、int64

对应的无符号整型：uint8、uint16、uint32、uint64

unsafe.Sizeof()查看变量占用空间



## 浮点型

go语言支持两种浮点型数据：float32和float64，这两种浮点型数据格式遵循IEEE754标准：float32的浮点数的最大范围约为3.4e38，可以使用常量定义：math.MaxFloat32。float64的浮点数的最大范围约为1.8e308，可以使用一个常量定义：math.MaxFloat64



## 布尔型

布尔型的值只可以是常量true或者false。var b bool = true

1. 布尔类型变量的默认值为false
2. go语言中不允许将整型强制转换为布尔类型。
3. 布尔型无法参与数值运算，也无法与其他类型进行转换。

## 字符串类型

字符串就是一串固定长度的字符连接起来的字符序列。Go 的字符串是由单个字节连接起来的。Go 语言的字符串的字节使用 UTF-8 编码标识 Unicode 文本。

go语言中的字符串以基本数据类型出现，使用字符串就像使用其他原生数据类型（int、bool、float32、float64等）一样。

**string类型无法通过`unsafe.Sizeof()`得到存储空间大小，因为string底层使用的是结构体和指针实现，可以使用`len`**

### 多行字符串

go语言中要定义一个多行字符串时，就必须使用反引号字符`，反引号中转义字符无效。

```go
str :=
		`this is str
	this is str
	this is str	
	this is str	
	this is str	
	`
```

### 字符串的常用操作

| 方法                                | 介绍                                               |
| ----------------------------------- | -------------------------------------------------- |
| len(str)                            | 求长度（占用字节数）                               |
| +或fmt.Sprintf                      | 拼接字符串                                         |
| strings.Split                       | 分割                                               |
| strings.contains                    | 判断是否包含                                       |
| strings.HasPrefix,strings.HasSuffix | 前缀/后缀判断                                      |
| strings.Index(),strings.LastIndex() | 子串出现的位置，查找到返回下标位置，查找不到返回-1 |
| strings.Join(a []int, sep string)   | join操作                                           |



### byte和rune类型

组成每个字符串的元素叫做“字符”，可以通过遍历字符串元素获得字符。字符用单引号（'）包裹起来，不是基本数据类型

当我们直接输出byte（字符）的时候输出的是这个字符对应的码值

go语言的字符有以下两种：

1. uint8类型，或者叫byte型，代表了ASCII码的一个字符
2. rune类型，代表一个UTF-8字符

当需要处理中文、日文或者其他复合字符时，则需要用到rune类型。rune类型实际是一个int32。

Go使用了特殊的rune类型来处理Unicode，让基于Unicode的文本处理更为方便，也可以使用byte型进行默认字符串处理，性能和扩展性都有照顾。

```go
func main(){
    s := "你好，golang"
    for i:=0; i< len(s); i++ {	//byte
        fmt.Printf("%v(%c)", s[i], s[i])
    }
    
    for _,r := range s {	//rune
        fmt.Printf("%v(%c)", r, r)
    }
}
```



### 修改字符串

要修改字符串，需要先将其转换成[]rune或[]byte，完成后再转换为string。无论哪种转换，都会重新分配内存，并复制字节数组。

```go
func changeString()  {
	s1 := "big"
	byteS1 := []byte(s1)
	byteS1[0] = 'p'
	fmt.Println(string(byteS1))

	s2 := "白萝卜"
	runeS2 := []rune(s2)
	runeS2[0] = '红'
	fmt.Println(string(runeS2))
}
```

## 派生类型

- 指针类型（Pointer）
- 数组类型
- 结构体类型（struct）
- Channel类型
- 函数类型
- 切片类型
- 接口类型（interface）
- Map类型



## 类型转换

类型转换用于将一种数据类型的变量转换为另一种类型的变量。Go语言类型转换基本格式如下：

```go
type_name(expression)
//type_name 为类型，expression 为表达式。
```

实例：

```go
func main() {
   var sum int = 17
   var count int = 5
   var mean float32
   
   mean = float32(sum)/float32(count)
   fmt.Printf("mean 的值为: %f\n",mean)
}
```

go不支持隐式转换类型，如：

```go
func main() {  
    var a int64 = 3
    var b int32
    b = a
    fmt.Printf("b 为 : %d", b)
}
```

string转int、int转string

```go
func main() {
  // string to int
  aStr := "100"
  bInt, err := strconv.Atoi(aStr)

  if err == nil {
    fmt.Printf("aStr：%T %s，bInt：%T %d", aStr, aStr, bInt, bInt)
  } else {
    fmt.Printf("err：%s", err)
  }

  // int to string
  cInt := 200
  dStr := strconv.Itoa(cInt)

  fmt.Printf("cInt：%T %d，dStr：%T %s", cInt, cInt, dStr, dStr)
}
```



### 数值类型之间的转换

***<u>go语言中只有强制类型转换，没有隐式类型转换。</u>***

转换的时候建议从低位转换为高位，高位转换成低位的时候如果转换不成功就会溢出。



### 其他类型转换成String类型

1. Sprintf把其他类型转换成string类型

注意：sprintf使用中需要注意转换的格式，int 为%d，float为%f，bool为%t，byte为%c

2. 使用strconv包里面的几种转换方法进行转换

- 整型转字符串：strconv.FormatInt(int64(i), 10)
  - 参数1：int64 的数值
  - 参数2：传值int类型的进制

- 浮点型转字符串：strconv.FormatFloat()
  - 参数1：要转换的值
  - 参数2：格式化类型
    - 'f'(-ddd.dddd)
    - 'b'(-ddddp±ddd，指数为二进制)
    - 'e'(-d.dddde±dd，十进制指数)
    - 'E'(-d.ddddE±dd，十进制指数)
    - 'g'(指数很大时用'e'格式，否则'f'格式)
    - 'G'(指数很大时用'E'格式，否则'f'格式)
  - 参数3：保留的小数点  -1（不对小数点格式化）
  - 参数4：格式化的类型（32/64）

- 布尔型转字符：strconv.FormatBool()//意义不大
- 字符转数值：strconv.FormatUint()
- string类型转int类型：strconv.ParseInt(str, 10, 64)
  - 参数1：string数据
  - 参数2：进制
  - 参数3：位数32 64 16
- string类型转float类型：strconv.ParseFloat(str, 32)

**数值类型无法和bool类型进行转换**

## 数字字面量语法（Number literals syntax）

Go1.13版本之后引入了数字字面量语法，这样便于开发者以二进制、八进制或十六进制浮点数的格式定义数字。

如：num := 0b00011101



# Go语言变量

Go 语言变量名由字母、数字、下划线组成，其中首个字符不能为数字。

Go语言中变量是区分大小写的

声明变量的一般形式是使用 var 关键字

```go
var identifier type
```

可以一次声明多个变量

```go
var a string = "czh"
fmt.Println(a)
var b, c int = 1, 2
fmt.Println(b, c)
```



## 变量声明

第一种：指定变量类型，如果没有初始化，则变量默认为零值

- 数值类型零值为0
- 布尔类型零值为false
- 字符串为“”（空字符串）

第二种：根据值自行判定变量类型

第三种：如果变量已经使用var声明过了，再使用`:=`声明变量就产生编译错误。

Go语言中的变量需要声明后才能使用，同一作用域内不支持重复声明。

声明一个局部变量却没有在相同的代码块中使用它会得到编译错误，但是全局变量是允许声明但不使用的。

## 多变量声明

```go
//类型相同多个变量, 非全局变量
var vname1, vname2, vname3 type
vname1, vname2, vname3 = v1, v2, v3

var vname1, vname2, vname3 = v1, v2, v3 // 和 python 很像,不需要显示声明类型，自动推断

vname1, vname2, vname3 := v1, v2, v3 // 出现在 := 左侧的变量不应该是已经被声明过的，否则会导致编译错误

// 这种因式分解关键字的写法一般用于声明全局变量
var (
    vname1 v_type1
    vname2 v_type2
)
```



## 值类型和引用类型

所有像int、float、bool、string这些基本类型都属于值类型，使用这些变量的类型直接指向存在内存中的值

## Go语言变量作用域

作用域为已声明标识符所表示的常量、类型、变量、函数或包在源代码中的作用范围。

Go语言中变量可以在三个地方声明：

- 函数内定义的变量为局部变量
- 函数外定义的变量称为全局变量
- 函数定义中的变量称为形式参数

### 局部变量

在函数体内声明的变量称之为局部变量，它们的作用域只在函数体内，参数和返回值变量也是局部变量。



### 全局变量

在函数体外声明的变量称之为全局变量，全局变量可以在整个包甚至外部包（被导出后）使用。

Go 语言程序中**全局变量与局部变量名称可以相同**，但是函数内的局部变量会被优先考虑。

```go
/* 声明全局变量 */
var g int = 20

func main() {
   /* 声明局部变量 */
   var g int = 10

   fmt.Printf ("结果： g = %d\n",  g)//10
}
```



### 形式参数

形式参数会作为函数的局部变量来使用。



# Go语言常量

常量是一个简单的标识符，在程序运行时，不会被修改的值。

常量中的数据类型只可以是布尔型、数字型（整数型、浮点型和复数）和字符串型。

常量的定义格式：

```go
const identifier [type] = value
```

***<u>常量在定义的时候必须赋值。</u>***

可以省略类型说明符[type]，因为编译器可以根据变量的值来推断其类型。

- 显示类型定义：const b string = "abc"
- 隐式类型定义：const b = "abc"

多个常量可以一起声明

```go
const (
	pi = 3.1415924
    a = "a"
)
```

const同时声明多个常量时，如果省略了值则表示和上面一行的值相同，例如：

```go
const (
	n1 = 100
    n2
    n3
)
```

## const结合iota的使用

iota是go语言的常量计数器，只能在常量的表达式中使用。

iota在const关键字出现时将被重置为0（const内部的第一行之前），const中每新增一行常量声明将使iota计数一次（iota可理解为const语句块中的行索引）。

1.iota只能在常量的表达式中使用

```go
fmt.Println(iota)
编译错误：undefined:iota
```

2.每次const出现时，都会让iota初始化为0（自增长）

```go
const a = iota //a=0
const (
    b = iota //b = 0
    c		 //c = 1
)
```

3.const iota使用_跳过某些值

```go
const (
    n1 = iota	//0
    _	
    n2			//2
    n3			//3
)
```

4.iota声明中插队

```go
const (
    n1 = iota //0
    n2 = 100	//100
    n3 = iota	//2
    n4			//3
)
const n5 = iota //0
```

5.多个iota定义在一行

```go
const (
    a,b = iota + 1, iota + 2
    c,d		//2,3
    e,f		//3,4
)
```





# Go语言运算符

## 算数运算符

假定A值为10，B值为20。

| 运算符 | 描述 | 实例               |
| :----- | :--- | :----------------- |
| +      | 相加 | A + B 输出结果 30  |
| -      | 相减 | A - B 输出结果 -10 |
| *      | 相乘 | A * B 输出结果 200 |
| /      | 相除 | B / A 输出结果 2   |
| %      | 求余 | B % A 输出结果 0   |
| ++     | 自增 | A++ 输出结果 11    |
| --     | 自减 | A-- 输出结果 9     |

1. 除法注意：如果运算的数都是整数，那么除后，去掉小数部分，保留整数部分

2. 取余注意：余数=被除数 - （被除数/除数）*除数
3. 注意：++（自增）和--（自减）在Go中是单独的语句，并不是运算符。++ 和 -- 只能单独使用，不能赋值，错误写法如下：

```go
var i int = 8
var a int
a = i++ //错误i++只能单独使用
a = i-- //错误i--只能单独使用
```

注意：在golang中没有前++，错误写法如下：

```go
var i int = 1
++i	//错误，在golang中没有前++
--i	//错误，在golang中没有前--
```



## 关系运算符

假定A值为10，B值为20。

| 运算符 | 描述                                                         | 实例              |
| :----- | :----------------------------------------------------------- | :---------------- |
| ==     | 检查两个值是否相等，如果相等返回 True 否则返回 False。       | (A == B) 为 False |
| !=     | 检查两个值是否不相等，如果不相等返回 True 否则返回 False。   | (A != B) 为 True  |
| >      | 检查左边值是否大于右边值，如果是返回 True 否则返回 False。   | (A > B) 为 False  |
| <      | 检查左边值是否小于右边值，如果是返回 True 否则返回 False。   | (A < B) 为 True   |
| >=     | 检查左边值是否大于等于右边值，如果是返回 True 否则返回 False。 | (A >= B) 为 False |
| <=     | 检查左边值是否小于等于右边值，如果是返回 True 否则返回 False。 | (A <= B) 为 True  |

## 逻辑运算符

假定 A 值为 True，B 值为 False。

| 运算符 | 描述                                                         | 实例               |
| :----- | :----------------------------------------------------------- | :----------------- |
| &&     | 逻辑 AND 运算符。 如果两边的操作数都是 True，则条件 True，否则为 False。 | (A && B) 为 False  |
| \|\|   | 逻辑 OR 运算符。 如果两边的操作数有一个 True，则条件 True，否则为 False。 | (A \|\| B) 为 True |
| !      | 逻辑 NOT 运算符。 如果条件为 True，则逻辑 NOT 条件 False，否则为 True。 | !(A && B) 为 True  |

## 位运算符

| p    | q    | p & q | p \| q | p ^ q |
| :--- | :--- | :---- | :----- | :---- |
| 0    | 0    | 0     | 0      | 0     |
| 0    | 1    | 0     | 1      | 1     |
| 1    | 1    | 1     | 1      | 0     |
| 1    | 0    | 0     | 1      | 1     |

假定 A 为60，B 为13

| 运算符 | 描述                                                         | 实例                                   |
| :----- | :----------------------------------------------------------- | :------------------------------------- |
| &      | 按位与运算符"&"是双目运算符。 其功能是参与运算的两数各对应的二进位相与。 | (A & B) 结果为 12, 二进制为 0000 1100  |
| \|     | 按位或运算符"\|"是双目运算符。 其功能是参与运算的两数各对应的二进位相或 | (A \| B) 结果为 61, 二进制为 0011 1101 |
| ^      | 按位异或运算符"^"是双目运算符。 其功能是参与运算的两数各对应的二进位相异或，当两对应的二进位相异时，结果为1。 | (A ^ B) 结果为 49, 二进制为 0011 0001  |
| <<     | 左移运算符"<<"是双目运算符。左移n位就是乘以2的n次方。 其功能把"<<"左边的运算数的各二进位全部左移若干位，由"<<"右边的数指定移动的位数，高位丢弃，低位补0。 | A << 2 结果为 240 ，二进制为 1111 0000 |
| >>     | 右移运算符">>"是双目运算符。右移n位就是除以2的n次方。 其功能是把">>"左边的运算数的各二进位全部右移若干位，">>"右边的数指定移动的位数。 | A >> 2 结果为 15 ，二进制为 0000 1111  |

## 赋值运算符

| 运算符 | 描述                                           | 实例                                  |
| :----- | :--------------------------------------------- | :------------------------------------ |
| =      | 简单的赋值运算符，将一个表达式的值赋给一个左值 | C = A + B 将 A + B 表达式结果赋值给 C |
| +=     | 相加后再赋值                                   | C += A 等于 C = C + A                 |
| -=     | 相减后再赋值                                   | C -= A 等于 C = C - A                 |
| *=     | 相乘后再赋值                                   | C *= A 等于 C = C * A                 |
| /=     | 相除后再赋值                                   | C /= A 等于 C = C / A                 |
| %=     | 求余后再赋值                                   | C %= A 等于 C = C % A                 |
| <<=    | 左移后赋值                                     | C <<= 2 等于 C = C << 2               |
| >>=    | 右移后赋值                                     | C >>= 2 等于 C = C >> 2               |
| &=     | 按位与后赋值                                   | C &= 2 等于 C = C & 2                 |
| ^=     | 按位异或后赋值                                 | C ^= 2 等于 C = C ^ 2                 |
| \|=    | 按位或后赋值                                   | C \|= 2 等于 C = C \| 2               |

## 其他运算符

| 运算符 | 描述             | 实例                       |
| :----- | :--------------- | :------------------------- |
| &      | 返回变量存储地址 | &a; 将给出变量的实际地址。 |
| *      | 指针变量。       | *a; 是一个指针变量         |

# Go语言条件语句

## if语句

Go语言中if语句的语法如下：

```go
if 布尔表达式 {
   /* 在布尔表达式为 true 时执行 */
}
```

If 在布尔表达式为 true 时，其后紧跟的语句块执行，如果为 false 则不执行。

```go
//第一种
age := 30
if age > 20 {
    fmt.Println("成年人")
}

//第二种
if age := 30; age > 20 {
    fmt.Println("成年人")
}

//两种写法age的作用域不同，第二种作用域在if内
```



## if...else语句

Go语言中if...else语句的语法如下：

```go
if 布尔表达式 {
   /* 在布尔表达式为 true 时执行 */
} else {
  /* 在布尔表达式为 false 时执行 */
}
```

If 在布尔表达式为 true 时，其后紧跟的语句块执行，如果为 false 则执行 else 语句块。

## if语句嵌套

略

## switch语句

switch 语句用于基于不同条件执行不同动作，每一个 case 分支都是唯一的，从上至下逐一测试，直到匹配为止。

switch 语句执行的过程从上至下，直到找到匹配项，匹配项后面也不需要再加 break。

switch 默认情况下 **case 最后自带 break 语句**，匹配成功后就不会执行其他 case，如果我们需要执行后面的 case，可以使用 `fallthrough`。

go语言中switch语句的语法如下：

```go
switch var1 {
    case val1:
        ...
    case val2:
        ...
    default:
        ...
}
```

变量 var1 可以是任何类型，而 val1 和 val2 则可以是同类型的任意值。类型不被局限于常量或整数，但必须是相同的类型；或者最终结果为相同类型的表达式。

switch的另一种写法：

```go
switch extName := ".html";extName {
	case ".html":
		fmt.Println("text/html")
		fallthrough
	case ".css":
		fmt.Println("text/css")
	case ".js":
		fmt.Println("text/javascript")
	default:
		fmt.Println("找不到此后缀")
	}
//extName写switch内部或外部时的作用域不同
```

switch的另一种写法：

一个分支可以有多个值，多个case值中间使用英文逗号分割

```go
var n = 5
switch n {
case 1, 3, 5, 7, 9:
    fmt.Println("奇数")
case 2, 4, 6, 8, 10:
    fmt.Println("偶数")
}
```

switch的另一种写法：

一个分支还可以使用表达式，此时switch后面不需要跟变量

```go
age := 20
	switch {
	case age < 24:
		fmt.Println("好好学习")
	}
```



### fallthrough

使用 fallthrough 会强制执行后面的 case 语句，fallthrough 不会判断下一条 case 的表达式结果是否为 true。



# Go语言循环语句

## for循环

go语言的for循环有3中形式:

```go
//和C语言的for一样
for init; condition; post {}
```

```go
//和C的while一样
for condition {}
```

```go
//和C的for(;;)一样
for {}
```

- init：一般为赋值表达式，给控制变量赋初值；
- condition：关系表达式或逻辑表达式，循环控制条件
- post：一般为赋值表达式，给控制变量增量或减量

for循环的range格式可以对slice、map、数组、字符串等进行迭代循环。格式如下：

```go
for key, value := range oldMap {
    newMap[key] = value
}
```

以上代码中的key和value是可以省略的。

如果只想读取key，格式如下：
```go
for key := range oldMap
//或者
for key,_ := range oldMap
```

 如果只想读取value，格式如下：
```go
for _,value := range oldMap
```



## 循环嵌套

```go
for [condition |  ( init; condition; increment ) | Range]
{
   for [condition |  ( init; condition; increment ) | Range]
   {
      statement(s);
   }
   statement(s);
}
```



## 循环控制语句

### break语句

- 用于循环语句中跳出循环，并开始执行循环之后的语句。
- break 在 switch（开关语句）中在执行一条 case 后跳出语句的作用。
- 在多重循环中，可以用标号 label 标出想 break 的循环。

### continue语句

for 循环中，执行 continue 语句会触发 for 增量语句的执行。

在多重循环中，可以用标号 label 标出想 continue 的循环。

### goto语句

Go 语言的 goto 语句可以无条件地转移到过程中指定的行。

goto 语句通常与条件语句配合使用。可用来实现条件转移， 构成循环，跳出循环体等功能。

但是，在结构化程序设计中一般不主张使用 goto 语句， 以免造成程序流程的混乱，使理解和调试程序都产生困难。

语法：

```go
goto label;
..
.
label: statement;
```

```go
package main
import "fmt"

func main() {
   /* 定义局部变量 */
   var a int = 10

   /* 循环 */
   LOOP: for a < 20 {
      if a == 15 {
         /* 跳过迭代 */
         a = a + 1
         goto LOOP
      }
      fmt.Printf("a的值为 : %d\n", a)
      a++    
   }  
}
```



## 无限循环

如果循环中条件语句永远不为 false 则会进行无限循环，我们可以通过 for 循环语句中只设置一个条件表达式来执行无限循环：

```go
package main
import "fmt"
func main() {
    for true  {
        fmt.Printf("这是无限循环。\n");
    }
}
```

# Go语言数组

## 声明数组

Go 语言数组声明需要指定元素类型及元素个数，语法格式如下：

```go
var variable_name [SIZE] variable_type
```

例如以下定义了数组 balance 长度为 10 类型为 float32：

```go
var balance [10] float32
```

## 初始化数组

```go
var balance = [5]float32{1000.0, 2.0, 3.4, 7.0, 50.0}

balance := [5]float32{1000.0, 2.0, 3.4, 7.0, 50.0}
```

如果数组长度不确定，可以使用`...`代替数组的长度，编译器会根据元素个数自行推断数组的长度：

```go
var balance = [...]float32{1000.0, 2.0, 3.4, 7.0, 50.0}
//或
balance := [...]float32{1000.0, 2.0, 3.4, 7.0, 50.0}
```

如果设置了数组的长度，我们还可以通过指定下标来初始化元素：

```go
balance := [5]float{1:2.0, 3:7.0}
```

初始化数组中`{}`中的元素个数不能大于`[]`中的数字。

如果忽略`[]`中的数字不设置数组的大小，go语言会根据元素的个数来设置数组的大小：

```go
balance[4] = 50.0
```



## 访问数组元素

数组元素可以通过索引（位置）来读取。格式为数组后加中括号，中括号为索引的值。例如：

```go
var salary float32 = balance[9]
```



### go语言多维数组

Go 语言支持多维数组，以下为常用的多维数组声明方式：

```go
var variable_name [SIZE1][SIZE2]...[SIZEN] variable_type

var threedim [5][10][4]int
```

**注意：多维数组只有第一层可以使用...来让编译器推导数组长度。**

### go语言向函数传递数组

如果你想向函数传递数组参数，你需要在函数定义时，声明形参为数组，我们可以通过以下两种方式来声明：

#### 方式一

形参设定数组大小：

```go
void myFunction(param [10]int)
{
    ...
}
```

#### 方式二

形参未设定数组大小：

```go
void myFunction(param []int)
{
    ...
}
```

```go
func main() {
   /* 数组长度为 5 */
   var  balance = [5]int {1000, 2, 3, 17, 50}
   var avg float32

   /* 数组作为参数传递给函数 */
   avg = getAverage( balance, 5 ) ;

   /* 输出返回的平均值 */
   fmt.Printf( "平均值为: %f ", avg );
}
func getAverage(arr [5]int, size int) float32 {
   var i,sum int
   var avg float32  

   for i = 0; i < size;i++ {
      sum += arr[i]
   }

   avg = float32(sum) / float32(size)

   return avg;
}
```

# Go语言Slice（切片）

Go 语言切片是对数组的抽象。

Go 数组的长度不可改变，在特定场景中这样的集合就不太适用，Go 中提供了一种灵活，功能强悍的内置类型切片("动态数组")，与数组相比切片的长度是不固定的，可以追加元素，在追加时可能使切片的容量增大。

切片是一个***<u>引用类型</u>***，它的内部结构包含`地址`、`长度`和`容量`

## 定义切片

可以声明一个未指定大小的数组来定义切片：

```go
var identifier []type
```

**切片不需要说明长度。**

或使用make()函数来创建切片：

```go
var slice1 []type = make([]type, len)
//或简写为
slice1 := make([]type, len)
```

也可以指定容量，其中capacity为可选参数。

```go
make([]T, length, capacity)
```

这里len是数组的长度并且也是切片的初始长度。



## 切片初始化

```go
s := []int {1,2,3}
//直接初始化切片，[]表示是切片类型，{1，2，3}初始化值依次是`1,2,3`，其cap=len=3
```

```go
s := arr[:]
//初始化切片 s，是数组 arr 的引用。
```

```go
s := arr[startIndex:endIndex]
//将 arr 中从下标 startIndex 到 endIndex-1 下的元素创建为一个新的切片。
```

```go
s := arr[startIndex:]
//默认 endIndex 时将表示一直到arr的最后一个元素。
```

```go
s := arr[:endIndex]
//默认 startIndex 时将表示从 arr 的第一个元素开始。
```

```go
s1 := s[startIndex:endIndex]
//通过切片 s 初始化切片 s1。
```

```go
s := make([]int, len, cap)
//通过内置函数 make() 初始化切片s，[]int 标识为其元素类型为 int 的切片。
```

## len()和cap()函数

切片是可索引的，并且可以由 len() 方法获取长度。

切片提供了计算容量的方法 cap() 可以测量切片最长可以达到多少。

**切片的长度就是它所包含的元素的个数。**

**切片的容量是从它的第一个元素开始数，到其底层数组元素末尾的个数。**

## 空(nil)切片

一个切片在未初始化之前默认为 nil，长度为 0，实例如下：

```go
func main() {
   var numbers []int

   printSlice(numbers)

   if(numbers == nil){
      fmt.Printf("切片是空的")
   }
}

func printSlice(x []int){
   fmt.Printf("len=%d cap=%d slice=%v\n",len(x),cap(x),x)
}

//输出：
//len=0 cap=0 slice=[]
//切片是空的
```

## append()和copy()函数

如果想增加切片的容量，我们必须创建一个新的更大的切片并把原分片的内容都拷贝过来。

下面的代码描述了从拷贝切片的 copy 方法和向切片追加新元素的 append 方法。

```go
func main() {
   var numbers []int
   printSlice(numbers)

   /* 允许追加空切片 */
   numbers = append(numbers, 0)
   printSlice(numbers)

   /* 向切片添加一个元素 */
   numbers = append(numbers, 1)
   printSlice(numbers)

   /* 同时添加多个元素 */
   numbers = append(numbers, 2,3,4)
   printSlice(numbers)

   /* 创建切片 numbers1 是之前切片的两倍容量*/
   numbers1 := make([]int, len(numbers), (cap(numbers))*2)

   /* 拷贝 numbers 的内容到 numbers1 */
   copy(numbers1,numbers)
   printSlice(numbers1)  
}

func printSlice(x []int){
   fmt.Printf("len=%d cap=%d slice=%v\n",len(x),cap(x),x)
}
//输出：
len=0 cap=0 slice=[]
len=1 cap=1 slice=[0]
len=2 cap=2 slice=[0 1]
len=5 cap=6 slice=[0 1 2 3 4]
len=5 cap=12 slice=[0 1 2 3 4]
```

append合并切片的时候最后一个元素要加...

```go
a := []int{30,31,32,33,34,35,36,37}
a = append(a[0:2], a[3:]...)
//删除第2个元素（32）
//...的作用是将切片展开，将元素当参数传入
```

# Go语言sort排序

对于int、float64和string数组或是切片的排序，go分别提供了sort.Ints()、sort.Float64s()和sort.Strings()函数，默认都是从小到大排序。

```go
intList := []int{2,4,3,5,7,6,9,8,1,0}
	floatList := []float64{4.2,5.9,12.4,10.2,50.7,99.9,31.4,27.81828,3.14}
	stringList := []string{"a", "b", "c", "z", "g", "x", "f", "i"}

	sort.Ints(intList)
	sort.Float64s(floatList)
	sort.Strings(stringList)
	fmt.Printf("%v\n%v\n%v\n", intList, floatList, stringList)
```

从大到小：

```go
sort.Sort(sort.Reverse(sort.IntSlice(intList)))
```

# Go语言Map（集合）

Map 是一种无序的基于key-value的数据结构，Go语言中的map是***<u>引用类型</u>***，必须初始化才能使用。

Map 是一种集合，所以我们可以像迭代数组和切片那样迭代它。不过，Map 是无序的，我们无法决定它的返回顺序，这是因为 Map 是使用 hash 表来实现的。

## 定义Map

可以使用内建函数make也可以使用map关键字来定义Map：

```go
/* 声明变量，默认 map 是 nil */
var map_variable map[key_data_type]value_data_type

/* 使用 make 函数 */
map_variable := make(map[key_data_type]value_data_type)
```

如果不初始化 map，那么就会创建一个 nil map。nil map 不能用来存放键值对

```go
var userinfo = map[string]string{
    "username": "张三",
    "age": "20",
    "sex": "男",
}
```

```go
//创建/修改map类型的数据
var userinfo = make(map[string]string)
userinfo["username"] = "张三"
userinfo["age"] = "20"
```



## delete()函数

delete() 函数用于删除集合的元素, 参数为 map 和其对应的 key。

实例：

 ```go
func main() {
        /* 创建map */
        countryCapitalMap := map[string]string{"France": "Paris", "Italy": "Rome", "Japan": "Tokyo", "India": "New delhi"}
        fmt.Println("原始地图")
        /* 打印地图 */
        for country := range countryCapitalMap {
                fmt.Println(country, "首都是", countryCapitalMap [ country ])
        }
        /*删除元素*/
    	delete(countryCapitalMap, "France")
        fmt.Println("法国条目被删除")
        fmt.Println("删除元素后地图")
        /*打印地图*/
        for country := range countryCapitalMap {
                fmt.Println(country, "首都是", countryCapitalMap [ country ])
        }
}
 ```



# Go语言函数

Go语言中支持：函数、匿名函数和闭包



## 函数定义

go语言函数定义格式如下：

```go
func function_name( [parameter list] ) [return_types] {
   函数体
}

//即
func 函数名(参数) (返回值) {
    函数体
}
```

函数定义解析：

- func：函数由 func 开始声明
- function_name：函数名称，参数列表和返回值类型构成了函数签名。
- parameter list：参数列表，参数就像一个占位符，当函数被调用时，你可以将值传递给参数，这个值被称为实际参数。参数列表指定的是参数类型、顺序、及参数个数。参数是可选的，也就是说函数也可以不包含参数。
- return_types：返回类型，函数返回一列值。return_types 是该列值的数据类型。有些功能不需要返回值，这种情况下 return_types 不是必须的。
- 函数体：函数定义的代码集合。

**在同一个包内，函数名不能重名**

实例：
```go
/* 函数返回两个数的最大值 */
func max(num1, num2 int) int {
   /* 声明局部变量 */
   var result int

   if (num1 > num2) {
      result = num1
   } else {
      result = num2
   }
   return result
}
```



## 函数参数

### 函数参数的简写

```go
func sub(x int, y int) int {
    sub := x - y
    return sub
}

//简写后
func sub(x, y int) int {
    sub := x - y
    return sub
}
//相当于前面没有指定类型的参数，与其后面参数类型相同
```

### 函数的可变参数

可变参数是指函数的参数数量不固定，go语言中的可变参数通过在参数名后加...来表示

```go
func mySum(x ...int) int {
	fmt.Printf("%v--%T\n", x, x)
	sum := 0
	for _, v := range x {
		sum += v
	}
	return sum
}
```

固定参数与可变参数可以混合使用，但是可变参数必须写在固定参数后面

```go
func mySum(x int, y ...int) int{
    
}
```



### 值传递

值传递是指在调用函数时将实际参数复制一份传递到函数中，这样在函数中如果对参数进行修改，将不会影响到实际参数。

```go
/* 定义相互交换值的函数 */
func swap(x, y int) int {
   var temp int

   temp = x /* 保存 x 的值 */
   x = y    /* 将 y 值赋给 x */
   y = temp /* 将 temp 值赋给 y*/

   return temp;
}
```



### 引用传递

引用传递是指在调用函数时将实际参数的地址传递到函数中，那么在函数中对参数所进行的修改，将影响到实际参数。

```go
/* 定义交换值函数*/
func swap(x *int, y *int) {
   var temp int
   temp = *x    /* 保持 x 地址上的值 */
   *x = *y      /* 将 y 值赋给 x */
   *y = temp    /* 将 temp 值赋给 y */
}
```

## 函数返回值

- `return`关键字一次可以返回多个值

```go
func calc(x, y int) (int, int){
    sum := x + y
    sub := x - y
    return sum, sub
}
```

- 返回值命名：函数定义时可以给返回值命名，并在函数体中直接使用这些变量，最后通过`return`关键字返回

```go
func calc(x, y int) (sum int, sub int){ // sum后的int也可以省略
    sum = x + y
    sub = x - y
    return sum, sub
}

```

命名返回值会被视为定义在函数顶部的变量，并且在使用 return 语句返回时，不再必须在其后面指定参数名，也就是支持“裸”返回。例如：

```go
func name(s string) (str string){
	str = s
	return
}

func main() {
	fmt.Println(name("czh")) //czh
}
```

在使用了命名返回值的情况下也可以返回其他变量

```go
func name(s string) (str string){
	str = s
	str1 := "200"
	return str1
}
func main() {
	fmt.Println(name("czh")) //200
}
```



## 函数变量作用域

全局变量：全局变量是定义在函数外部的变量，它在程序整个运行周期内有效。

局部变量：局部变量是函数内部定义的变量，函数内定义的变量无法在该函数外使用。



## Go语言递归函数

递归，就是在运行的过程中调用自己。

Go 语言支持递归。但我们在使用递归时，开发者需要设置退出条件，否则递归将陷入无限循环中。



## 函数类型与变量

### 定义函数类型

可以使用type关键字来定义一个函数类型，具体格式如下：

```go
type calc func(int ,int) int //没有花括号
```

上面语句定义了一个calc类型，它是一种函数类型，这种函数接收两个int类型的参数并返回一个int类型的返回值。

简单来说，凡是满足这个条件的函数都是calc类型的函数，例如：
```go
type cal func(int, int) int

func add(x, y int) int {
	return x + y
}

func sub(x, y int) int {
	return x - y
}

func main()  {

	var c cal
	c = add
	fmt.Printf("c的类型：%T\n", c)//c的类型：main.cal
    fmt.Println(c(10, 5))//15
    
    f := sub
	fmt.Printf("f的类型：%T\n", f)//f的类型：func(int, int) int
    fmt.Println(f(10, 5))//10
}
```

```go
//也可以自定义其他类型，类似于C中的#define
type myInt int

```

### 函数作为另一个函数的参数

函数定义后可作为另外一个函数的参数传入

```go
func add(x, y int) int {
	return x + y
}

func sub(x, y int) int {
	return x - y
}

type calcType func(int, int) int

func calc(x, y int, cb calcType) int{
    return cb(x, y)
}
//或者
//func calc(x, y int, cb func(int, int) int) int{
//    return cb(x, y)
//}

func main(){
    s := calc(10, 5, add)
    fmt.Println(s)//15
    
    j := calc(3, 4, func(x, y int) int{//传入匿名函数
        return x * y
    })
    fmt.Println(j)//12
}
```



```go
func main(){
   /* 声明函数变量 */
   getSquareRoot := func(x float64) float64 {
      return math.Sqrt(x)
   }

   /* 使用函数 */
   fmt.Println(getSquareRoot(9))

}
```

### 函数作为返回值

```go
type calcType func(int, int) int

func do(o string) calcType{
    switch o {
    case "+":
    	return add
    case "-":
        return sub
    case "*":
        return func(x, y int)int{//匿名函数
            return x * y
        }
    default:
        return nil
    }
}

func main(){
    var a = do("+")
    fmt.Println(a(12, 4))//16
}
```





## 高阶函数



## 匿名函数和闭包

### 匿名函数

函数当然还可以作为返回值，但是在Go语言中函数内部不能再像之前那样定义函数了，只能定义匿名函数。匿名函数就是没有函数名的函数，匿名函数的定义格式如下：

```go
func(参数)(返回值){
    
}
```

匿名函数因为没有函数名，所以没办法像普通函数那样调用，所以匿名函数需要保存到某个变量或者作为立即执行的函数

```go
func main(){
    //匿名函数 匿名自执行函数
    func(){
        fmt.Println("test...")
    }()//加括号表示自执行
    
    var fn = func(x, y int) int {
        return x * y
    }
    fmt.Println(fn(2,3))//6
    
    //匿名自执行函数接收参数
    func(x, y int){
        fmt.Println(x, y)//10 20
    }(10, 20)
}
```

匿名函数多用于实现回调函数和闭包。

### 闭包

闭包

1. 闭包是指有权访问另一个函数作用域中的变量的函数。
2. 创建闭包的常见方式就是在一个函数内部创建另一个函数，通过另一个函数访问这个函数的局部变量。

全局变量特点：

1. 常驻内存
2. 污染全局

局部变量的特点：

1. 不常住内存
2. 不污染全局

闭包的特点：

1. 可以让一个变量常驻内存
2. 可以让一个变量不污染全局

闭包是匿名函数，可在动态编程中使用

闭包可以理解成“定义在一个函数内部的函数”。在本质上，闭包是将函数内部和函数外部连接起来的桥梁。或者说是函数和其引用环境的组合体。

以下实例中，我们创建了函数 getSequence() ，返回另外一个函数。该函数的目的是在闭包中递增 i 变量，代码如下：

```go
func getSequence() func() int {
   i:=0
   return func() int {
      i+=1
     return i  
   }
}

func main(){
   /* nextNumber 为一个函数，函数 i 为 0 */
   nextNumber := getSequence()  

   /* 调用 nextNumber 函数，i 变量自增 1 并返回 */
   fmt.Println(nextNumber()) //1
   fmt.Println(nextNumber()) //2
   fmt.Println(nextNumber()) //3
   
   /* 创建新的函数 nextNumber1，并查看结果 */
   nextNumber1 := getSequence()  
   fmt.Println(nextNumber1()) //1
   fmt.Println(nextNumber1()) //2
}
```

**注意：由于闭包作用域返回的局部变量资源不会被立刻销毁回收，所以可能会占用更多的内存。过度使用闭包会导致性能下降，建议在非常有必要的时候才使用闭包。**



## defer语句

Go语言中的defer语句会将其后面跟随的语句进行延迟处理。在defer归属的函数即将返回时，将延迟处理的语句按defer定义的***<u>逆序</u>***进行执行，也就是说，先被defer的语句最后被执行，最后被defer的语句，最先被执行。

**defer 在命名返回值和普通返回值的函数或方法中，返回的结果不一样。**

例子：

```go
func f2() int {
	var a int	//0

	defer func() {
		a++
	}()

	return a
}

func f3() (a int) {
	defer func() {
		a++
	}()

	return a
}
func main() {
    
	fmt.Println(f2())//0
	fmt.Println(f3())//1
}
```

defer注册要延迟执行的函数时该函数所有的参数都需要确定其值

```go
func myCalc(index string, a, b int) int {
	ret := a + b
	fmt.Println(index, a, b, ret)
	return ret
}
func main() {
	x := 1
	y := 2
	defer myCalc("AA", x, myCalc("A", x, y))
	x = 10
	defer myCalc("BB", x, myCalc("B", x, y))
	y = 20
}
/*
注册顺序
	defer myCalc("AA", x, myCalc("A", x, y))
	defer myCalc("BB", x, myCalc("B", x, y))
执行顺序
	defer myCalc("BB", x, myCalc("B", x, y))
	defer myCalc("AA", x, myCalc("A", x, y))
*/
//1.myCalc("A", x, y) A 1 2 3
//2.myCalc("B", x, y) B 10 2 12
//3.myCalc("BB", x, myCalc("B", x, y)) BB 10 12 22
//4.myCalc("AA", x, myCalc("A", x, y)) AA 1 3 4
```



### defer执行时机

在Go语言的函数中return语句在底层并不是原子操作，它分为给返回值赋值和RET指令两步。而defer语句执行的时机就在返回值赋值操作后，RET指令执行前。具体如下图所示：
![defer执行时机](a57c1d07a9eb6c1476f5faa2a8095fc8.png)

[Golang中defer的执行时机_清秋-的博客-CSDN博客_defer什么时候执行](https://blog.csdn.net/qq_36867807/article/details/116406954)

（自己的理解：命名返回值的时候相当于已经决定好了返回的变量对应的地址，那么对该要返回的变量的修改都在此地址处进行修改，最后执行RET指令时返回该地址中的值，因此defer语句可以对返回值产生影响；而普通返回值则是在return的第一步时将返回值copy了一份到一个地址中，执行RET指令时就返回这个地址中的值，而defer语句中进行修改也只是修改原先变量地址中的值，因此不会对返回值产生影响）

## 内置函数panic/recover

| 内置函数       | 介绍                                                         |
| -------------- | ------------------------------------------------------------ |
| close          | 主要用来关闭channel                                          |
| len            | 用来求长度，比如string、array、slice、map、channel           |
| new            | 用来分配内存，主要用来分配值类型，比如int、struct。返回的是指针 |
| make           | 用来分配内存，主要用来分配引用类型，比如chan、map、slice     |
| append         | 用来追加元素到数组、slice中                                  |
| panic和recover | 用来做错误处理                                               |

Go语言中目前是没有异常机制，但是使用panic/recover模式来处理错误。panic可以在任何地方引发，但recover只有在defer调用的函数中有效。

```go
func fn1() {
	fmt.Println("fn1")
}

func fn2() {
	defer func() {
		err := recover()
		if err != any(nil) {
			fmt.Println(err)
		}
	}()
	panic(any("抛出一个异常"))
}

func main()  {
	fn1()
	fn2()
	fmt.Println("结束")
}
//fn1
//抛出一个异常
//结束
```

recover之后程序会继续往下执行

```go
func fn1(a, b int) int {
	defer func() {
		err := recover()
		if err != any(nil) {
			fmt.Println("error:", err)
		}
	}()
	fmt.Println(a/b)
	return a/b
}
func main()  {
	fmt.Println(fn1(10, 0))
	fmt.Println("结束")
	fn1(10, 2)
}
//error: runtime error: integer divide by zero
//0
//结束
//5
```



# Go语言time包及日期函数

## time包



## time.Now()获取当前时间

```go
func main() {
	now := time.Now()
	fmt.Printf("%v -- %T\n", now, now)//2023-01-04 20:12:50.677304 +0800 CST m=+0.003117301 -- time.Time

	year := now.Year()
	month := now.Month()
	day := now.Day()
	hour := now.Hour()
	minute := now.Minute()
	second := now.Second()

	fmt.Printf("%d-%02d-%02d %02d:%02d:%02d\n", year,month,day,hour,minute,second)
}
```

## now.Format格式化输出日期字符串

时间类型有一个自带的方法Format进行格式化

需要注意的是Go语言中格式化时间模板不是常见的Y-m-d H:M:S

而是使用Go的诞生时间2006年1月2日15点04分（记忆口诀为2006 1 2 3 4）

```go
func main() {
	now := time.Now()
	//2023-01-04
	str := now.Format("2006-01-02 03:04:05")
	fmt.Println(str)
}
```

2006 年

01 月

02 日

03 时（12小时制） 15 时（24小时制）

04 分

05 秒

## 获取当前时间戳

获取秒时间戳

```go
func main() {
	now := time.Now()
	unix := now.Unix()
	fmt.Println(unix)
}
```

获取纳秒时间戳

```go
func main() {
	now := time.Now()
	unix := now.UnixNano()
	fmt.Println(unix)
}
```



## 时间戳转换为日期字符（年-月-日 时:分:秒）

```go
func main(){
    unixTime := 1672835458
	timeObj := time.Unix(int64(unixTime), 0)//第一个参数写毫秒时间戳，第二个参数写纳秒时间戳，没有就写0

	str1 := timeObj.Format("2006-01-02 15:04:05")
	fmt.Println(str1)//2023-01-04 20:30:58
}
```

## 日期字符串转换成时间戳

```go
func main(){
    var str2 = "2023-01-04 20:30:58"
	var tmp = "2006-01-02 15:04:05" //模板
	location, _ := time.ParseInLocation(tmp, str2, time.Local)
	fmt.Println(location)
    fmt.Println(location.Unix())
}
```

## 时间间隔

time.Duration是time包定义的一个类型，它代表两个时间点之间经过的时间，以纳秒为单位。time.Duration表示一段时间间隔，可表示的最长时间段大约为290年。

time包中定义的时间间隔类型的常量如下：

```go
const (
    Nanosecond Duration = 1
    Microsecond			= 1000 * Nanosecond
    Millisecond			= 1000 * Microsecond
    Second				= 1000 * Millisecond
    Minute				= 60 * Second
    Hour				= 60 * Minute
)
```

## 时间操作函数

Add：时间+时间间隔

```go
func (t Time) Add(d Duration) Time
```

```go
//求1小时之后的时间
func main(){
    now := time.Now()
    later := now.Add(time.Hour)
    fmt.Println(later)
}
```

Sub：求两个时间的差值

```go
func (t Time) Sub(u Time) Duration
```

返回一个时间段t-u。如果结果超出了Duration可以表示的最大值/最小值，将返回最大值/最小值。要获取时间点t-d（d为Duration），可以使用t.Add(-d)。

Equal

```go
func (t Time) Equal(u Time) bool
```

判断两个时间是否相同，会考虑时区的影响，因此不同时区标准的时间也可以正确比较。本方法和用t==u不同，这种方法还会比较地点和时区信息。

Before

```go
func (t Time) Before(u Time) bool
```

After

```go
func (t Time) After(u Time) bool
```

## 定时器

1.使用time.NewTicker(时间间隔)来设置定时器

```go
func main(){
    ticker := time.NewTicker(time.Second)
	n := 5
	for t := range ticker.C {
		n--
		fmt.Println(t)
		if n == 0 {
			ticker.Stop()
			break
		}
	}
}
```



2.time.Sleep(time.Second)来实现定时器

```go
func main(){
    for {
        time.Sleep(timme.Second)
        fmt.Println("我在定时执行任务")
    }
}
```



# Go语言指针

Go 语言的取地址符是 &，放到一个变量前使用就会返回相应变量的内存地址。

```go
func main() {
   var a int = 10  

   fmt.Printf("变量的地址: %x\n", &a  )
}
```

指针声明格式如下：

```go
var var_name *var-type
```

var-type 为指针类型，var_name 为指针变量名，* 号用于指定变量是作为一个指针。以下是有效的指针声明：

```go
var ip *int        /* 指向整型*/
var fp *float32    /* 指向浮点型 */
```

```go
func main() {
   var a int= 20   /* 声明实际变量 */
   var ip *int        /* 声明指针变量 */

   ip = &a  /* 指针变量的存储地址 */

   fmt.Printf("a 变量的地址是: %x\n", &a  )

   /* 指针变量的存储地址 */
   fmt.Printf("ip 变量储存的指针地址: %x\n", ip )

   /* 使用指针访问值 */
   fmt.Printf("*ip 变量的值: %d\n", *ip )
}
```

## Go空指针

当一个指针被定义后没有分配到任何变量时，它的值为nil。

nil指针也称为空指针。

nil在概念上和其他语言的null、None、nil、NULL一样，都指代零值或空值。

## Go指针数组

声明整型指针数组：

```go
var ptr [MAX]*int
```

```go
const MAX int = 3

func main() {
   a := []int{10,100,200}
   var i int
   var ptr [MAX]*int;

   for  i = 0; i < MAX; i++ {
      ptr[i] = &a[i] /* 整数地址赋值给指针数组 */
   }

   for  i = 0; i < MAX; i++ {
      fmt.Printf("a[%d] = %d\n", i,*ptr[i] )
   }
}
```



## Go指向指针的指针

如果一个指针变量存放的又是另一个指针变量的地址，则称这个指针变量为指向指针的指针变量。

指向指针的指针变量声明格式如下：

```go
var ptr **int
```

```go
func main() {
   var a int
   var ptr *int
   var pptr **int
   a = 3000
   /* 指针 ptr 地址 */
   ptr = &a
   /* 指向指针 ptr 地址 */
   pptr = &ptr
   /* 获取 pptr 的值 */
   fmt.Printf("变量 a = %d\n", a )
   fmt.Printf("指针变量 *ptr = %d\n", *ptr )
   fmt.Printf("指向指针的指针变量 **pptr = %d\n", **pptr)
}
```



## Go向函数传递指针参数

Go 语言允许向函数传递指针，只需要在函数定义的参数上设置为指针类型即可。

实例：

```go
func main() {
   /* 定义局部变量 */
   var a int = 100
   var b int= 200

   fmt.Printf("交换前 a 的值 : %d\n", a )//100
   fmt.Printf("交换前 b 的值 : %d\n", b )//200

   /* 调用函数用于交换值
   * &a 指向 a 变量的地址
   * &b 指向 b 变量的地址
   */
   swap(&a, &b);

   fmt.Printf("交换后 a 的值 : %d\n", a )//200
   fmt.Printf("交换后 b 的值 : %d\n", b )//100
}

func swap(x *int, y *int) {
   var temp int
   temp = *x    /* 保存 x 地址的值 */
   *x = *y      /* 将 y 赋值给 x */
   *y = temp    /* 将 temp 赋值给 y */
}
```

# new和make函数

在Go语言中对于引用数据类型的变量，我们在使用的时候不仅要声名它，还要为它分配内存空间，否则我们的值就没办法存储。而对于值类型的声明不需要分配内存空间，是因为它们在声明的时候已经默认分配好了内存空间。要分配内存就需要new和make。Go语言中new和make是内建的两个函数，主要用来分配内存。

### new函数分配内存

new是一个内置的函数，它的函数签名如下：

```go
func new(Type) *Type
```

其中：

- Type表示类型，new函数只接受一个参数，这个参数是一个类型
- *Type表示类型指针，new函数返回一个指向该类型内存地址的指针。

实际开发中new函数不太常用，使用new函数得到的是一个类型的指针，并且该指针对应的值为该类型的零值，例子：

```go
func main(){
    a := new(int)
    b := new(bool)
    fmt.Printf("%T\n", a) //*int
    fmt.Printf("%T\n", b) //*bool
    fmt.Println(*a)		//0
    fmt.Println(*b)		//false
}
```

```go
func main(){
    var b *int
    b = new(int)
    *b = 100
    fmt.Println(*b) //100
}
```



### new与make的区别

1. 二者都是用来做内存分配的
2. make只是用于slice、map以及channel的初始化，返回的还是这三个引用类型本身
3. new用于类型的内存分配，并且内存对应的值为类型零值，返回的是指向类型的指针。



# Go语言结构体

Golang中没有“类”的概念，Golang中的结构体和其他语言中的类有点相似。和其他面向对象语言中的类相比，Golang中的结构体具有更高的扩展性和灵活性。

## 自定义类型

在Go语言中有一些基本的数据类型，如string、整型、浮点型、布尔等数据类型，Go语言中可以使用type关键字来自定义类型。

```go
type myInt int
//表示将myInt定义为int类型，通过type关键字的定义，myInt就是一种新的类型，它具有int的特性
```

## 类型别名

Golang1.9版本以后新添加的功能

类型别名规定：TypeAlias只是Type的别名，本质上TypeAlias与Type是同一种类型。

```go
type TypeAlias = Type
```

rune和byte就是类型别名，它们的底层定义如下：

```go
type byte = uint8
type rune = int32
```

```go
type myInt int			//自定义类型

type myFloat = float64	//类型别名

func main() {

	var a myInt = 10
	fmt.Printf("%v %T\n", a, a) //10 main.myInt

	var b myFloat = 12.3
	fmt.Printf("%v %T\n", b, b) //12.3 float64
}
```



## 定义结构体

结构体定义需要使用 type 和 struct 语句。struct 语句定义一个新的数据类型，结构体中有一个或多个成员。type 语句设定了结构体的名称。结构体的格式如下：

```go
type struct_variable_type struct {
   member definition
   member definition
   ...
   member definition
}
```

**结构体要求字段名称必须唯一**

结构体的字段类型可以是：基本数据类型、也可以是切片、Map以及结构体

如果结构体的字段类型是：指针，slice和map的零值都是nil，即还没有分配空间，如果需要使用这样的字段，需要先 make，才能使用。

## 结构体实例化

第一种方法：

```go
variable_name := structure_variable_type {value1, value2...valuen}
//或
variable_name := structure_variable_type { key1: value1, key2: value2..., keyn: valuen}
```

```go
type Books struct {
   title string
   author string
   subject string
   book_id int
}

func main() {

    // 创建一个新的结构体
    fmt.Println(Books{"Go 语言", "www.runoob.com", "Go 语言教程", 6495407})

    // 也可以使用 key => value 格式
    fmt.Println(Books{title: "Go 语言", author: "www.runoob.com", subject: "Go 语言教程", book_id: 6495407})

    // 忽略的字段为 0 或 空
   fmt.Println(Books{title: "Go 语言", author: "www.runoob.com"})
}
//输出
//{Go 语言 www.runoob.com Go 语言教程 6495407}
//{Go 语言 www.runoob.com Go 语言教程 6495407}
//{Go 语言 www.runoob.com  0}
```

第二种方法：

可以通过使用new关键字对结构体进行实例化，得到的是结构体的地址。

```go
type Person struct{
	name string
	age int
	sex string
}

func main() {
	var p1 Person
	p1.name = "张三"
	p1.sex = "男"
	p1.age = 20
	fmt.Printf("值：%#v 类型：%T\n", p1, p1)

	var p2 = new(Person)
	p2.name = "张三"
	p2.age = 20
	p2.sex = "男"
	fmt.Printf("值：%#v 类型：%T\n", *p2, p2)

}
```

**注意：在Golang中支持对结构体指针直接使用`.`来访问结构体的成员。p2.name = "张三" 其实底层是(*p2).name = "张三"**

其他初始化方法：
```go
func main(){
    p3 := &Person{"王五", 20, "男"}
	fmt.Printf("%#v %T\n", p3, p3)//&main.Person{name:"王五", age:20, sex:"男"} *main.Person

	p4 := Person{"王五", 20, "男"}
	fmt.Printf("%#v %T\n", p4, p4)//main.Person{name:"王五", age:20, sex:"男"} main.Person
}
```



## 访问结构体成员

如果要访问结构体成员，需要使用点号 `.` 操作符，格式为：

```go
结构体.成员名
```

## 结构体方法和接收者

Go 语言中同时有函数和方法。一个方法就是一个包含了接受者的函数，接受者可以是命名类型或者结构体类型的一个值或者是一个指针。所有给定类型的方法属于该类型的方法集。

在go语言中，没有类的概念但是可以给类型（结构体，自定义类型）定义方法。所谓方法就是定义了接收者的函数。接收者的概念就类似于其他语言中的this或者self。

方法的定义格式如下：

```go
func (接收者变量 接收者类型) 方法名(参数列表) [返回参数]{
   /* 函数体*/
}
```

其中：

- 接收者变量：接收者中的参数变量名在命名时，官方建议使用接收者类型名的第一个小写字母，而不是self、this之类的命名。例如，Person类型的接收者变量应该命名为p，Connector类型的接收者变量应该命名为c等。
- 接收者类型：接收者类型和参数类似，可以是指针类型和非指针类型。
- 方法名、参数列表、返回参数：具体格式与函数定义相同。

```go
/* 定义结构体 */
type Circle struct {
  radius float64
}

func main() {
  var c1 Circle
  c1.radius = 10.00
  fmt.Println("圆的面积 = ", c1.getArea())
}

//该 method 属于 Circle 类型对象中的方法
func (c Circle) getArea() float64 {
  //c.radius 即为 Circle 类型对象中的属性
  return 3.14 * c.radius * c.radius
}
```



如果需要定义一个方法修改结构体变量内的值，那么该方法的接收者类型需要为**结构体指针类型**

```go
type Person struct{
	name string
	age int
	sex string
}

func (p *Person) setInfo(name string, age int) {
	p.name = name
	p.age = age
}
func main(){
    p5 := new(Person)
	p5.setInfo("czh", 23)
	fmt.Printf("%#v %T\n", p5, p5)
    
    var p6 = Person{name: "abc", age: 1, sex: "男"}
	p6.setInfo("czh", 23)
	fmt.Printf("%#v %T\n", p6, p6)//也可
}
//只要方法的接收者类型是指针类型，那么通过结构体指针变量还是结构体变量调用该方法都可以
```

## 给任意类型添加方法

```go
type myInt int
func (m myInt) SayHello(){
    fmt.Println("Hello world")
}
```

注意事项：非本地类型不能定义方法，也就是说我们不能给别的包的类型定义方法。

## 结构体的匿名字段

结构体允许其成员字段在声明时没有字段名而只有类型，这种没有名字的字段就称为匿名字段。（**定义普通结构体的时候不建议这样写，一般在嵌套结构体中才会使用**）

匿名字段默认采用类型名作为字段名，结构体要求字段名称必须唯一，因此一个结构体中同种类型的匿名字段只能定义一次。

```go
type Person struct{
    string
    int
}

func main(){
    p1 := Person{
        "张三",
        20,
    }
}
```



## 嵌套结构体

```go
type Student struct {
	Name string
	Age int
	Hobby []string
	map1 map[string]string
}

type User struct {
	Username string
	Password string
	//Address Address //表示User结构体嵌套Address结构体
	Address			//嵌套匿名结构体
}

type Address struct {
	Name string
	Phone string
	City string
}

func main() {
    
	var u User
	u.Username = "czh"
	u.Password = "123456"
	u.Address.Name = "张三"
	u.Address.Phone = "123456789"
	u.Address.City = "北京"

	u.City = "上海"	//User中没有City字段，因此也可以直接通过u修改City的值，如果User中有City字段则此代码会修改User中的City
	fmt.Printf("%#v\n", u)
}
```

当访问结构体成员时会先在结构体中查找该字段，找不到再去匿名结构体中查找。

关于嵌套结构体的字段命名冲突：

如果父结构体中有多个子匿名结构体，子匿名结构体中有字段冲突则会报错

```go
type User struct {
	Username string
	Password string
	Address
	Email
}

type Address struct {
	Name string
	Phone string
	City string
	AddTime string // 
}

type Email struct {
	Account string
	AddTime string //
}

func main(){
    vaar u User
    //u.AddTime = "2023-01-05" //报错
    u.Address.AddTime = "2023-01-05"
    u.Email.AddTime = "2023-01-05"
}
```

## 结构体继承

Go语言中使用结构体也可实现其他编程语言中的继承，即通过结构体嵌套。

一个结构体中可以嵌套包含另一个结构体或结构体指针

```go
//父结构体
type Animal struct {
	Name string
}

func (a Animal) run(){
	fmt.Printf("%v 在运动\n", a.Name)
}

//子结构体
type Dog struct {
	Age int
	Animal		//结构体嵌套
    // *Animal	//也可使用结构体指针，但是在初始化值时需要传地址
}

func (d Dog) wang()  {
	fmt.Printf("%v 在汪汪\n", d.Name)
}

func main(){
    var d = Dog{
		Age: 20,
        Animal: Animal{	//如果是*Animal类型则为 Animal: &Animal{}
			Name: "修勾",
		},
	}
	d.run()	//修勾 在运动
	d.wang()	//修勾 在汪汪
}
```

## 结构体和Json相互转换、序列化和反序列化

Golang JSON序列化是指把结构体数据转换成JSON格式的字符串，Golang JSON的反序列化是指把JSON数据转换成Golang中的结构体对象。

Golang中的序列化和反序列化主要通过“encoding/json”包中的json.Marshal()和json.Unmarshal()

要将结构体与json进行转换那么字段首字母必须大写，即公有属性，否则不能被json包访问，就会被忽略

```go
type Stu struct {
	ID int
	Gender string
	Name string
	Sno string
}

func main() {

	var s1 = Stu{
		ID: 12,
		Gender: "男",
		Name: "李四",
		Sno: "s001",
	}

	jsonByte, _ := json.Marshal(s1)
	jsonStr := string(jsonByte)
	fmt.Printf("%v\n", jsonStr)
    //{"ID":12,"Gender":"男","Name":"李四","Sno":"s001"}
    
    str := `{"ID":12,"Gender":"男","Name":"李四","Sno":"s001"}`
	var s2 Stu
	err := json.Unmarshal([]byte(str), &s2)

	if err != nil {
		fmt.Println(err)
	}
	fmt.Printf("%#v\n", s2)
    //main.Stu{ID:12, Gender:"男", Name:"李四", Sno:"s001"}
}
```

如果想要把json串中的key首字母小写，如ID修改为id，则需要使用到结构体标签。

## 结构体标签Tag

Tag是结构体的元信息，可以在运行的时候通过反射的机制读取存出来。Tag在结构体字段的后方定义，由一对`反引号`包裹起来，具体的格式如下：

```go
`key1:"value1" key2:"value2"`
```

结构体tag由一个或多个键值对组成。健与值使用冒号分隔，值用双引号括起来。同一个结构体字段可以设置多个键值对tag，不同的键值对之间使用空格分隔。

**注意事项**：为结构体编写Tag时，必须严格遵守键值对的规则。结构体标签的解析代码的容错能力很差，一旦格式写错，编译和运行时都不会提示任何错误，通过反射也无法正确取值。例如：不要在key和value之间添加空格。

```go
type Stu struct {
	ID int `json:"id"`
	Gender string `json:"gender"`
	Name string `json:"name"`
	Sno string `json:"sno"`
}

func main(){
    var s1 = Stu{
		ID: 12,
		Gender: "男",
		Name: "李四",
		Sno: "s001",
	}
	fmt.Printf("%#v\n", s1)

	jsonByte, _ := json.Marshal(s1)
	jsonStr := string(jsonByte)
	fmt.Printf("%v\n", jsonStr)
    //{"id":12,"gender":"男","name":"李四","sno":"s001"}
}
```

项目中常用

## 结构体作为函数参数

实例：

```go
type Books struct {
   title string
   author string
   subject string
   book_id int
}

func main() {
   var Book1 Books        /* 声明 Book1 为 Books 类型 */
   var Book2 Books        /* 声明 Book2 为 Books 类型 */

   /* book 1 描述 */
   Book1.title = "Go 语言"
   Book1.author = "www.runoob.com"
   Book1.subject = "Go 语言教程"
   Book1.book_id = 6495407

   /* book 2 描述 */
   Book2.title = "Python 教程"
   Book2.author = "www.runoob.com"
   Book2.subject = "Python 语言教程"
   Book2.book_id = 6495700

   /* 打印 Book1 信息 */
   printBook(Book1)

   /* 打印 Book2 信息 */
   printBook(Book2)
}

func printBook( book Books ) {
   fmt.Printf( "Book title : %s\n", book.title)
   fmt.Printf( "Book author : %s\n", book.author)
   fmt.Printf( "Book subject : %s\n", book.subject)
   fmt.Printf( "Book book_id : %d\n", book.book_id)
}
```



## 结构体指针

可以定义指向结构体的指针类似于其他指针变量，格式如下：

```go
var struct_pointer *Books
```

以上定义的指针变量可以存储结构体变量的地址。查看结构体变量地址，可以将 & 符号放置于结构体变量前：

```go
struct_pointer = &Book1
```

使用结构体指针访问结构体成员，使用 "." 操作符：

```go
struct_pointer.title
```

实例：

```go
type Books struct {
   title string
   author string
   subject string
   book_id int
}

func main() {
   var Book1 Books        /* 声明 Book1 为 Books 类型 */
   var Book2 Books        /* 声明 Book2 为 Books 类型 */

   /* book 1 描述 */
   Book1.title = "Go 语言"
   Book1.author = "www.runoob.com"
   Book1.subject = "Go 语言教程"
   Book1.book_id = 6495407

   /* book 2 描述 */
   Book2.title = "Python 教程"
   Book2.author = "www.runoob.com"
   Book2.subject = "Python 语言教程"
   Book2.book_id = 6495700

   /* 打印 Book1 信息 */
   printBook(&Book1)

   /* 打印 Book2 信息 */
   printBook(&Book2)
}
func printBook( book *Books ) {
   fmt.Printf( "Book title : %s\n", book.title)
   fmt.Printf( "Book author : %s\n", book.author)
   fmt.Printf( "Book subject : %s\n", book.subject)
   fmt.Printf( "Book book_id : %d\n", book.book_id)
}
```

# Go中的go mod以及Golang包详解

## Golang中包的介绍和定义

包（package）是多个Go源码的集合，是一种高级的代码复用方案，Go语言为我们提供了很多内置包，如fmt、strings、sort、errors、time、encoding/josn、os、io等。

Golang中的包可以分为三种：1.系统内置包。2.自定义包。3.第三方包。

系统内置包：Golang语言给我们提供的内置包，引入后可以直接使用，如fmt、strconv、strings、sort、errors、time、encoding/json、os、io等

自定义包：开发者自己写的包

第三方包：属于自定义包的一种，需要安装到本地后才可以使用，如“github.com/shopspring/decimal”包解决float精度丢失问题。



## Golang包管理工具

在Golang1.11版本之前如果我们要自定义包的话必须把项目放在GOPATH目录。Go1.11版本之后无需手动配置环境变量，使用go mod管理项目，也不需要非得把项目放到GOPATH指定目录下，你可以在你磁盘的任何位置新建一个项目，Go1.13以后可以彻底不要GOPATH了。



## go mod init初始化项目

实际项目开发中我们首先要在我们项目目录中用go mod命令生成一个go.mod文件管理我们项目的依赖。

```bash
go mod init 项目名
```



## Golang中自定义包

包（package）是多个Go源码的集合，一个包可以简单理解为一个存放多个.go文件的文件夹。该文件夹下面的所有go文件都要在代码的第一行写如下代码，声明该文件归属的包。

```go
package 包名
//必须放在第一行
```

注意事项：

- 一个文件夹下面直接包含的文件只能归属一个package，同样一个package的文件不能在多个文件夹下。
- 包名可以不和文件夹的名字一样，包名不能包含 - 符号。
- 包名为main的包为应用程序的入口包，这种包编译后会得到一个可执行文件，而编译不包含main包的源代码则不会得到可执行文件。

## 自定义包名

在导入包名的时候，我们还可以为导入的包设置别名。通常用于导入的包名太长或者导入的包名冲突的情况。把别名写在导入的包名前面即可。

```go
import T "awesomeProject/tools"
//或者
import (
	T "awesomeProject/tools"
)
```

## 匿名导入包

如果只希望导入包，而不使用包内的数据时，可以使用匿名导入包。具体的格式如下：

```go
import _ "包的路径"
```

匿名导入的包与其他方式导入的包一样都会被编译到可执行文件中。



## Golang中的init()初始化函数

### init()函数介绍

在go语言程序执行时导入包语句会自动触发包内部init()函数的调用。需要注意的是：init()函数没有参数也没有返回值。init()函数在程序运行时自动被调用执行，不能在代码中主动调用它。

包初始化执行的顺序如下图所示：

```go
package main
import "fmt"
var x int8 = 10
const pi = 3.14
func init(){
    fmt.Println(x)
}

func main(){
    fmt.Println("hello")
}
//全局声明 -> init() -> main()
```

### init()函数执行顺序

Go语言包会从main包开始检查其导入的所有包，每个包中又可能导入了其他的包。Go编译器由此构建出一个树状的包引用关系，再根据引用顺序决定编译顺序，依次编译这些包的代码。

在运行时，被最后导入的包会最先初始化并调用其init()函数，如下图所示：

![init函数执行顺序](init函数执行顺序.png)

## Golang中使用第三方包

我们可以在https://pkg.go.dev/查找常见的golang第三方包

1. 找到我们需要下载安装的第三方包的地址

2. 安装这个包

- 第一种方法：go get 包名称 （全局）

```go
go get github.com/shopspring/decimal
```

- 第二种方法：go mod download （全局），依赖包会自动下载到$GOPATH/pkg/mod，多个项目可以共享缓存的mod，注意使用go mod download的时候首先需要在你的项目里面引入第三方包。

```go
go mod download
```

- 第三种方法：go mod vendor 将依赖复制到当前项目的vendor下（本项目）。

```go
go mod vendor
```

​	注意：使用go mod vendor 的时候首先需要在你到项目里面引入第三方包。

3. 看文档使用包

也可以直接使用import导入，运行时会自动下载到GOPATH



# Go语言范围（Range）

Go 语言中 range 关键字用于 for 循环中迭代数组(array)、切片(slice)、通道(channel)或集合(map)的元素。在数组和切片中它返回元素的索引和索引对应的值，在集合中返回 key-value 对。

for 循环的 range 格式可以对 slice、map、数组、字符串等进行迭代循环。格式如下：

```go
for key, value := range oldMap {
    newMap[key] = value
}
```

for 循环的 range 格式可以省略 key 和 value，如下实例：

```go
func main() {
    map1 := make(map[int]float32)
    map1[1] = 1.0
    map1[2] = 2.0
    map1[3] = 3.0
    map1[4] = 4.0
   
    // 读取 key 和 value
    for key, value := range map1 {
      fmt.Printf("key is: %d - value is: %f\n", key, value)
    }

    // 读取 key
    for key := range map1 {
      fmt.Printf("key is: %d\n", key)
    }

    // 读取 value
    for _, value := range map1 {
      fmt.Printf("value is: %f\n", value)
    }
}
//输出：
key is: 4 - value is: 4.000000
key is: 1 - value is: 1.000000
key is: 2 - value is: 2.000000
key is: 3 - value is: 3.000000
key is: 1
key is: 2
key is: 3
key is: 4
value is: 1.000000
value is: 2.000000
value is: 3.000000
value is: 4.000000
```



# Go语言接口（interface）

Go 语言提供了另外一种数据类型即接口，它把所有的具有共性的方法定义在一起，任何其他类型只要实现了这些方法就是实现了这个接口。

Golang中的接口是一种抽象数据类型，Golang中接口定义了对象的行为规范，只定义规范不实现。接口中定义的规范由具体的对象来实现。

通俗的讲，接口就是一个标准，它是对一个对象的行为和规范进行约定。

## Golang接口的定义

​	在Golang中接口（interface）是一种类型，一种抽象类型。接口（interface）是一组函数method的集合，Golang中的接口不能包含任何变量。

​	在Golang中接口中的所有方法都没有方法体，接口定义了一个对象的行为规范，只定义规范不实现。接口体现了程序设计的多态和高内聚低耦合的思想。

​	Golang中的接口也是一种数据类型，不需要显示实现。只需要一个变量含有接口类型中的所有方法，那么这个变量就实现了这个接口。

Golang中每个接口由数个方法组成，接口的定义格式如下：

```go
type 接口名 interface{
    方法名1(参数列表1) 返回值列表1
    方法名2(参数列表2) 返回值列表2
}
```

其中：

- 接口名：使用type将接口定义为自定义的类型名。Go语言的接口在命名时，一般会在单词后面添加er，如有写操作的接口叫Writer，有字符串功能的接口叫Stringer等。接口名最好能突出该接口的类型含义。
- 方法名：当方法名首字母是大写且这个接口类型名首字母也是大写时，这个方法可以被接口所在的包（package）之外的代码访问。
- 参数列表、返回值列表：参数列表和返回值列表中的参数变量名可以忽略。

如果接口里面有方法的话，必须要通过结构体或者通过自定义类型实现这个接口。

实例：
```go
/* 定义接口 */
type interface_name interface {
   method_name1 [return_type]
   method_name2 [return_type]
   method_name3 [return_type]
   ...
   method_namen [return_type]
}

/* 定义结构体 */
type struct_name struct {
   /* variables */
}

/* 实现接口方法 */
func (struct_name_variable struct_name) method_name1() [return_type] {
   /* 方法实现 */
}
...
func (struct_name_variable struct_name) method_namen() [return_type] {
   /* 方法实现*/
}
```

实例：

```go
type Usber interface {
	start()
	stop()
}

type Phone struct {
	Name string
}

func (p Phone) start() {
	fmt.Println(p.Name, "启动")
}

func (p Phone) stop()  {
	fmt.Println(p.Name, "关机")
}

type Camera struct {

}

func (c Camera) start() {
	fmt.Println("相机启动")
}

func (c Camera) stop()  {
	fmt.Println("相机关机")
}


func main() {

	var p = Phone{
		Name: "华为手机",
	}

	var p1 Usber //golang中接口就是一个数据类型
	p1 = p		//表示手机实现Usb接口
	p1.start()

	c := Camera{}
	var c1 Usber = c	//表示相机实现了Usb接口
	c1.start()
}
```

## 空接口

Golang中的接口可以不定义任何方法，没有定义任何方法的接口就是空接口。**空接口表示没有任何约束，因此任何类型变量都可以实现空接口。**

空接口在实际项目中用的是非常多的，用空接口可以表示任意数据类型。

```go
type A interface {}//空接口 表示没有任何约束，任意类型都可以实现空接口

func main() {
	var a A
	var str = "你好golang"
	a = str		//让字符串实现A接口
	fmt.Printf("%v %T\n", a, a)//你好golang string

	var num = 20
	a = num
	fmt.Printf("%v %T\n", a, a)//20 int

	var flag = true
	a = flag
	fmt.Printf("%v %T\n", a, a)//true bool
}
```

golang中空接口也可以直接当作类型使用，可以表示任意类型

```go
func main(){
    var b interface{}
	b = "你好golang"
	fmt.Printf("%v %T\n", b, b) //你好golang string
	b = true
	fmt.Printf("%v %T\n", b, b) //true bool
}
```

### 空接口作为函数的参数

使用空接口实现可以接收任意类型的函数参数。

```go
func show(a interface{})  {
	fmt.Printf("%v %T\n", a, a)
}
func main(){
    show(20)//20 int
	show("你好golang")//你好golang string
	show([]int{1, 2, 3, 4})//[1 2 3 4] []int
}
```

### map的值实现空接口

使用空接口实现可以保存任意值的字典。

```go
func main(){
    var m1 = make(map[string]interface{})
	m1["name"] = "czh"
	m1["age"] = 23
	m1["married"] = false
	for k,v := range m1 {
		fmt.Printf("%v %v, %T\n", k, v, v)
	}
}
//name czh, string
//age 23, int
//married false, bool
```

### slice的值实现空接口

```go
func main(){
    var s1 = []interface{}{1, 2, "你好", true}
	fmt.Println(s1)//[1 2 你好 true]
}
```



## 类型断言

一个接口的值（简称接口值）是由一个具体类型和具体类型的值两部分组成的。这两部分分别称为接口的动态类型和动态值。

如果我们想要判断空接口中值的类型，那么这个时候就可以使用类型断言，其语法格式：

```go
x.(T)
```

其中：

- x：表示类型为interface{}的变量（不一定是空接口，也可以是非空接口）
- T：表示断言x可能是的类型

该语法返回两个参数，第一个参数是x转化为T类型后的变量，第二个参数是一个布尔值，为true则表示断言成功，为false则表示断言失败。

```go
func main() {
    var a interface{}
	a = 20
	v, ok := a.(string)
	if ok {
		fmt.Println("a就是一个string类型，值是：", v)
	} else {
		fmt.Printf("断言失败, %v %T\n", v, v)
	}
}
```

**可以使用x.(type)判断一个变量的类型，这个语句只能用在switch语句里面**

```go
func Print(x interface{}){
    switch x.(type){
        case int:
        fmt.Println("int类型")
        case string:
        fmt.Println("string类型")
        case bool:
        fmt.Println("bool类型")
        default:
        fmt.Println("类型错误")
    }
}
```



## 结构体接收者和指针接收者实现接口的区别

值接收者：

- 如果结构体中的方法是值接收者，那么实例化后的结构体值类型和结构体指针类型都可以赋值给接口变量，如下所示：

```go
type Usber interface {
	start()
	stop()
}

type Phone struct {
	Name string
}

func (p Phone) start() {
	fmt.Println(p.Name, "启动")
}

func (p Phone) stop()  {
	fmt.Println(p.Name, "关机")
}

func main()  {

	var p1 = Phone{
		Name: "hw",
	}

	var p2 Usber = p1	//结构体变量可以赋值给接口变量
	p2.start()

	var p3 = &Phone{
		Name: "mi",
	}

	var p4 Usber = p3 //结构体指针变量可以赋值给接口变量
	p4.start()

}
```

指针接收者：

- 如果结构体中的方法是指针接收者，那么实例化后的结构体只有结构体指针类型可以赋值给接口变量

```go
type Usber interface {
	start()
	stop()
}

type Phone struct {
	Name string
}

func (p *Phone) start() {	//修改为指针接收者
	fmt.Println(p.Name, "启动")
}

func (p *Phone) stop()  {
	fmt.Println(p.Name, "关机")
}

func main()  {

	var p1 = Phone{
		Name: "hw",
	}

	//var p2 Usber = p1 //会报错
	//p2.start()
    
    var p3 = &Phone{
		Name: "mi",
	}

	var p4 Usber = p3
	p4.start()
}
```

```go
type Animaler interface {
	SetName(string)
	GetName() string
}

type Dog1 struct {
	Name string
}

func (d *Dog1) SetName(name string)  {
	d.Name = name
}

func (d *Dog1) GetName() string {
	return d.Name
}

type Cat struct {
	Name string
}

func (c *Cat) SetName(name string)  {
	c.Name = name
}

func (c *Cat) GetName() string {
	return c.Name
}

func main() {
	//Dog实现Animal的接口
	d := &Dog1{Name: "d"}
	var a Animaler = d
	fmt.Println(a.GetName())
	a.SetName("dd")
	fmt.Println(a.GetName())

	c := &Cat{Name: "c"}
	a = c
	fmt.Println(a.GetName())
	a.SetName("cc")
	fmt.Println(a.GetName())

}
```



## 一个结构体实现多个接口

例子

```go
type Animaler1 interface {
	SetName(string)
}

type Animaler2 interface {
	GetName() string
}

type Dog1 struct {
	Name string
}

func (d *Dog1) SetName(name string)  {
	d.Name = name
}

func (d *Dog1) GetName() string {
	return d.Name
}

func main() {
	d := &Dog1{Name: "d"}
	var d1 Animaler1 = d	//表示让Dog实现Animaler1接口
	var d2 Animaler2 = d	//表示让Dog实现Animaler2接口
	d1.SetName("dd")
	fmt.Println(d2.GetName())
}
```



## 接口嵌套

接口与接口间可以通过嵌套创造出新的接口

```go
type AInterface interface {
	SetName(string)
}

type BInterface interface {
	GetName() string
}

type Animaler interface {
	AInterface
	BInterface
}
```

实现Animaler接口需要实现内部接口的所有方法

## Golang中空接口和类型断言使用细节

```go
func main() {
	var userinfo = make(map[string]interface{})
	userinfo["username"] = "张三"
	userinfo["age"] = 20
	userinfo["hobby"] = []string{"睡觉", "吃饭"}
	fmt.Println(userinfo["username"])
	//fmt.Println(userinfo["hobby"][0]) //报错
    //空接口类型不支持索引
    
    //可以通过类型断言获取
    hobby, ok := userinfo["hobby"].([]string)
	if ok {
		fmt.Println(hobby[0])
	}
}
```



# Go错误处理

Go 语言通过内置的错误接口提供了非常简单的错误处理机制。

error类型是一个接口类型，这是它的定义：

```go
type error interface {
    Error() string
}
```

可以在编码中通过实现 error 接口类型来生成错误信息。

函数通常在最后的返回值中返回错误信息。使用errors.New 可返回一个错误信息：

```go
func Sqrt(f float64) (float64, error) {
    if f < 0 {
        return 0, errors.New("math: square root of negative number")
    }
    return math.Sqrt(f), nil
}
```



# Go并发

## 并发和并行

并发：多个线程同时竞争一个位置，竞争到的才可以执行，每个时间段只有一个线程在执行。

并行：多个线程可以同时执行，每个时间段可以有多个线程同时执行。

通俗地讲：多线程程序在单核CPU上面运行就是**并发**，多线程程序在多核CPU上运行就是**并行**，如果线程数大于CPU核数，则多线程程序在多个CPU上面运行既有并行又有并发。

## Golang中的协程（goroutine）以及主线程

**golang中的主线程**：在一个Golang程序的主线程上可以起**多个协程**。Golang中多协程可以实现并行或者并发。

**协程**：可以理解为用户级线程，这是对内核透明的，也就是系统并不知道协程的存在，是完全由用户自己的程序进行调度的。Golang的一大特色就是从语言层面原生支持协程，在函数或者方法前面加go关键字就可以创建一个协程。可以说Golang中的协程就是goroutine。

**多协程和多线程**：Golang中每个goroutine（协程）默认占用内存远比Java、C的线程少。OS线程（操作系统线程）一般都有固定的栈内存（通常为2MB左右），一个goroutine（协程）占用内存非常小，只有2KB左右，多协程切换调度开销方面远比线程要少。



Go 语言支持并发，我们只需要通过 go 关键字来开启 goroutine 即可。

goroutine 是轻量级线程，goroutine 的调度是由 Golang 运行时进行管理的。

goroutine 语法格式：

```go
go 函数名(参数列表)
//例如
go f(x,y,z)
```

Go 允许使用 go 语句开启一个新的运行期线程， 即 goroutine，以一个不同的、新创建的 goroutine 来执行一个函数。 同一个程序中的所有 goroutine 共享同一个地址空间。

```go
func say(s string) {
        for i := 0; i < 5; i++ {
                time.Sleep(100 * time.Millisecond)
                fmt.Println(s)
        }
}

func main() {
        go say("world")
        say("hello")
}
```

注意：主协程代码执行完毕会直接退出程序，不会等子协程执行结束

sync.WaitGroup可以实现主线程等待协程执行完毕。

举例：

```go
func printHello1(){
	for i := 0; i < 10; i++ {
		fmt.Println("1 -- 你好golang-", i)
		time.Sleep(time.Millisecond * 100)
	}
	wg.Done() //协程计数器减1
}
func printHello2(){
    defer wg.Done()
	for i := 0; i < 10; i++ {
		fmt.Println("2 -- 你好golang-", i)
		time.Sleep(time.Millisecond * 100)
	}
}

var wg sync.WaitGroup

func main(){
	wg.Add(1) //协程计数器加1
	go printHello1()
	wg.Add(1)
	go printHello2()
	
	wg.Wait()
	fmt.Println("主线程退出")
}
```



统计200000内素数举例：

```go
/*
1协程 统计 1-50000
2协程 统计 50001-100000
3协程 统计 100001-150000
4协程 统计 150001-200000
		(n-1)*50000 + 1, n*50000
*/

//输出素数
func suShu(n int){
	defer wg.Done()
	start := time.Now().Unix()
	for num := (n - 1) * 50000 + 1; num < n * 50000; num++ {
        if num > 1 {
			var flag = true
			for i := 2; i < num; i++ {
				if num%i == 0 {
					flag = false
					break
				}
			}
			if flag {
				//fmt.Println(num, "是素数")
			}
		}
	}
	end := time.Now().Unix()
	fmt.Println(end - start, "s")
}

func main() {
	start := time.Now().Unix()
	for i := 0; i < 4; i++ {
		wg.Add(1)
		go suShu(i)
	}
	wg.Wait()
	end := time.Now().Unix()
	fmt.Printf("程序执行完成共耗时 %v s", end - start)
}
```

结果：单协程执行耗时4s，4协程执行耗时1s

## 设置Golang并行运行的时候占用的cpu数量

Go运行时的调度器使用GOMAXPROCS参数来确定需要使用多少个OS线程来同时执行Go代码。默认值是机器上的CPU核心数。例如在一个8核心的机器上，调度器会把Go代码同时调度到8个OS线程上。

Go语言中可以通过runtime.GOMAXPROCS()函数设置当前程序并发时占用的CPU逻辑核心数。可以通过runtime.NumCPU()获取当前计算机上的CPU逻辑核心数。

Go1.5版本之前，默认使用的是单核心执行。Go1.5版本之后，默认使用全部的CPU逻辑核心数。

## 通道（channel）

通道是Go语言在语言级别上提供的goroutine间的通讯方式，我们可以使用channel在多个goroutine之间传递消息。如果说goroutine是Go程序并发的执行体，，channel就是它们之间的连接。channel是可以让一个goroutine发送特定值到另一个goroutine的通信机制。

Go语言的并发模型是CSP（Communicating Sequential Processes），提倡**通过通信共享内存**而不是**通过共享内存而实现通信**。

Go语言中的通道（channel）是一种特殊的类型。通道像一个传送带或者队列，总是遵循**先入先出（First In First Out）**的规则，保证收发数据的顺序。每一个通道都是一个具体类型的导管，也就是声明channel的时候需要为其指定元素类型。

channel是一种类型，一种**引用类型**

### 声明channel

```go
var 变量 chan 元素类型
var ch1 chan int	//声明一个传递整型的通道
var ch2 chan bool	//声明一个传递布尔类型的通道
var ch3 chan []int	//声明一个传递int切片类型的通道
```

### 创建channel

声明的通道需要使用make函数初始化之后才能使用。

创建channel的格式如下：

```go
make(chan 元素类型, 容量)
```

```go
//创建一个能存储10个int类型数据的通道
ch1 := make(chan int, 10)
```



通道可用于两个 goroutine 之间通过传递一个指定类型的值来同步运行和通讯。操作符 `<-` 用于指定通道的方向，发送或接收。如果未指定方向，则为双向通道。

```go
ch <- v    // 把 v 发送到通道 ch
v := <-ch  // 从 ch 接收数据
           // 并把值赋给 v
<-ch		//从ch中接收值，忽略结果
```

**注意**：默认情况下，通道是不带缓冲区的。发送端发送数据，同时必须有接收端相应的接收数据。

```go
func sum(s []int, c chan int) {
        sum := 0
        for _, v := range s {
                sum += v
        }
        c <- sum // 把 sum 发送到通道 c
}

func main() {
        s := []int{7, 2, 8, -9, 4, 0}

        c := make(chan int)
        go sum(s[:len(s)/2], c)
        go sum(s[len(s)/2:], c)
        x, y := <-c, <-c // 从通道 c 中接收

        fmt.Println(x, y, x+y) // -5 17 12
}
```

## 通道缓冲区

通道可以设置缓冲区，通过 make 的第二个参数指定缓冲区大小：

 ```go
 ch := make(chan int, 100)
 ```

带缓冲区的通道允许发送端的数据发送和接收端的数据获取处于异步状态，就是说发送端发送的数据可以放在缓冲区里面，可以等待接收端去获取数据，而不是立刻需要接收端去获取数据。

不过由于缓冲区的大小是有限的，所以还是必须有接收端来接收数据的，否则缓冲区一满，数据发送端就无法再发送数据了。

**注意**：如果通道不带缓冲，发送方会阻塞直到接收方从通道中接收了值。如果通道带缓冲，发送方则会阻塞直到发送的值被拷贝到缓冲区内；如果缓冲区已满，则意味着需要等待直到某个接收方获取到一个值。接收方在有值可以接收之前会一直阻塞。

```go
func main() {
    // 这里我们定义了一个可以存储整数类型的带缓冲通道
        // 缓冲区大小为2
        ch := make(chan int, 2)

        // 因为 ch 是带缓冲的通道，我们可以同时发送两个数据
        // 而不用立刻需要去同步读取数据
        ch <- 1
        ch <- 2

        // 获取这两个数据
        fmt.Println(<-ch)//1
        fmt.Println(<-ch)//2
}
```

## Go遍历通道与关闭通道

Go 通过 range 关键字来实现遍历读取到的数据，类似于与数组或切片。格式如下：

```go
v, ok := <-ch
```

如果通道接收不到数据后 ok 就为 false，这时通道就可以使用 **close()** 函数来关闭。关闭通道的操作只由发送方执行。

```go
func fibonacci(n int, c chan int) {
        x, y := 0, 1
        for i := 0; i < n; i++ {
                c <- x
                x, y = y, x+y
        }
        close(c)
}

func main() {
        c := make(chan int, 10)
        go fibonacci(cap(c), c)
        // range 函数遍历每个从通道接收到的数据，因为 c 在发送完 10 个
        // 数据之后就关闭了通道，所以这里我们 range 函数在接收到 10 个数据
        // 之后就结束了。如果上面的 c 通道不关闭，那么 range 函数就不
        // 会结束，从而在接收第 11 个数据的时候就阻塞了。
        for i := range c {
                fmt.Println(i)
        }
}
```

goroutine与channel结合使用案例：

```go
//写数据
func fun1(ch chan<- int){
	defer wg.Done()
	for i := 0; i < 10; i++ {
		ch <- i
		fmt.Printf("【写入】数据%v成功\n", i)
		time.Sleep(time.Millisecond * 500)
	}
	close(ch)
}

//读数据
func fun2(ch <-chan int){
	defer wg.Done()
	for v := range ch {
		fmt.Printf("【读取】数据%v成功\n", v)
		time.Sleep(time.Millisecond * 10)
	}
}

func main() {
	ch := make(chan int, 10)
	wg.Add(1)
	go fun1(ch)
	wg.Add(1)
	go fun2(ch)

	wg.Wait()
	fmt.Println("退出...")
}
```

## 单向通道

Go语言的类型系统提供了单方向的 channel 类型，顾名思义，单向 channel 就是只能用于写入或者只能用于读取数据。当然 channel 本身必然是同时支持读写的，否则根本没法用。

假如一个 channel 真的只能读取数据，那么它肯定只会是空的，因为你没机会往里面写数据。同理，如果一个 channel 只允许写入数据，即使写进去了，也没有丝毫意义，因为没有办法读取到里面的数据。所谓的单向 channel 概念，其实只是对 channel 的一种使用限制。

有的时候我们会将通道作为参数在多个任务函数间传递，很多时候我们在不同的任务函数中使用通道都会对其进行限制，比如限制通道在函数中只能发送或只能接收。

```go
var 通道实例 chan<- 元素类型    // 只能写入数据的通道
var 通道实例 <-chan 元素类型    // 只能读取数据的通道

ch1 := make(chan<- int, 2)	//声明通道为只写
ch2 := make(<-chan int, 2)	//声明通道为只读
```

## select多路复用

在某些场景下我们需要同时从多个通道接收数据。这个时候就可以用到golang中给我们提供的select多路复用。

通常情况通道在接收数据时，如果没有数据可以接收将会发生阻塞。

select 是 Go 中的一个控制结构，类似于 switch 语句。

select 语句只能用于通道操作，每个 case 必须是一个通道操作，要么是发送要么是接收。

select 语句会监听所有指定的通道上的操作，一旦其中一个通道准备好就会执行相应的代码块。

如果多个通道都准备好，那么 select 语句会随机选择一个通道执行。如果所有通道都没有准备好，那么执行 default 块中的代码。

go语言中select语句的语法如下：

```go
select {
  case <- channel1:
    // 执行的代码
  case value := <- channel2:
    // 执行的代码
  case channel3 <- value:
    // 执行的代码

    // 你可以定义任意数量的 case

  default:
    // 所有通道都没有准备好，执行的代码
}
```

以下描述了 select 语句的语法：

- 每个 case 都必须是一个通道

- 所有 channel 表达式都会被求值

- 所有被发送的表达式都会被求值

- 如果任意某个通道可以进行，它就执行，其他被忽略。

- 如果有多个 case 都可以运行，select 会随机公平地选出一个执行，其他不会执行。

  否则：

  1. 如果有 default 子句，则执行该语句。
  2. 如果没有 default 子句，select 将阻塞，直到某个通道可以运行；Go 不会重新对 channel 或值进行求值。

```go
func main() {

	intChan := make(chan int, 10)
	for i := 0; i < 10; i++ {
		intChan <- i
	}
	//close(intChan)//无需关闭，关闭会导致死循环

	stringChan := make(chan string, 5)
	for i := 0; i < 5; i++ {
		stringChan <- "hello_" + strconv.FormatInt(int64(i), 10)
	}

	//使用select来获取channel里面的数据的时候不需要关闭channel
	Loop:
	for {
		select {
		case v := <-intChan:
			fmt.Printf("从 intChan 读取的数据%d\n", v)
		case v := <-stringChan:
			fmt.Printf("从 stringChan 读取的数据%s\n", v)
		default:
			fmt.Printf("数据获取完毕\n")
			break Loop //使用goto退出也可
		}
	}
	fmt.Println("程序执行完毕")
}
```

**使用select来获取channel里面的数据的时候不需要关闭channel**

## goroutine Recover解决协程中出现的panic

通常一个协程报错会导致其他协程停止执行

实现一个协程报错不影响其他协程执行

```go
func sayHello(){
	defer wg.Done()
	for i := 0; i < 10; i++ {
		time.Sleep(time.Millisecond * 50)
		fmt.Println("hello world")
	}
}

func test2() {
	defer wg.Done()
	defer func() {
		if err := recover(); err != any(nil) {
			fmt.Println("test()发生错误", err)
		}
	}()
	var myMap map[int]string

	myMap[0] = "golang"//error
	fmt.Printf("这行代码不会执行\n")
}

func main() {
	wg.Add(1)
	go sayHello()
	wg.Add(1)
	go test2()

	wg.Wait()
}
```

## 并发安全和锁

### 互斥锁

互斥锁是传统并发编程中对共享资源进行访问控制的主要手段，它由标准库sync中的Mutex结构体类型表示。sync.Mutex类型只有两个公开的指针方法，Lock和Unlock。Lock锁定当前的共享资源，Unlook进行解锁。

### 读写互斥锁

Go中的读写锁由结构体类型sync.RWMutex表示。



# Go reflect（反射）

## 反射引言

有时需要写一个函数，这个函数有能力统一处理各种值类型，而这些类型可能无法共享同一个接口，也可能布局未知，也有可能这个类型在我们设计函数时还不存在，这个时候我们就可以用到反射。

1. 空接口可以存储任意类型的变量，那么如何知道这个空接口保存数据的类型是是什么？
   1. 可以使用断言
   2. 可以使用反射，也就是在程序运行时动态地获取一个变量的类型信息和值信息。

## 反射的基本介绍

反射是指在程序运行期间对程序本身进行访问和修改的能力。正常情况程序在编译时，变量被转换为内存地址，变量名不会被编译器写入到可执行部分。在运行程序时，程序无法获取自身的信息。支持反射的语言可以在程序编译期将变量的反射信息，如字段名称、类型信息、结构体信息等整合到可执行文件中，并给程序提供接口访问反射信息，这样就可以在程序运行期获取类型的反射信息，并且有能力修改它们。

### Go可以实现的功能：

1. 反射可以在程序运行期间动态获取变量的各种信息，比如变量的类型、类别
2. 如果是结构体，通过反射还可以获得结构体本身的信息，比如结构体的字段、结构体的方法。
3. 通过反射，可以修改变量的值，可以调用关联的方法。

### Go语言中的变量是分为两部分的：

- 类型信息：预先定义好的元信息。
- 值信息：程序运行过程中可动态变化的。

在Go语言的反射机制中，任何接口值都是一个**具体类型**和**具体类型的值**两部分组成的。

在Go语言中反射的相关功能由内置的reflect包提供，任意接口值在反射中都可以理解为由reflect.Type和reflect.Value两部分组成，并且reflect包提供了reflect.TypeOf和reflect.ValueOf两个重要函数来获取任意对象的Value和Type。

## reflect.TypeOf()获取任意值的类型对象

在Go语言中，使用reflect.TypeOf()函数可以接受任意interface{}参数，可以获得任意值的类型对象（reflect.Type），程序通过类型对象可以访问任意值的类型信息

### type Name和type Kind

**在反射中关于类型还划分为两种：**类型（type）和种类（kind）。因为在Go语言中我们可以使用type关键字构造很多自定义类型，而**种类**（Kind）就是指底层的类型，但在反射中，当需要区分指针、结构体等大品种的种类时，就会用到种类（Kind）。

数组、切片、Map、指针类型的变量，它们的.Name()都是返回空

```go
type myInt2 int
type Person2 struct {
	Name string
	Age int
}

func reflectFn(x interface{})  {
	v := reflect.TypeOf(x)

	fmt.Printf("类型:%v 类型名称:%v 类型种类:%v\n", v, v.Name(), v.Kind())
}

func main(){
	a := 10
	b := 23.4
	c := true
	d := "你好go"
	reflectFn(a)//int
	reflectFn(b)//float64
	reflectFn(c)//bool
	reflectFn(d)//string

	var e myInt2 = 34
	var f = Person2{
		Name: "张三",
		Age:  20,
	}
	reflectFn(e)//main.myint
	reflectFn(f)//main.Person

	var h = 25
	reflectFn(&h)
    
    var i = [...]int{1,2,3}
	var j = []int{11,22,33}
	reflectFn(i)
	reflectFn(j)
}
//类型:int 类型名称:int 类型种类:int
//类型:float64 类型名称:float64 类型种类:float64
//类型:bool 类型名称:bool 类型种类:bool
//类型:string 类型名称:string 类型种类:string
//类型:main.myInt2 类型名称:myInt2 类型种类:int
//类型:main.Person2 类型名称:Person2 类型种类:struct
//类型:*int 类型名称: 类型种类:ptr
//类型:[3]int 类型名称: 类型种类:array
//类型:[]int 类型名称: 类型种类:slice
```

种类（Kind）指的是对象归属的品种，在 reflect 包中有如下定义：

```go
type Kind uint
const (
    Invalid Kind = iota  // 非法类型
    Bool                 // 布尔型
    Int                  // 有符号整型
    Int8                 // 有符号8位整型
    Int16                // 有符号16位整型
    Int32                // 有符号32位整型
    Int64                // 有符号64位整型
    Uint                 // 无符号整型
    Uint8                // 无符号8位整型
    Uint16               // 无符号16位整型
    Uint32               // 无符号32位整型
    Uint64               // 无符号64位整型
    Uintptr              // 指针
    Float32              // 单精度浮点数
    Float64              // 双精度浮点数
    Complex64            // 64位复数类型
    Complex128           // 128位复数类型
    Array                // 数组
    Chan                 // 通道
    Func                 // 函数
    Interface            // 接口
    Map                  // 映射
    Ptr                  // 指针
    Slice                // 切片
    String               // 字符串
    Struct               // 结构体
    UnsafePointer        // 底层指针
)
```

## reflect.ValueOf()

reflect.ValueOf()返回的是reflect.Value类型，其中包含了原始值的值信息。reflect.Value与原始值之间可以相互转换。

```go
func reflectValue(x interface{})  {
	v := reflect.ValueOf(x)
	fmt.Println(v)

	var n = v.Int() + 12	//v.Int()获取原始值
	fmt.Println(n)
}

func main(){
    var a = 23
	reflectValue(a)
}
```

reflect.Value类型提供的获取原始值的方法如下：

| 方法                    | 说明                                                         |
| ----------------------- | ------------------------------------------------------------ |
| Interface() interface{} | 将以interface{}类型返回，可以通过类型断言转换为指定类型      |
| Int() int64             | 将值以int类型返回，所有的有符号整型均可以此方式返回          |
| Uint() uint64           | 将值以uint类型返回，所有无符号整型均可以此方式返回           |
| Float() float64         | 将值以双精度（float64）类型返回，所有浮点数（float32、float64）均可以此方式返回 |
| Bool() bool             | 将值以bool类型返回                                           |
| Bytes() []bytes         | 将值以字节数组[]bytes类型返回                                |
| String() string         | 将值以此字符串类型返回                                       |

## 通过反射设置变量的值

想要在函数中通过反射修改变量的值，需要注意函数参数传递的是值拷贝，必须传递变量地址才能修改变量值。而反射中使用专有的Elem()方法来获取指针对应的值。

```go
func reflectValue1(x interface{}){
	v := reflect.ValueOf(x)
	fmt.Println(v.Kind())
	fmt.Println(v.Elem().Kind())
	if v.Elem().Kind() == reflect.Int64 {
		v.Elem().SetInt(120)
	} else if v.Elem().Kind() == reflect.String {
		v.Elem().SetString("你好Go")
	}
}

func main(){
    var a int64 = 100
	reflectValue1(&a)
	fmt.Println(a)

	var b string = "你好golang"
	reflectValue1(&b)
	fmt.Println(b)
}
```

## 结构体反射

### 与结构体相关的方法

任意值通过reflect.TypeOf()获得反射对象信息后，如果它的类型是结构体，可以通过反射值对象（reflect.Type）的NumField()和Field()方法获得结构体成员的详细信息。

reflect.Type中与获取结构体成员相关的方法如下表所示。

| 方法                                         | 说明                                                         |
| -------------------------------------------- | ------------------------------------------------------------ |
| Field(i int) StructField                     | 根据索引，返回索引对应的结构体字段的信息。                   |
| NumField() int                               | 返回结构体成员字段数量。                                     |
| FieldByName(name string) (StructField, bool) | 根据给定字符串返回字符串对应的结构体字段的信息。             |
| FieldByIndex(index []int) StructField        | 多层成员访问时，根据[]int提供的每个结构体的字段索引，返回字段的信息 |

### 获取结构体属性名称、类型和值等信息/修改属性值

```go
type Students struct {
	Name string `json:"name"`
	Age int `json:"age"`
	Score int `json:"score"`
}

func (s Students)GetInfo() string {
	var str = fmt.Sprintf("姓名:%v 年龄:%v 成绩:%v\n", s.Name, s.Age, s.Score)
	return str
}

func (s *Students)SetInfo(name string, age int, score int)  {
	s.Name = name
	s.Age = age
	s.Score = score
}

func printStructField(s interface{})  {

	//判断参数是不是结构体类型
	t := reflect.TypeOf(s)
	v := reflect.ValueOf(s)
	if t.Kind() != reflect.Struct && t.Elem().Kind() != reflect.Struct {
		fmt.Println("传入的参数不是结构体")
		return
	}

	//1.通过类型变量里面的Field可以获取结构体的字段
	field0 := t.Elem().Field(0)//参数传的结构体指针用这个
	//field0 := t.Field(0)//参数传的结构体变量用这个
	fmt.Printf("%#v, %T\n", field0, field0)
	fmt.Println("字段名称：", field0.Name)
	fmt.Println("字段类型：", field0.Type)
	fmt.Println("字段Tag：", field0.Tag.Get("json"))

	//2.通过类型变量里面的FieldByName可以获取结构体的字段
	field1, ok := t.Elem().FieldByName("Age")
	if ok {
		fmt.Println("字段名称：", field1.Name)
		fmt.Println("字段类型：", field1.Type)
		fmt.Println("字段Tag：", field1.Tag.Get("json"))
	}

	//3.通过类型变量里面的NumField获取该结构体有几个字段
	fmt.Println("结构体有 ", t.Elem().NumField(), " 个属性")

	//4.获取结构体属性对应的值
	fmt.Println(v.Elem().FieldByName("Name"))

	fmt.Println("-------------")
	for i := 0; i < t.Elem().NumField(); i++ {
		fmt.Printf("属性名称：%v 属性值：%v 属性类型：%v 属性Tag：%v\n", t.Elem().Field(i).Name, v.Elem().Field(i), t.Elem().Field(i).Type, t.Elem().Field(i).Tag)
	}
}

func main() {

	stu1 := Students{
		Name:  "小明",
		Age:   15,
		Score: 98,
	}
	printStructField(&stu1)
}
```

### 获取结构体方法并调用

```go
func printStructFn(s interface{})  {

	t := reflect.TypeOf(s)
	v := reflect.ValueOf(s)
	if t.Kind() != reflect.Struct && t.Elem().Kind() != reflect.Struct {
		fmt.Println("传入的参数不是结构体")
		return
	}

	//1.通过类型变量里面的Method可以获取结构体的方法
	method0 := t.Method(0)	//和结构体方法的顺序没有关系，与结构体方法的ASCII有关系
	fmt.Println(method0.Name)
	fmt.Println(method0.Type)

	method1, ok := t.MethodByName("GetInfo")
	if ok {
		fmt.Println(method1.Name)
		fmt.Println(method1.Type)
	}

	//2.通过类型变量获取这个结构体有多少个方法
	fmt.Println("方法数量：", t.NumMethod())

	//3.通过《值变量》执行方法（注意需要使用值变量，并且要注意参数） v.Method(0).call(nil)
	info := v.MethodByName("GetInfo").Call(nil)
	fmt.Println(info)

	//4.执行方法传入参数（注意需要使用《值变量》，并且要注意参数，接收的参数是[]reflect.Value的切片）
	var params []reflect.Value
	params = append(params, reflect.ValueOf("李四"))
	params = append(params, reflect.ValueOf(20))
	params = append(params, reflect.ValueOf(99))

	v.MethodByName("SetInfo").Call(params)
	info = v.MethodByName("GetInfo").Call(nil)
	fmt.Println(info)

}

//反射修改结构体值
func reflectChangeStruct(s interface{}){
	t := reflect.TypeOf(s)
	v := reflect.ValueOf(s)
	if t.Kind() != reflect.Ptr {
		fmt.Println("传入的不是指针类型")
		return
	} else if t.Elem().Kind() != reflect.Struct {
		fmt.Println("传入的不是结构体指针类型")
		return
	}

	v.Elem().FieldByName("Name").SetString("czh")
}
func main(){
    stu1 := Students{
		Name:  "小明",
		Age:   15,
		Score: 98,
	}
	printStructFn(&stu1)
}
```

## 不要乱用反射

反射是一个强大并富有表现力的工具，能让我们写出更灵活的代码，但是反射不应该被滥用，原因：

1. 基于反射的代码是极其脆弱的，反射中的类型错误会在真正运行的时候才会引发panic。
2. 大量使用反射的代码通常难以理解。

# Golang文件和目录操作

## 读取文件

方法一：

1. ***<u>只读方式打开文件</u>*** file, err := os.Open()

2. 读取文件 file.Read()

3. 关闭文件流 defer file.Close()

方法二：bufio读取文件

1. 只读方式打开文件 file, err := os.Open()
2. 创建reader对象 reader := bufio.NewReader(file)
3. ReadString读取文件 line, err := reader.ReadString('\n')
4. 关闭文件流  defer file.Close()

方法三：ioutil读取文件，打开关闭文件的方法它都封装好了只需要一句话就可以读取

ioutil.ReadFile("./main.go")

（ioutil从1.16起被废弃，改为os.ReadFile()）



方法一实例：

```go
//方法一
func main() {
	//1.打开文件
	file, err := os.Open("./file.go")
	defer file.Close()

	if err != nil {
		fmt.Println(err)
		return
	}

	//2.读取文件内容
	var strSlice []byte
	var tempSlice = make([]byte, 128)
	for {
		n, err := file.Read(tempSlice)
		if err == io.EOF {
			fmt.Println("读取完毕")
			break
		}
		if err != nil {
			fmt.Println("读取失败")
			return
		}
		fmt.Printf("读取到了%v个字节\n", n)
		strSlice = append(strSlice, tempSlice[:n]...)
	}
	fmt.Println(string(strSlice))

}
```

方法二实例：

```go
func method2() {
	file, err := os.Open("./file.go")
	defer file.Close()

	if err != nil {
		fmt.Println(err)
		return
	}

	//bufio
	var fileStr string
	reader := bufio.NewReader(file)
	for {
		line, err := reader.ReadString('\n')
		if err == io.EOF {
			//fileStr += line
			break
		}
		if err != nil {
			fmt.Println(err)
			return
		}
		fileStr += line
	}

	fmt.Println(fileStr)
}
```

方法三实例：

```go
func method3() {
	byteStr, err := os.ReadFile("./file.go")
	if err != nil {
		fmt.Println(err)
		return
	}
	fmt.Println(string(byteStr))
}
```



## 写入文件

方法一：

1. 打开文件 file, err := os.OpenFile("./file.go", "os.O_CREATE|os.O_RDWR", 0666)
2. 写入文件
   - file.Write([]byte(str))		//写入字节切片数据
   - file.WriteString("直接写入字符串数据")    //直接写入字符串数据
3. 关闭文件流 file.Close

```go
func OpenFile(name string, flag int, perm FileMode) (*File, error){
    ...
}
```

- name:要打开的文件名
- flag:打开的文件的模式

模式有以下几种：

| 模式        | 含义     |
| ----------- | -------- |
| os.O_WRONLY | 只写     |
| os.O_CREATE | 创建文件 |
| os.O_RDONLY | 只读     |
| os.O_RDWR   | 读写     |
| os.O_TRUNC  | 清空     |
| os.O_APPEND | 追加     |

perm：文件权限，一个八进制数。r（读）04，w（写）02，x（执行）01。



方法二：bufio写入文件

1. 打开文件 file, err := os.OpenFile("./file.go", os.O_CREATE|os.O_RDWR, 0666)
2. 创建writer对象 writer := bufio.NewWriter(file)
3. 将数据先写入缓存 writer.WriteString("你好golang\r\n")

4. 将缓存中的内容写入文件 writer.Flush()
5. 关闭文件流 file.Close()

方法三：ioutil写入文件（无法追加数据）

str := "hello golang"

err := ioutil.WriteFile("./test.txt", []byte(str), 0666)

（ioutil从1.16起被废弃，改为os.WriteFile()）



方法一实例：

```go
func method11()  {
	file, err := os.OpenFile("./test.txt", os.O_CREATE|os.O_RDWR|os.O_APPEND, 0666)

	defer file.Close()

	if err != nil {
		fmt.Println(err)
		return
	}

	for i := 0; i < 10; i++ {
		file.WriteString("直接写入字符串"+ strconv.FormatInt(int64(i), 10) +"\r\n")
	}

	var str = "通过file.Write()直接写入"
	file.Write([]byte(str))

}
```



方法二实例：

```go
func method22() {
	file, err := os.OpenFile("./test.txt", os.O_CREATE|os.O_RDWR|os.O_APPEND, 0666)
	defer file.Close()
	if err != nil {
		fmt.Println(err)
		return
	}

	writer := bufio.NewWriter(file)
	writer.WriteString("通过bufio写入\r\n")
	writer.Flush()
}
```

方法三实例：

```go
func method33() {
	str := "hello golang"
	err := os.WriteFile("./test.txt", []byte(str), 0666)
	if err != nil {
		fmt.Println(err)
		return
	}
}
```



## 文件重命名

```go
err := os.Rename("./test.txt", "./test1.txt")
```

只能同盘操作

## 复制文件

方法一：os.ReadFile()方法先读出来，再用os.WriteFile()写入

```go
input, err := os.ReadFile(srcFileName)
err = os.WriteFile(dstFileName, input, 0644)
```

方法二：

```go
source,_ := os.Open(srcFileName)
destination,_ := os.OpenFile(dstFileName, os.O_CREATE|os.O_WRONLY, 0666)

n, err := source.Read(buf)
destination.Write(buf[:n])
```



```go
func CopyFile(srcFile string, dstFile string) error {
	sFile, err1 := os.Open(srcFile)
    defer sFile.Close()
	dFile, err2 := os.OpenFile(dstFile, os.O_CREATE|os.O_RDWR|os.O_APPEND, 0666)
    defer dFile.Close()
	if err1 != nil {
		return err1
	}
	if err2 != nil {
		return err2
	}
	var tempSlice = make([]byte, 128)
	for {
		//读取数据
		n, e1 := sFile.Read(tempSlice)
		if e1 == io.EOF {
			break
		}
		if e1 != nil {
			return e1
		}

		//写入数据
		if _, e2 := dFile.Write(tempSlice[:n]); e2 != nil{
			return e2
		}
	}
	return nil
}

func main() {
    
	err := CopyFile("./test.txt", "./test2.txt")
	if err == nil {
		fmt.Printf("拷贝完成\n")
	} else {
		fmt.Printf("拷贝错误 err=%v\n", err)
	}
}
```



## 创建目录

```go
err := os.Mkdir("./abc", 0666)//已经存在不进行操作，不存在则创建文件夹

err := os.MkdirAll("dir1/dir2/dir3", 0666)
```



## 删除目录和文件

```go
os.Remove("abc/d/e/f")//可以删除目录也可删除文件
os.RemoveAll("/abc")//删除abc下所有目录和文件
```





# 格式化输出字符串

```go
type point struct {
	x, y int
}
func main() {
	// 打印结构体
	p := point{1, 2}
	fmt.Printf("%v
", p) //输出结果为 {1 2}

	// 如果值是一个结构体，%+v 变体将包括结构体的字段名。
	fmt.Printf("%+v
", p) //输出结果为 {x:1 y:2}

	// %#v 变体打印值的 Go 语法表示，即将生成该值的源代码片段。
	fmt.Printf("%#v
", p) //输出结果为 main.point{x:1, y:2}

	// 打印类型
	fmt.Printf("%T
", p) //输出结果为 main.point

	// 打印布尔值
	fmt.Printf("%t
", true) //输出结果为 true

	// 打印整数。
	fmt.Printf("%d
", 123) //输出结果为 123

	// 打印二进制
	fmt.Printf("%b
", 14) //输出结果为 1110

	// 打印字符
	fmt.Printf("%c
", 33) //输出结果为 ！

	// 打印 16 进制
	fmt.Printf("%x
", 456) //输出结果为 1c8

	// 打印浮点数
	fmt.Printf("%f
", 78.9) //输出结果为 78.900000
	// 指数型
	fmt.Printf("%e
", 123400000.0) //输出结果为 1.234000e+08
	fmt.Printf("%E
", 123400000.0) //输出结果为 1.234000E+08

	// 字符串
	fmt.Printf("%s
", ""string"") //输出结果为 "string"
	// 双引号字符串.
	fmt.Printf("%q
", ""string"") //输出结果为 ""string""

	// 每个字符用两位 16 进制表示。
	fmt.Printf("%x
", "hex this") //输出结果为 6865782074686973

	// 打印指针`.
	fmt.Printf("%p
", &p) //输出结果为 0xc00001c08

	// 字符宽度
	fmt.Printf("|%6d|%6d|
", 12, 345) //输出结果为 |    12|   345|

	//字符精度
	fmt.Printf("|%6.2f|%6.2f|
", 1.2, 3.45) //输出结果为 |  1.20|  3.45|

	// 左对齐
	fmt.Printf("|%-6.2f|%-6.2f|
", 1.2, 3.45) //输出结果为 |1.20  |3.45  |

	//同样可以控制字符的宽度
	fmt.Printf("|%6s|%6s|
", "foo", "b") //输出结果为 |   foo|     b|

	// 同样字符左对齐.
	fmt.Printf("|%-6s|%-6s|
", "foo", "b") //输出结果为 |foo   |b     |

	// 合并
	s := fmt.Sprintf("a %s", "string") //输出结果为 a string
	fmt.Println(s) //输出结果为
	// 同样的效果
	fmt.Fprintf(os.Stderr, "an %s
", "error") //输出结果为 an error
}

```

# 值类型：基本数据类型、数组、结构体

# 引用类型：Slice、Map、interface、channel、指针

函数调用时，Slice、Map、interface、channel默认使用引用传递

```go
	arr1 := [...]int{1,2,3}
	arr2 := arr1
	arr1[0] = 11
	fmt.Printf("arr1:%v, arr2:%v\n", arr1, arr2)

	slice1 := []int{1,2,3}
	slice2 := slice1
	slice1[0] = 11
	fmt.Printf("slice1:%v, slice2:%v\n", slice1, slice2)

//arr1:[11 2 3], arr2:[1 2 3]
//slice1:[11 2 3], slice2:[11 2 3]
```

# Go语言中每种类型对应的零值

```
bool -> false
numbers -> 0
string -> ""
pointers -> nil
slices -> nil
maps -> nil
channels -> nil
functions -> nil
```

# Go是nil却不等于nil的情况

```go
func main()  {
	var a *struct{} //结构体指针
	var f func()	//函数指针
	var m map[string]string	//未初始化的map

	if a == nil {
		fmt.Println("var a *struct{} is nil")
	}

	if f == nil {
		fmt.Println("var f func() is nil")
	}

	if m == nil {
		fmt.Println("var m map[string]string is nil")
	}

	ifList := []interface{}{a, f, m, nil}
	for _,item := range ifList {
		if item != nil {
			fmt.Println(item, "is not nil")
		} else {
			fmt.Println(item, "is nil")
		}
	}
}
//输出
//var a *struct{} is nil
//var f func() is nil
//var m map[string]string is nil
//<nil> is not nil
//<nil> is not nil
//map[] is not nil
//<nil> is nil
```

原因：有两种interface，一种数据结构的interface（空接口切片的那个），一种关键字定义的interface

数据结构的interface的本质是eface，另一种接口本质为iface，都是一个结构体包含一个类型字段和一个数据字段

```go
//runtime2.go
type iface struct {
	tab  *itab
	data unsafe.Pointer
}

type eface struct {
	_type *_type
	data  unsafe.Pointer
}
```

对于代码中的空接口类型切片中的元素，有type而没有data，只有当两个字段都是nil的时候才会判断为nil

## 在空接口类型的切片中判断元素是否为nil的方法：

方法一：通过反射中的IsNil()方法判断

```go
if reflect.ValueOf(item).IsNil() {
    ...
}
```



方法二：通过类型断言

```go
if v,ok := item.(*struct{}); ok && v == nil //item此时是空接口类型

if v,ok := item.(func()); ok && v == nil

if v,ok := item.(map[string]string); ok && v == nil
```



# Go中的类型比如：channel（通道）、complex（复数类型）、func（函数）均不能进行序列化
