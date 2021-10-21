---
title: Go - 数据类型-复合数据类型
top_img: >-
  https://could-res-1252778021.cos.ap-shanghai.myqcloud.com/pic/wallpaper/5991569950166.jpg
cover: >-
  https://could-res-1252778021.cos.ap-shanghai.myqcloud.com/pic/wallpaper/5991569950166.jpg
tags: Go
abbrlink: 5fc11a3d
date: 2021-10-21 16:35:45
---



# 复合数据类型

数组、切片、结构体、函数、map、通道（channel）、接口等



## 数组

- 数组是一个由固定长度的特定类型元素组成的序列，一个数组可以由零个或多个元素组成。因为数组的长度是固定的，所以在Go语言中很少直接使用数组。

* 
数组是指一系列同一类型数据的集合。数组中包含的每个数据被称为数组元素(element)，这种类型可以是任意的原始类型，比如 int、string 等，也可以是用户自定义的类型。
* 
一个数组包含的元素个数被称为数组的长度。在 Golang 中数组是一个长度固定的数据类型， 数组的长度是类型的一部分，也就是说 [5]int 和 [10]int 是两个不同的类型。
* 
Golang中数组的另一个特点是占用内存的连续性，也就是说数组中的元素是被分配到连续的内存地址中的，因而索引数组元素的速度非常快

### 数组的声明

```go
//一般格式 
var 数组变量名 [元素数量]Type 
//声明并初始化 可以只初始化部分元素值 
var 数组变量名 [元素数量]Type{value1,value2,...} 
//数组的长度根据初始化值的个数来自动计算 
var 数组变量名 [...]Type{value1,value2,...}
```



语法说明如下所示：

- 数组变量名：数组声明及使用时的变量名。

- 元素数量：数组的元素数量，可以是一个表达式，但最终通过编译期计算的结果必须是整型数值，元素数量不能含有到运行时才能确认大小的数值。

- Type：可以是任意基本类型，包括数组本身，类型为数组本身时，可以实现多维数组

  

1. 数组的每个元素都可以通过索引下标来访问，索引下标的范围是从 0 开始到数组长度减 1 的位置，内置函数 len() 可以返回数组中元素的个数，最后一个元素下标是：len-1，访问越界（下标在合法范围之外），则触发访问越界，会 panic
2. 默认情况下，数组的每个元素都会被初始化为元素类型对应的零值，对于数字类型来说就是 0
3. 数组的长度是数组类型的一个组成部分，因此 [3]int 和 [4]int 是两种不同的数组类型，数组的长度必须是常量表达式，因为数组的长度需要在编译阶段确定
4. 如果两个数组类型相同（包括数组的长度，数组中元素的类型）的情况下，我们可以直接通过较运算符（==和!=）来判断两个数组是否相等，只有当两个数组的所有元素都是相等的时候数组才是相等的，不能比较两个类型不同的数组，否则程序将无法完成编译

```go
var a [3]int         // 定义三个整数的数组 
fmt.Println(a[0])     // 打印第一个元素 
fmt.Println(a[len(a)-1])     // 打印最后一个元素 
// 打印索引和元素 
for i, v := range a { 
    fmt.Printf("%d %d\n", i, v) 
} 
// 仅打印元素 
for _, v := range a { 
    fmt.Printf("%d\n", v) 
} 
//定义数组并初始化 
var q [3]int = [3]int{1, 2, 3} 
var r [3]int = [3]int{1, 2} // 初始化部分元素 
fmt.Println(r[2]) // "0"  

//不直接定义数组长度 
q := [...]int{1, 2, 3} 
fmt.Printf("%T\n", q) // "[3]int" // 
q := [3]int{1, 2, 3} 
q = [4]int{1, 2, 3, 4} // 编译错误：无法将 [4]int 赋给 [3]int 

//比较两个数组是否相等 
a := [2]int{1, 2} 
b := [...]int{1, 2} 
c := [2]int{1, 3} 
fmt.Println(a == b, a == c, b == c) // "true false false" 
d := [3]int{1, 2} 
fmt.Println(a == d) // 编译错误：无法比较 [2]int == [3]int
```



### 数组的遍历

