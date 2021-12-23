---
title: Go - 流程控制语句
top_img: >-
  https://could-res-1252778021.cos.ap-shanghai.myqcloud.com/pic/wallpaper/timg1.jpg
cover: >-
  https://could-res-1252778021.cos.ap-shanghai.myqcloud.com/pic/wallpaper/timg1.jpg
tags: Go
abbrlink: e4384262
date: 2021-10-21 18:16:12
categories:
  - Go
---

**流程控制**

流程控制是每种编程语言控制逻辑走向和执行次序的重要部分，流程控制可以说是一门语言的“经脉”。 Go 语言中最常用的流程控制有 if 和 for，而 switch 和 goto 主要是为了简化代码、降低重复代码而生的结构，属于扩展类的流程控制



# **if else（分支结构）**

## 基本写法

```go
if 表达式 1 { 
    分支 1 
} else if 表达式 2 { 
    分支 2 
} else { 
    分支 3 
}
```

Go 语言规定与 if 匹配的左括号 { 必须与 if 和表达式放在同一行，{ 放在其他位置会触发编译错误。

同理，与 else 匹配的 { 也必须与 else 写在同一行，else 也必须与上一个 if 或 else if 右边的大括号在同一行



## 特殊写法

if 条件判断还有一种特殊的写法，可以在 if 表达式之前添加一个执行语句，再根据变量值进行判断

```go
if score := 56; score >= 90 { 
    fmt.Println("A") 
} else if score > 75 { 
    fmt.Println("B") 
} else { 
    fmt.Println("C") 
}
```





# **for （循环结构）**

Go 语言中的所有循环类型均可以使用 for 关键字来完成

## 基本格式

```go
for 初始语句;条件表达式;结束语句{ 
    循环体语句 
}
```

比如：

```go
// 
for i := 0; i < 10; i++ { 
    fmt.Println(i) 
} 

//for 循环的初始语句可以被忽略，但是初始语句后的分号必须要写，例如 
i := 0 
for ; i < 10; i++ { 
    fmt.Println(i) 
} 

//for 循环的初始语句和结束语句都可以省略 
//这种写法类似于其他编程语言中的 while，在 while 后添加一个条件表达式，满足条件表达式时持续循环，否则结束循环 
i := 0 for i < 10 { 
    fmt.Println(i) 
    i++ 
}
```

条件表达式返回 true 时循环体不停地进行循环，直到条件表达式返回 false 时自动退出循环

Go 语言中是没有 while 语句的，我们可以通过 for 代替

for 循环可以通过 break、goto、return、panic 语句强制退出循环



## **for 无限循环**

```go
for { 
    循环体语句 
}
```



## **for range（键值循环）**

Go 语言中可以使用 for range 遍历数组、切片、字符串、map 及通道（channel）。

for range 语法上类似于其它语言中的 foreach 语句，通过 for range 遍历的返回值有以下规律：

1. 数组、切片、字符串返回索引和值。
2. map 返回键和值。
3. 通道（channel）只返回通道内的值

一般语法

```go
for key, val := range coll {
     ... 
}
```

val 始终为集合中对应索引的值拷贝，因此它一般只具有只读性质，对它所做的任何修改都不会影响到集合中原有的值

使用

**遍历数组、切片——获得索引和值**

在遍历代码中，key 和 value 分别代表切片的下标及下标对应的值，下面的代码展示如何遍历切片，数组也是类似的遍历方法

```go
for key, value := range []int{1, 2, 3, 4} { 
    fmt.Printf("key:%d value:%d\n", key, value) 
}
```



**遍历字符串——获得字符**

Go语言和其他语言类似，可以通过 for range 的组合，对字符串进行遍历，遍历时，key 和 value 分别代表字符串的索引和字符串中的每一个字符

```go
var str = "hello 你好" 
for key, value := range str { 
    fmt.Printf("key:%d value:0x%x\n", key, value) 
} 
//代码中的变量 value，实际类型是 rune 类型，以十六进制打印出来就是字符的编码
```



**遍历 map——获得 map 的键和值**

对 map 遍历时，遍历输出的键值是无序的，如果需要有序的键值对输出，需要对结果进行排序

对于 map 类型来说，for range 遍历时，key 和 value 分别代表 map 的索引键 key 和索引对应的值，一般被称为 map 的键值对，因为它们是一对一对出现的，下面的代码演示了如何遍历 map

```go
m := map[string]int{ "hello": 100, "world": 200, } 
for key, value := range m { 
    fmt.Println(key, value) 
}
```



**遍历通道（channel）——接收通道数据**

for range 可以遍历通道（channel），但是通道在遍历时，只输出一个值，即管道内的类型对应的数据

```go
c := make(chan int) 
go func() { 
    c <- 1 
    c <- 2 
    c <- 3 
    close(c) 
}() 
for v := range c { 
    fmt.Println(v) 
}
```

代码说明：

- 第 1 行创建了一个整型类型的通道。
- 第 3 行启动了一个 goroutine，其逻辑的实现体现在第 5～8 行，实现功能是往通道中推送数据 1、2、3，然后结束并关闭通道。
- 这段 goroutine 在声明结束后，在第 9 行马上被执行。
- 从第 11 行开始，使用 for range 对通道 c 进行遍历，其实就是不断地从通道中取数据，直到通道被关闭

在遍历中选择希望获得的变量

在使用 for range 循环遍历某个对象时，一般不会同时需要 key 或者 value，这个时候可以采用一些技巧，让代码变得更简单，下面将前面的例子修改一下，参考下面的代码示例

```go
m := map[string]int{ "hello": 100, "world": 200, } 
for _, value := range m { 
    fmt.Println(value) 
}
```



在上面的例子中将 key 变成了下划线_，这里的下划线就是匿名变量。

- 可以理解为一种占位符。
- 匿名变量本身不会进行空间分配，也不会占用一个变量的名字。
- 在 for range 可以对 key 使用匿名变量，也可以对 value 使用匿名变量



总结一下 for 的功能：

- Go语言的 for 包含初始化语句、条件表达式、结束语句，这 3 个部分均可缺省。
- for range 支持对数组、切片、字符串、map、通道进行遍历操作。
- 在需要时，可以使用匿名变量对 for range 的变量进行选取



# **switch case**

Go语言改进了 switch 的语法设计，case 与 case 之间是独立的代码块，==不需要通过 显示定义break 语句跳出当前 case== 代码块以避免执行到下一行。也就是说Go的switch中的case默认会break。

## 基本语法

Go语言规定每个 switch 只能有一个 default 分支

```go
var a = "hello" 
switch a { 
    case "hello": 
        fmt.Println(1) 
    case "world": 
        fmt.Println(2) 
    default: 
        fmt.Println(0) 
}
```



## 一分支多值

当出现多个 case 要放在一起的时候，不同的 case 表达式使用逗号分隔，可以写成下面这样

```go
var a = "mum" 
switch a { 
    case "mum", "daddy": 
        fmt.Println("family") 
}
```



## 分支表达式

case 后不仅仅只是常量，还可以和 if 一样添加表达式，这种情况的 switch 后面不再需要跟判断变量

```go
var r int = 11 
switch { 
    case r > 10 && r < 20: 
        fmt.Println(r) 
}
```



## 跨越 case 的 fallthrough

Go语言中 case 是一个独立的代码块，执行完毕后不会像其他语言那样紧接着执行下一个 case。fallthrouth的作用是兼容模拟其他语言不写break出现的case穿透，在Go中写了 fallthrouth 才会出现case穿透

```go
var s = "hello" 
switch { 
    case s == "hello": 
        fmt.Println("hello") 
    fallthrough 
    case s != "world": 
        fmt.Println("world") 
}
```





# select - 待完善







# continue & break & goto的使用

## **continue**

跳出本次循环继续下一次循环，仅限在 for 循环内使用，在 continue 语句后添加标签时，表示开始标签对应的循环

```go
package main 

import "fmt" 

func main() { 
    //跳出当前循环继续下一次循环 
    for i := 0; i < 2; i++ { 
        for j := 0; j < 5; j++ { 
            switch j { 
                case 2: 
                    fmt.Println(i, j) 
                    continue
             } 
         }
    } 
    //跳出循环后开始标签对应的循环 OuterLoop: 
    for i := 0; i < 2; i++ { 
        for j := 0; j < 5; j++ { 
            switch j { 
                case 2: 
                    fmt.Println(i, j) 
                    continue OuterLoop 
            } 
        } 
    } 
}
```

第 14 行将结束当前循环，开启下一次的外层循环，而不是第 10 行的循环



## **break**

break 语句可以结束 for、switch 和 select 的代码块，另外 break 语句还可以在语句后面添加标签，表示退出某个标签对应的代码块，标签要求必须定义在对应的 for、switch 和 select 的代码块上

for 循环中默认 break 只能跳出一层循环

在多重循环中，可以用标号 label 标出想 break 的循环

```go
package main 

import "fmt" 

func main() { 
    //跳出循环 
    for i := 0; i < 2; i++ { 
        for j := 0; j < 5; j++ { 
            switch j { 
                case 2: 
                    fmt.Println(i, j) 
                    break 
                case 3: 
                    fmt.Println(i, j) 
                    break 
            } 
        }
    } 
    //跳出循环到指定标记 OuterLoop: 
    for i := 0; i < 2; i++ { 
        for j := 0; j < 5; j++ { 
            switch j { 
                case 2: 
                    fmt.Println(i, j) 
                    break OuterLoop 
                case 3: 
                    fmt.Println(i, j) 
                    break OuterLoop 
            } 
        } 
    } 
}
```



## **goto**

goto 语句通过标签进行代码间的无条件跳转。goto 语句可以在快速跳出循环、避免重复退出上有一定的帮助。Go 语言中使用 goto 语句能简化一些代码的实现过程

需要连续退出两层循环，使用传统的编码方式如下

```go
package main 

import "fmt" 

func main() { 
    var breakAgain bool 
    // 外循环 
    for x := 0; x < 10; x++ { 
        // 内循环 
        for y := 0; y < 10; y++ { 
            // 满足某个条件时, 退出循环 
            if y == 2 { 
                // 设置退出标记 
                breakAgain = true 
                // 退出本次循环 
                break 
            } 
        } 
        // 根据标记, 还需要退出一次循环 
        if breakAgain { 
            break 
        } 
    }
    fmt.Println("done") 
}
```

使用Go语言的 goto 语句进行优化。使用 goto 语句后，无须额外的变量就可以快速退出所有的循环

```go
package main 

import "fmt" func main() { 
    for x := 0; x < 10; x++ { 
        for y := 0; y < 10; y++ { 
            if y == 2 { 
                // 跳转到标签 
                goto breakHere 
            } 
        } 
    } 
    // 手动返回, 避免执行进入标签 
    return 
    // 标签 
    breakHere: 
    fmt.Println("done") 
}
```



