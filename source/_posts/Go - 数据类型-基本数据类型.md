---
title: Go数据类型-基本数据类型
top_img: >-
  https://could-res-1252778021.cos.ap-shanghai.myqcloud.com/pic/wallpaper/1618220328417.jpg
cover: >-
  https://could-res-1252778021.cos.ap-shanghai.myqcloud.com/pic/wallpaper/1618220328417.jpg
tags: Go
abbrlink: 9717c201
date: 2021-10-21 15:52:55
---



# Go的关键字&预定义标识符

25个关键字

| 1        | 2           | 3      | 4         | 5      |
| -------- | ----------- | ------ | --------- | ------ |
| break    | default     | func   | interface | select |
| case     | defer       | go     | map       | struct |
| chan     | else        | goto   | package   | switch |
| const    | fallthrough | if     | range     | type   |
| continue | for         | import | return    | var    |

预定义标识符

| 1       | 2         | 3          | 4       | 5     |
| ------- | --------- | ---------- | ------- | ----- |
| append  | bool      | byte       | cap     | close |
| complex | complex64 | complex128 | uint16  | copy  |
| FALSE   | float32   | float64    | imag    | int   |
| int8    | int16     | uint32     | int32   | int64 |
| iota    | len       | make       | new     | nil   |
| panic   | uint64    | print      | println | real  |
| recover | string    | TRUE       | uint    | uint8 |
| uintp   |           |            |         |       |

字节 也叫 Byte，是计算机数据的基本存储单位。习惯上用大写 B 来表示。bit 位

8bit = 1Byte

1024Byte = 1KB 

1024KB = 1MB

1024MB = 1GB

1024GB = 1TB 。

在电脑里一个中文字是占两个字节的unsafe.Sizeof(n1) 是 unsafe 包的一个函数，可以返回 n1 变量占用的字节数





# 基本数据类型

### 整型

有符号整型：int8、int16、int32、int64

无符号整型：uint8、uint16、uint32、uint64

| 类型   | 取值范围                                                     | 占用空间 | 有无符号 |
| ------ | ------------------------------------------------------------ | -------- | -------- |
| int8   | (-128 到 127) 即 -2^7 到 2^7 -1                              | 1个字节  | 有       |
| int16  | (-32768 到 32767) 即 -2^15 到 2^15-1                         | 2个字节  | 有       |
| int32  | (-2147483648 到 2147483647) 即 -2^31 到 2^31-1               | 4个字节  | 有       |
| int64  | (-9223372036854775808 到 9223372036854775807) 即 -2^63 到 2^63-1 | 8个字节  | 有       |
| uint8  | (0 到 255) 即 0 到 2^8-1                                     | 1个字节  | 无       |
| uint16 | (0 到 65535) 即 0 到 2^16-1                                  | 2个字节  | 无       |
| uint32 | (0 到 4294967295) 即 0 到 2^32-1                             | 4个字节  | 无       |
| uint64 | (0 到 18446744073709551615) 即 0 到 2^64-1                   | 8个字节  | 无       |

特殊整型：uint、int、uintptr

| 类型    | 描述                                                 |
| ------- | ---------------------------------------------------- |
| uint    | 在32位操作系统上是uint32，在64位操作系统上就是uint64 |
| int     | 在32位操作系统上是int32，在64位操作系统上就是int64   |
| uintptr | 无符号整型，用于存放一个指针                         |

==重点==

- 在使用 int 和 uint 类型时，不能假定它是 32 位或 64 位的整型，而是考虑 int 和 uint 可能在不同平台上的差异

- 实际项目中整数类型、切片、 map 的元素数量等都可以用 int 来表示。 在涉及到二进制传输、为了保持文件的结构不会受到不同编译目标平台字节长度的影响 ，不要使用 int 和 和 uint 。



#### 数字字面量语法（Number literals syntax ） （了解）

Go1.13 版本之后引入了数字字面量语法，这样便于开发者以二进制、八进制或十六进制浮点数的格式定义数字

```go
var a int = 10           //十进制 
var b int = 077          //八进制 以0开头 
var c int = 0xff         //十六进制 以0x开头 

fmt.Printf("%d \n", a)   //占位符 %d 表示十进制数字 
fmt.Printf("%b \n", a)   //占位符 %b 表示二进制数字 
fmt.Printf("%o \n", b)   //占位符 %o 表示八进制数字 
fmt.Printf("%x \n", c)   //ff 
fmt.Printf("%X \n", c)   //FF
```

允许用 _ 来分隔数字

```go
v := 123_456     //等于 123456
```