```go
var a = [...]string{"北京", "上海", "深圳"} 
// 方法 1 ： for 循环遍历 
for i := 0; i < len(a); i++ { 
    fmt.Println(a[i]) 
} 
// 方法 2 ： for range 遍历 
for index, value := range a { 
    fmt.Println(index, value) 
}
```



### 数组的修改

数组是值类型，赋值和传参会复制整个数组。因此改变副本的值，不会改变本身的值。

`[n]*T 表示指针数组，*[n]T 表示数组指针`

```go
func modifyArray(x [3]int) {
     x[0] = 100 
} 

func modifyArray2(x [3][2]int) {
     x[2][0] = 100
} 

func main() { 
    a := [3]int{10, 20, 30} 
    modifyArray(a) // 在 modify 中修改的是 a 的副本 x 
    fmt.Println(a) //[10 20 30] 

    b := [3][2]int{ {1, 1}, {1, 1}, {1, 1}, } 
    modifyArray2(b)     // 在 modify 中修改的是 b 的副本 x 
    fmt.Println(b)      //[[1 1] [1 1] [1 1]] 
}
```



### 二维数组

声明

```go
//声明方式同一维数组基本一致 有几维就定义几层[元素数量]即可
//不过需要注意的是 ... 的方式如果是多维数组的话只能作用于第一层 
var 数组变量名 [元素数量][元素数量]T
```

循环

多维数组的循环用多层循环嵌套即可





## 切片

切片（slice）是对数组的一个连续片段的引用，所以切片是一个引用类型，这个片段可以是整个数组，也可以是由起始和终止索引标识的一些项的子集，需要注意的是，终止索引标识的项不包括在切片内

Go语言中切片的内部结构包含地址、大小和容量，切片一般用于快速地操作一块数据集合，如果将数据集合比作切糕的话，切片就是你要的“那一块”，切的过程包含从哪里开始（切片的起始位置）及切多大（切片的大小），容量可以理解为装切片的口袋大小，如下图所示