### **浮点型**

float32 和 float64。这两种浮点型数据格式遵循 IEEE 754 标准

| 类型    | 最大取值范围（约值） | 使用常量定义    |
| ------- | -------------------- | --------------- |
| float32 | 3.40E+38             | math.MaxFloat32 |
| float64 | 1.8e308              | math.MaxFloat64 |

输出浮点数

```go
//占位符 %f 表示浮点数 
fmt.Printf("%f\n", math.Pi)     // 默认保留 6 位小数 
fmt.Printf("%.2f\n", math.Pi)   // 保留 2 位小数
```

- ==浮点数默认是 float64==
- float 精度丢失问题
  - ==几乎所有的编程语言都有精度丢失这个问题，这是典型的二进制浮点数精度损失问题，在定长条件下，二进制小数和十进制小数互转可能有精度丢失==
  - 使用第三方包来解决精度损失问题：https://github.com/shopspring/decimal
- 科学计数法 表示浮点类型



### 布尔值

布尔型数据==只有 true（真）和 false（假）两个值==
	==1. 布尔类型变量的默认值为 false==
	==2. Go 语言中不允许将整型强制转换为布尔型==
	==3. 布尔型无法参与数值运算，也无法与其他类型进行转换==



### 字符串

Go 语言中的==字符串以原生数据类型出现==，使用字符串就像使用其他原生数据类型（int、bool、float32、float64 等）一样。
Go 语言里的字符串的内部==实现使用 UTF-8 编码==。
字符串的表示法有两种，即：==原生表示法和解释型表示法==。