![1-1PQ3154340Y9](https://could-res-1252778021.file.myqcloud.com/img/1-1PQ3154340Y9.jpg)



说明：

1. 根据索引位置取切片 slice 元素值时，取值范围是（0～len(slice)-1），超界会报运行时错误，生成切片时，结束位置可以填写 len(slice) 但不会报错
2. 切片有点像C语言里的指针，指针可以做运算，但代价是内存操作越界，切片在指针的基础上增加了大小，约束了切片对应的内存区域，切片使用中无法对切片内部的地址和大小进行手动调整，因此切片比指针更安全、强大
3. 生成切片的格式中，当开始和结束位置都被忽略时，生成的切片将表示和原切片一致的切片，并且生成的切片与原切片在数据内容上也是一致的
4. 把切片的开始和结束位置都设为 0 时，生成的切片将变空
5. 切片是动态结构，只能与 nil 判定相等，不能互相判定相等。声明新的切片后，可以使用 append() 函数向切片中添加元素
6. 切片之间是不能比较的，我们不能使用==操作符来判断两个切片是否含有全部相等元素。切片唯一合法的比较操作是和 nil 比较。 一个 nil 值的切片并没有底层数组，一个 nil 值的切片的长度和容量都是 0。但是我们不能说一个长度和容量都是 0 的切片一定是 nil。所以要判断一个切片是否是空的，要是用 len(s) == 0 来判断，不应该使用 s == nil 来判断



### 生成切片

#### **从数组或切片生成新的切片**

切片默认指向一段连续内存区域，可以是数组，也可以是切片本身

从连续内存区域生成切片是常见的操作，格式如下：

```go
slice [开始位置 : 结束位置]
```

语法说明如下：

slice：表示目标切片对象；

开始位置：对应目标切片对象的索引；

结束位置：对应目标切片的结束索引。

```go
var a = [3]int{1, 2, 3} 
fmt.Println(a, a[1:2])
```

从数组或切片生成新的切片拥有如下特性：

- 取出的元素数量为：结束位置 - 开始位置；
- 取出元素不包含结束位置对应的索引，切片最后一个元素使用 slice[len(slice)] 获取；
- 当缺省开始位置时，表示从连续区域开头到结束位置；
- 当缺省结束位置时，表示从开始位置到整个连续区域末尾；
- 两者同时缺省时，与切片本身等效；
- 两者同时为 0 时，等效于空切片，一般用于切片复位



#### 直接生成新的切片

除了可以从原有的数组或者切片中生成切片外，也可以声明一个新的切片，每一种类型都可以拥有其切片类型，表示多个相同类型元素的连续集合，因此切片类型也可以被声明

切片类型声明格式如下

```go
var name []Type
```

其中 name 表示切片的变量名，Type 表示切片对应的元素类型

```go
// 声明字符串切片 
var strList []string 
// 声明整型切片 
var numList []int 
// 声明一个空切片 
var numListEmpty = []int{}
```



#### **使用make()函数构造切片**

如果需要动态地创建一个切片，可以使用 make() 内建函数，格式如下

```go
make( []Type, size, cap )
```

其中 Type 是指切片的元素类型，
size 指的是为这个类型分配多少个元素，
cap 为预分配的元素数量，这个值设定后不影响 size，只是能提前分配空间，降低多次分配空间造成的性能问题。

```go
a := make([]int, 2) 
b := make([]int, 2, 10)
```

代码说明:

- a 和 b 均是预分配 2 个元素的切片，只是 b 的内部存储空间已经分配了 10 个，但实际使用了 2 个元素
- 容量不会影响当前的元素个数，因此 a 和 b 取 len 都是 2

使用 make() 函数生成的切片一定发生了内存分配操作，但给定开始与结束位置（包括切片复位）的切片只是将新的切片结构指向已经分配好的内存区域，设定开始与结束位置，不会发生内存分配操作



#### **使用append()为切片添加元素**

Go语言的内建函数 append() 可以为切片动态添加元素(默认追加尾部)，代码如下所示

```go
var a []int a = append(a, 1)     // 追加1个元素 
a = append(a, 1, 2, 3)           // 追加多个元素, 手写解包方式 
a = append(a, []int{1,2,3}...)   // 追加一个切片, 切片需要解包
```

需要注意的是，在使用 append() 函数为切片动态添加元素时，如果空间不足以容纳足够多的元素，切片就会进行“扩容”，此时新切片的长度会发生改变

切片在扩容时，容量的扩展规律是按容量的 2 倍数进行扩充，例如 1、2、4、8、16……

除了在切片的尾部追加，我们还可以在切片的开头添加元素

```go
var a = []int{1,2,3} 
a = append([]int{0}, a...)         // 在开头添加1个元素 
a = append([]int{-3,-2,-1}, a...)  // 在开头添加1个切片
```

在切片开头添加元素一般都会导致内存的重新分配，而且会导致已有元素全部被复制 1 次，因此，从切片的开头添加元素的性能要比从尾部追加元素的性能差很多

因为 append 函数返回新切片的特性，所以切片也支持链式操作，我们可以将多个 append 操作组合起来，实现在切片中间插入元素

```go
var a []int a = append(a[:i], 
append([]int{x}, a[i:]...)...) // 在第i个位置插入x 
a = append(a[:i], append([]int{1,2,3}, a[i:]...)...) // 在第i个位置插入切片
```

每个添加操作中的第二个 append 调用都会创建一个临时切片，并将 a[i:] 的内容复制到新创建的切片中，然后将临时创建的切片再追加到 a[:i] 中



#### **使用copy()函数进行切片复制(切片拷贝)**

Go语言的内置函数 copy() 可以将一个数组切片复制到另一个数组切片中，copy() 函数的使用格式如下

```go
copy( destSlice, srcSlice []T) int
```

	* 其中 srcSlice 为数据来源切片，destSlice 为复制的目标（也就是将 srcSlice 复制到 destSlice）
 * 目标切片必须分配过空间且足够承载复制的元素个数，并且来源和目标的类型必须一致，copy() 函数的返回值表示实际发生复制的元素个数
 * 如果加入的两个数组切片不一样大，就会按照其中较小的那个数组切片的元素个数进行复制

```go
slice1 := []int{1, 2, 3, 4, 5} 
slice2 := []int{5, 4, 3} 
copy(slice2, slice1) // 只会复制slice1的前3个元素到slice2中 
copy(slice1, slice2) // 只会复制slice2的3个元素到slice1的前3个位置
```

虽然通过循环复制切片元素更直接，不过内置的 copy() 函数使用起来更加方便，copy() 函数的第一个参数是要复制的目标 slice，第二个参数是源 slice，两个 slice 可以共享同一个底层数组，甚至有重叠也没有问题



### 从切片中删除元素

Go语言并没有对删除切片元素提供专用的语法或者接口，需要使用切片本身的特性来删除元素，根据要删除元素的位置有三种情况，分别是从开头位置删除、从中间位置删除和从尾部删除，其中删除切片尾部的元素速度最快

#### 从开头位置删除

删除开头的元素可以直接移动数据指针

```go
a = []int{1, 2, 3} a = a[1:] // 删除开头1个元素 
a = a[N:]     // 删除开头N个元素
```

也可以不移动数据指针，但是将后面的数据向开头移动，可以用 append 原地完成（所谓原地完成是指在原有的切片数据对应的内存区间内完成，不会导致内存空间结构的变化）

```go
a = []int{1, 2, 3} a = append(a[:0], a[1:]...) // 删除开头1个元素 
a = append(a[:0], a[N:]...)     // 删除开头N个元素
```

还可以用 copy() 函数来删除开头的元素

```go
a = []int{1, 2, 3} a = a[:copy(a, a[1:])] // 删除开头1个元素 
a = a[:copy(a, a[N:])] // 删除开头N个元素
```

#### **从中间位置删除**

对于删除中间的元素，需要对剩余的元素进行一次整体挪动，同样可以用 append 或 copy 原地完成：

```go
a = []int{1, 2, 3, ...} 
a = append(a[:i], a[i+1:]...) // 删除中间1个元素 
a = append(a[:i], a[i+N:]...) // 删除中间N个元素 
a = a[:i+copy(a[i:], a[i+1:])] // 删除中间1个元素 
a = a[:i+copy(a[i:], a[i+N:])] // 删除中间N个元素
```

#### 从尾部删除

```go
a = []int{1, 2, 3} 
a = a[:len(a)-1] // 删除尾部1个元素 
a = a[:len(a)-N] // 删除尾部N个元素
```



删除开头的元素和删除尾部的元素都可以认为是删除中间元素操作的特殊情况

删除过程可以使用下图来描述

![1-1PQ3164544530](https://could-res-1252778021.file.myqcloud.com/img/1-1PQ3164544530.jpg)



Go语言中删除切片元素的本质是，以被删除元素为分界点，将前后两个部分的内存重新连接起来。

**提示**

连续容器的元素删除无论在任何语言中，都要将删除点前后的元素移动到新的位置，随着元素的增加，这个过程将会变得极为耗时

因此，当业务需要大量、频繁地从一个切片中删除元素时，如果对性能要求较高的话，就需要考虑更换其他的容器了（如双链表等能快速从删除点删除元素）





### 切片排序

#### 选择排序

概念: 通过比较，首先选出最小的数放在第一个位置上，然后在其余的数中选出次小数放在第二个位置上,依此类推,直到所有的数成为有序序列

```go
var numSlice = []int{9, 8, 7, 6, 5, 4} 
for i := 0; i < len(numSlice); i++ { 
    for j := i + 1; j < len(numSlice); j++ { 
        if numSlice[i] > numSlice[j] { 
            temp := numSlice[i] 
            numSlice[i] = numSlice[j] 
            numSlice[j] = temp 
        } 
    } 
} 
fmt.Println(numSlice)
```

#### 冒泡排序

概念:从头到尾,比较相邻的两个元素的大小,如果符合交换条件,交换两个元素的位置。 特点:每一轮比较中,都会选出一个最大的数，放在正确的位置

````go
var numSlice = []int{9, 8, 7, 6, 5, 4} 
for i := 0; i < len(numSlice); i++ { 
    for j := i + 1; j < len(numSlice); j++ { 
        if numSlice[i] > numSlice[j] { 
            temp := numSlice[i] 
            numSlice[i] = numSlice[j] 
            numSlice[j] = temp 
        } 
    } 
} 
fmt.Println(numSlice)
````

Golang 内置 Sort 包对切片进行排序

文档：

- http://docscn.studygolang.com/pkg/sort/

- https://golang.org/src/sort



**sort 升序排序**

对于 int 、 float64 和 string 数组或是切片的排序， go 分别提供了 sort.Ints() 、sort.Float64s() 和 sort.Strings() 函数， 默认都是从小到大排序

```go
intList := []int{2, 4, 3, 5, 7, 6, 9, 8, 1, 0} 
float8List := []float64{4.2, 5.9, 12.4, 10.2, 50.7, 99.9, 31.4, 27.81828, 3.14} 
stringList := []string{"a", "c", "b", "z", "x", "w", "y", "d", "f", "i"} 
sort.Ints(intList) 
sort.Float64s(float8List) 
sort.Strings(stringList)
```

**sort 降序**

Golang 的 sort 包 可 以 使 用 sort.Reverse(slice) 来调换slice.Interface.Less ，也就是比较函数，所以， int 、 float64 和 string的逆序排序函数可以这么写

```go
intList := []int{2, 4, 3, 5, 7, 6, 9, 8, 1, 0} 
float8List := []float64{4.2, 5.9, 12.4, 10.2, 50.7, 99.9, 31.4, 27.81828, 3.14} 
stringList := []string{"a", "c", "b", "z", "x", "w", "y", "d", "f", "i"} 

sort.Sort(sort.Reverse(sort.IntSlice(intList))) 
sort.Sort(sort.Reverse(sort.Float64Slice(float8List))) 
sort.Sort(sort.Reverse(sort.StringSlice(stringList)))
```



### 多维切片

声明一个多维数组的语法格式如下

```go
var sliceName [][]...[]sliceType
```

其中，sliceName 为切片的名字，sliceType为切片的类型，每个[ ]代表着一个维度，切片有几个维度就需要几个[ ]
声明一个二维切片并赋值，代码如下所示

```go
//声明一个二维切片 
var slice [][]int 
//为二维切片赋值 
slice = [][]int{{10}, {100, 200}} 

//上面的代码也可以简写为下面的样子。 
// 声明一个二维整型切片并赋值 
slice := [][]int{{10}, {100, 200}}
```

上面的代码中切片 slice 的值如下图所示

![4-1Z61416004D92](https://could-res-1252778021.file.myqcloud.com/img/4-1Z61416004D92.gif)





通过上图可以看到外层的切片包括两个元素，每个元素都是一个切片，第一个元素中的切片使用单个整数 10 来初始化，第二个元素中的切片包括两个整数，即 100 和 200

内置函数[ append() ](http://c.biancheng.net/view/28.html)的规则也可以应用到组合后的切片上，如下所示

```go
// 声明一个二维整型切片并赋值 
slice := [][]int{{10}, {100, 200}} 
// 为第一个切片追加值为 20 的元素 
slice[0] = append(slice[0], 20)
```

append() 函数处理方式，先增长切片，再将新的整型切片赋值给外层切片的第一个元素，当上面代码中的操作完成后，再将切片复制到外层切片的索引为 0 的元素，如下图所示

![4-1Z614160234963](https://could-res-1252778021.file.myqcloud.com/img/4-1Z614160234963.gif)



即便是这么简单的多维切片，操作时也会涉及众多的布局和值，在函数间这样传递数据结构会很复杂，不过切片本身结构很简单，可以用很小的成本在函数间传递。



## **map**

Go语言中 map 是一种特殊的[数据结构](http://c.biancheng.net/data_structure/)，一种元素对（pair）的无序集合，pair 对应一个 key（索引）和一个 value（值），所以这个结构也称为关联数组或字典，这是一种能够快速寻找值的理想结构，给定 key，就可以迅速找到对应的 value。

map 这种数据结构在其他编程语言中也称为字典（[Python](http://c.biancheng.net/python/)）、hash 和 HashTable 等



### 声明

一般方法

map 是引用类型，可以使用如下方式声明：

```go
var mapname map[keytype]valuetype
```

make()

```go
mapname := make(map[keytype]valuetype[, cap])
```

 * name 为 map 的变量名。
* 
  keytype 为键类型。
* 
  valuetype 是键对应的值类型。
* 
  make()函数的第二个参数cap表示 map 的容量，该参数不是必须的，获取 map 的容量不能使用 cap, cap 返回的是数组切片分配的空间大小, 根本不能用于 map

> 提示：[keytype] 和 valuetype 之间允许有空格。

在声明的时候不需要知道 map 的长度，因为 map 是可以动态增长的，未初始化的 map 的值是 nil，使用函数 len() 可以获取 map 中 pair 的数目

```go
var mapLit map[string]int 
mapLit = map[string]int{"one": 1, "two": 2} 

var mapAssigned map[string]int //mapAssigned 是 mapList 的引用，对 mapAssigned 的修改也会影响到 mapLit 的值 
mapAssigned = mapLit 

//var mapCreated map[string]float32
mapCreated := make(map[string]float32) 

mapCreated["key1"] = 4.5 
mapCreated["key2"] = 3.14159 
mapAssigned["two"] = 3 

fmt.Printf("Map literal at \"one\" is: %d\n", mapLit["one"]) 
fmt.Printf("Map created at \"key2\" is: %f\n", mapCreated["key2"]) 
fmt.Printf("Map assigned at \"two\" is: %d\n", mapLit["two"]) 
fmt.Printf("Map literal at \"ten\" is: %d\n", mapLit["ten"])
```



注意：
 	1. 可以使用 make()，但不能使用 new() 来构造 map，如果错误的使用 new() 分配了一个引用对象，会获得一个空引用的指针，相当于声明了一个未初始化的变量并且取了它的地址
 	2. 当 map 增长到容量上限的时候，如果再增加新的 key-value，map 的大小会自动加 1，所以出于性能的考虑，对于大的 map 或者会快速扩张的 map，即使只是大概知道容量，也最好先标明



用切片作为 map 的值

既然一个 key 只能对应一个 value，而 value 又是一个原始类型，那么如果一个 key 要对应多个值怎么办？例如，当我们要处理 unix 机器上的所有进程，以父进程（pid 为整形）作为 key，所有的子进程（以所有子进程的 pid 组成的切片）作为 value。通过将 value 定义为 []int 类型或者其他类型的切片，就可以优雅的解决这个问题，示例代码如下所示：

```go
mp1 := make(map[int][]int) 
mp2 := make(map[int]*[]int)
```

### 判断某个键是否存在

Go 语言中有个判断 map 中键是否存在的特殊写法，格式如下:

```go
value, ok := map 对象[key]
```

demo：

```go
scoreMap := make(map[string]int) 
scoreMap["张三"] = 90 scoreMap["小明"] = 100 

// 如果 key 存在 ok 为 true,v 为对应的值；不存在 ok 为 false,v 为值类型的零值 v, 
ok := scoreMap["张三"]
if ok { 
    fmt.Println(v) } 
else { 
    fmt.Println("查无此人") 
}
```

### 遍历map

Go 语言中使用 for range 遍历 map

```go
func main() { 
    scoreMap := make(map[string]int) 
    scoreMap["张三"] = 90 
    scoreMap["小明"] = 100 
    scoreMap["娜扎"] = 60 
    
    for k, v := range scoreMap { 
        fmt.Println(k, v)
    } 
}
```

只想遍历 key 的时候，可以按下面的写法：

```go
func main() {
     scoreMap := make(map[string]int)
     scoreMap["张三"] = 90
     scoreMap["小明"] = 100
     scoreMap["娜扎"] = 60 
    
     for k := range scoreMap {
         fmt.Println(k)
     }
}
```

注意： 遍历 map 时的元素顺序与添加键值对的顺序无关。

**按照指定顺序遍历 map**

```go
func main() { 
    rand.Seed(time.Now().UnixNano()) // 初始化随机数种子 
    var scoreMap = make(map[string]int, 200) 

    for i := 0; i < 100; i++ { 
        key := fmt.Sprintf("stu%02d", i) // 生成 stu 开头的字符串 
        value := rand.Intn(100) // 生成 0~99 的随机整数 
        scoreMap[key] = value 
    } // 取出 map 中的所有 key 存入切片 keys 
        
    var keys = make([]string, 0, 200) 
    for key := range scoreMap { 
        keys = append(keys, key) 
    } 
    // 对切片进行排序 
    sort.Strings(keys) 
    // 按照排序后的 key 遍历 
    map for _, key := range keys { 
        fmt.Println(key, scoreMap[key]) 
    } 
}
```



### 删除键值对

使用 delete()内建函数从 map 中删除一组键值对，delete()函数的格式如下：

```go
delete(map 对象, key)
```

- map 对象: 表示要删除键值对的 map 对象

- key: 表示要删除的键值对的键

demo：

```go
func main(){ 
    scoreMap := make(map[string]int) 
    scoreMap["张三"] = 90 
    scoreMap["小明"] = 100 
    scoreMap["娜扎"] = 60 
    delete(scoreMap, "小明")// 将小明 :100 从 map 中删除 

    for k,v := range scoreMap{ 
        fmt.Println(k, v) 
    } 
}
```



### **清空所有元素**

Go语言中并没有为 map 提供任何清空所有元素的函数、方法，清空 map 的唯一办法就是重新 make 一个新的 map，不用担心垃圾回收的效率，Go语言中的并行垃圾回收效率比写一个清空函数要高效的多

**元素为 map 的切片**

```go
func main() { 
    var mapSlice = make([]map[string]string, 3) 
    for index, value := range mapSlice { 
        fmt.Printf("index:%d value:%v\n", index, value) 
    } 
    fmt.Println("after init") // 对切片中的 map 元素进行初始化 
    mapSlice[0] = make(map[string]string, 10) 
    mapSlice[0]["name"] = "小王子" 
    mapSlice[0]["password"] = "123456" 
    mapSlice[0]["address"] = "海淀区" 

    for index, value := range mapSlice { 
        fmt.Printf("index:%d value:%v\n", index, value) 
    } 
}
```

**值为切片类型的 map**

```go
func main() { 
    var sliceMap = make(map[string][]string, 3) 
    fmt.Println(sliceMap) 
    fmt.Println("after init") 

    key := "中国" 
    value, ok := sliceMap[key] 
    if !ok { 
        value = make([]string, 0, 2) 
    } 
    value = append(value, "北京", "上海") 
    sliceMap[key] = value fmt.Println(sliceMap) 
}
```



### map的多键索引 - 待完善

sync.Map（在并发环境中使用的map）

- Go语言中的 map 在并发情况下，只读是线程安全的，同时读写是线程不安全的。

- 如果使用了两个并发函数不断地对 map 进行读和写而发生了竞态问题，map 内部会对这种并发操作进行检查并提前发现，所以会导致运行报错

需要并发读写时，一般的做法是加锁，但这样性能并不高，Go语言在 1.9 版本中提供了一种效率较高的并发安全的 sync.Map，sync.Map 和 map 不同，不是以语言原生形态提供，而是在 sync 包下的特殊结构

sync.Map 有以下特性：

- 无须初始化，直接声明即可。
- sync.Map 不能使用 map 的方式进行取值和设置等操作，而是使用 sync.Map 的方法进行调用，Store 表示存储，Load 表示获取，Delete 表示删除。
- 使用 Range 配合一个回调函数进行遍历操作，通过回调函数返回内部遍历出来的值，Range 参数中回调函数的返回值在需要继续迭代遍历时，返回 true，终止迭代遍历时，返回 false。

并发安全的 sync.Map 演示代码如下：

```go
package main

import ( "fmt" "sync" ) 

func main() {
    var scene sync.Map 
    // 将键值对保存到sync.Map 
    scene.Store("greece", 97) 
    scene.Store("london", 100) 
    scene.Store("egypt", 200) 

    // 从sync.Map中根据键取值 
    fmt.Println(scene.Load("london")) 
    / 根据键删除对应的键值对 
    scene.Delete("london") 
    // 遍历所有sync.Map中的键值对 
    scene.Range(func(k, v interface{}) bool { 
        fmt.Println("iterate:", k, v)
        return true 
    }) 
}
```

sync.Map 没有提供获取 map 数量的方法，替代方法是在获取 sync.Map 时遍历自行计算数量，sync.Map 为了保证并发安全有一些性能损失，因此在非并发情况下，使用 map 相比使用 sync.Map 会有更好的性能



## Go语言嵌入类型 - 待完善





