- 原生表示法，需用用反引号"`"把字符序列包起来，
  - 解释型表示法，需要用双引号 """ 包裹字符序列。

==定义一个多行字符串时，就必须使用反引号字符。反引号间换行将被作为字符串中的换行，但是所有的转义字符均无效，文本将会原样输出==

```go
s1 := "hello" 
s2 := `第一行 
第二行 
第三行 
`
```

字符串转义符

| 转义符 | 含义                           |
| ------ | ------------------------------ |
| \r     | 回车符（返回行首）             |
| \n     | 换行符（跳到下一行的同列位置） |
| \t     | 制表符                         |
| \'     | 单引号                         |
| \"     | 双引号                         |
| \\     | 反斜杠                         |

字符串的常用操作

| 方法                                                         | 作用                                          |
| ------------------------------------------------------------ | --------------------------------------------- |
| len(str)                                                     | 求长度                                        |
| + 或 fmt.Sprintf                                             | 拼接字符串                                    |
| strings.Split(str, "分隔符")                                 | 分割成数组                                    |
| strings.contains(str, "要查找的内容")                        | 判断是否包含                                  |
| strings.HasPrefix(str, "前缀") strings.HasSuffix(str, "后缀") | 前缀/后缀判断                                 |
| strings.Index(str, "子串") strings.LastIndex(str, "子串")    | 从前往后/从后往前查找子串，返回子串出现的位置 |
| strings.Join(a[]string, sep string)                          | join 操作，数组拼接成字符串                   |



### byte和rune类型

字符

组成每个字符串的元素叫做“字符”, 是指==计算机中使用的字母、数字、字和符号==。
* 
  可以通过遍历字符串元素获得字符。 ==字符用单引号（'）包裹起来==
* ==直接输出 byte（字符）的时候输出的是这个字符对应的ASCII 码码值。如果要输出字符，需要格式化输出，占位符 %c 表示字符相应的 Unicode 码点所表示的字符==
* ==一个汉字占用 3 节 个字节 一个字母占用一个字节==



Go 语言的字符有以下两种：
	1. 
byte 型，==实际上是uint8 类型==，代表了 ASCII 码的一个字符
	2. 
  rune 类型，==实际上是int32==，代表一个 UTF-8 字符

==当需要处理中文、日文或者其他复合字符时，则需要用到 rune 类型==。rune 类型实际是一个int32。Go 使用了特殊的 rune 类型来处理 Unicode，让基于 Unicode 的文本处理更为方便，也可以使用 byte 型进行默认字符串处理，性能和扩展性都有照顾
字符串底层是一个 byte 数组，所以==可以和[]byte 类型相互转换==。字符串是不能修改的，字符串是由 byte 字节组成，所以字符串的长度是 byte 字节的长度。 rune 类型用来表示 utf8 字符，一个 rune 字符由一个或多个 byte 组成
==要修改字符串，需要先将其转换成[]rune 或[]byte，完成后再转换为 string。无论哪种转换，都会重新分配内存，并复制字节数组==



### 复数

在计算机中，复数是由两个浮点数表示的，其中一个表示实部（real），一个表示虚部（imag）。
* 
  Go语言中复数的类型有两种，分别是 complex128（64 位实数和虚数，默认类型）和 complex64（32 位实数和虚数）
* 
  复数的值由三部分组成 RE + IMi，其中 RE 是实数部分，IM 是虚数部分，RE 和 IM 均为 float 类型，而最后的 i 是虚数单位



**语法格式**

```go
var name complex128 = complex(x, y) 
name := complex(x, y) 
// name 为复数的变量名 
//complex128 为复数的类型， 
//“=”后面的 complex 为Go语言的内置函数用于为复数赋值， 
//x、y 分别表示构成该复数的两个 float64 类型的数值，x 为实部，y 为虚部
```

对于一个复数z := complex(x, y)，可以通过Go语言的内置函数real(z)来获得该复数的实部，也就是 x；通过imag(z)获得该复数的虚部，也就是 y

【示例】使用内置的 complex 函数构建复数，并使用 real 和 imag 函数返回复数的实部和虚部

```go
var x complex128 = complex(1, 2) // 1+2i 
var y complex128 = complex(3, 4) // 3+4i 
fmt.Println(x*y) // "(-5+10i)" 
fmt.Println(real(x*y)) // "-5" 
fmt.Println(imag(x*y)) // "10"
```

复数也可以用==和!=进行相等比较，只有两个复数的实部和虚部都相等的时候它们才是相等的

Go语言内置的 math/cmplx 包中提供了很多操作复数的公共方法，==实际操作中建议大家使用复数默认的 complex128 类型，因为这些内置的包中都使用 complex128 类型作为参数==



### 指针

指针（pointer）在Go语言中可以被拆分为两个核心概念：

- 类型指针，允许对这个指针类型的数据进行修改，传递数据可以直接使用指针，而无须拷贝数据，类型指针不能进行偏移和运算。

- 切片，由指向起始元素的原始指针、元素数量和容量组成。

**认识指针地址和指针类型**
一个指针变量可以指向任何一个值的内存地址，它==所指向的值的内存地址在 32 和 64 位机器上分别占用 4 或 8 个字节，占用字节的大小与所指向的值的大小无关==。

当一个指针被定义后没有分配到任何变量时，它的==默认值为 nil。指针变量通常缩写为 ptr==

每个变量在运行时都拥有一个地址，这个地址代表变量在内存中的位置。

**取地址**
Go语言中使用在变量名前面添加&操作符（前缀）来获取变量的内存地址（取地址操作），格式如下：

```go
ptr := &v // v 的类型为 T
```

其中 v 代表被取地址的变量，变量 v 的地址使用变量 ptr 进行接收，ptr 的类型为*T，称做 T 的指针类型，*代表指针

从指针获取指针指向的值

当使用&操作符对普通变量进行取地址操作并得到变量的指针后，可以对指针使用*操作符，也就是指针取值

```go
var house = "Malibu Point 10880, 90265" 
// 对字符串取地址, ptr类型为*string 
ptr := &house 
// 对指针进行取值操作 
value := *ptr
```

取地址操作符&和取值操作符*是一对互补操作符，&取出地址，*根据地址取出地址指向的值
==重点理解：==
变量、指针和地址三者的关系是，每个变量都拥有内存地址，指针的值就是内存地址

变量、指针地址、指针变量、取地址、取值的相互关系和特性如下：

- 对变量进行取地址操作使用&操作符，可以获得这个变量的指针变量。
 - 
    指针变量的值是指针地址。
- 
  对指针变量进行取值操作使用*操作符，可以获得指针变量指向的原变量的值。

**使用指针修改值**

通过指针不仅可以取值，也可以修改值

```go
// 取a指针的值, 赋给临时变量t 
t := *a 
// 取b指针的值, 赋给a指针指向的变量 
*a = *b 
// 将a指针的值赋给b指针指向的变量 
*b = t
```

- \* 操作符作为右值时，意义是取指针的值，作为左值时，也就是放在赋值操作符的左边时，表示 a 指针指向的变量。
- 其实归纳起来，* 操作符的根本意义就是操作指针指向的变量。当操作在右值时，就是取指向变量的值，当操作在左值时，就是将值设置给指向的变量

**创建指针的另一种方法——new() 函数**

```go
new(类型)
```

demo

```go
str := new(string) 
*str = "Go语言教程" 
fmt.Println(*str)
```

new() 函数可以创建一个对应类型的指针，创建过程会分配内存，被创建的指针指向默认值



# 基本数据类型之间的相互转换

Go 语言中==只有强制类型转换，没有隐式类型转换==

数值类型之间的相互转换

数值类型包括：==整形和浮点型==

==转换的时候建议从低位转换成高位，高位转换成低位的时候如果转换不成功就会溢出==

```go
var a = 1 
var b = int(a) 
var c = int8(a) 
var d = int16(a) 
var e = int32(a) 
var f = int64(a) 
var g = uint(a) 
var h = uint8(a) 
var i = uint16(a) 
var j = uint32(a) 
var k = uint64(a) 
var l = uintptr(a) 
var m = float32(a) 
var n = float64(a) 
fmt.Println(a, b, c, d, e, f, g, h, i, j, k, l, m, n)
```

#### 其他类型转换成 String 类型

方法一：sprintf 把其他类型转换成 string

注意：sprintf 使用中需要注意转换的格式 int 为%d，float 为%f，bool 为%t，byte 为%c

```go
var i int = 20 
var f float64 = 12.456 
var t bool = true 
var b byte = 'a' 
var strs string 

strs = fmt.Sprintf("%d", i) 
fmt.Printf("str type %T ,strs=%v \n", strs, strs) 
strs = fmt.Sprintf("%f", f) 
fmt.Printf("str type %T ,strs=%v \n", strs, strs) 
strs = fmt.Sprintf("%t", t) 
fmt.Printf("str type %T ,strs=%v \n", strs, strs) 
strs = fmt.Sprintf("%c", b) 
fmt.Printf("str type %T ,strs=%v \n", strs, strs)
```



方法二： 使用 strconv 包里面的几种转换方法进行转换

**int 转换成 string**

strconv.Itoa(num)

```go
var num1 int = 20 
s1 := strconv.Itoa(num1) 
fmt.Printf("str type %T ,strs=%v \n", s1, s1)
```

**float 转 string**

```go
strconv.FormatFloat(num, 'f', 2, 64) 

参数 1：要转换的值 
参数 2：格式化类型 
        'f'（-ddd.dddd）、 
        'b'（-ddddp±ddd，指数为二进制）、 
        'e'（-d.dddde±dd，十进制指数）、 
        'E'（-d.ddddE±dd，十进制指数）、 
        'g'（指数很大时用'e'格式，否则'f'格式）、 
        'G'（指数很大时用'E'格式，否则'f'格式）。 
参数 3: 保留的小数点 -1（不对小数点格式化） 
参数 4：格式化的类型
```

2

```go
var num2 float64 = 20.113123 
s2 := strconv.FormatFloat(num2, 'f', 2, 64) 
fmt.Printf("str type %T ,strs=%v \n", s2, s2)
```

**bool 转 string：**

strconv.FormatBool(true/false)

```go
s3 := strconv.FormatBool(true) 
fmt.Printf("str type %T ,strs=%v \n", s3, s3)
```

**int64 转 string：**

strconv.FormatInt(num, 进制)

```go
var num3 int64 = 20 

s4 := strconv.FormatInt(num3, 10) 
fmt.Printf("类型 %T ,strs=%v \n", s4, s4)
```



#### String 类型转换成数值类型

**string 类型转换成 int 类型**

strconv.ParseInt(str, 10, 64)

第二个参数是进制，第三个参数是bitSize，比如int8就是8

```go
var s = "1234" 
i64, _ := strconv.ParseInt(s, 10, 64) 
fmt.Printf("值：%v 类型：%T", i64, i64)
```



string 类型转换成 float 类型

strconv.ParseFloat(str, 32)

第二个参数是bitSize，比如float32就是32

```go
str := "3.1415926535" 
v1, _ := strconv.ParseFloat(str, 32) 
v2, _ := strconv.ParseFloat(str, 64) 
fmt.Printf("值：%v 类型：%T\n", v1, v1) 
fmt.Printf("值：%v 类型：%T", v2, v2)
```



**string 转字符**

```go
s := "hello 张三" 
for _, r := range s { 
    //rune 
    fmt.Printf("%v(%c) ", r, r) 
} 
fmt.Println()
```



#### string 类型转换成 bool 类型(意义不大)

strconv.ParseBool(str)

```go
b, _ := strconv.ParseBool("true") // string 转 bool 
fmt.Printf("值：%v 类型：%T", b, b)
```



==数值类型没法和 bool类型进行转换==

注意：在 go 语言中数值类型没法直接转换成 bool 类型 bool 类型也没法直接转换成数值类型


