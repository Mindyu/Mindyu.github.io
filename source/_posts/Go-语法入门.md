---
title: Go 语法入门
date: 2019-01-30 16:32:12
tags: [golang]
categories: [Go 学习]
---

### Go 简介

​	Go语言是谷歌推出的一种全新的开源的编程语言，可以在不损失应用程序性能的情况下降低代码的复杂性，让构造简单、可靠且高效的软件变得容易 。 Go的目标是希望提升现有编程语言对程序库等依赖性(dependency)的管理，这些软件元素会被应用程序反复调用。由于存在并行编程模式，因此这一语言也被设计用来解决多处理器的任务。 

<!--more-->

### 特色

简洁 快速 安全

并行 有趣 开源

内存管理 数组安全 编译迅速



### 基础语法

```go
package main // 包声明

// 引入包
import "fmt"

// 这种因式分解关键字的写法一般用于声明全局变量
var (
	a int
	b bool
)

// main 函数：程序的入口
func main() { // 注：{ 不能在单独的行上

	// 注：一行代表一个语句结束，推荐不使用 ; 结尾。只有多个语句在一行的时候使用分号分隔

	// 变量声明
	/*
	var a int = 10
	var b = 10		// 自行判断变量类型
	c := 10			// 省略var关键字，但只能在函数体类使用（推荐）

	// 支持多变量声明
	var c, d int = 1, 2
	var e, f = 123, "hello"
	*/
	// 注：如果一个局部变量声明之后并未使用，会报错

	// 定义常量
	const LENGTH int = 10
	// 枚举常量
	const (
		Unknown = 0
		Female  = 1
		Male    = 2
	)

	// iota 特殊常量，在 const关键字出现时将被重置为 0(const 内部的第一行之前)，
	// const 中每新增一行常量声明将使 iota 计数一次(iota 可理解为 const 语句块中的行索引)。
	const (
		a  = 0    //0，iota += 1
		b  = iota //1
		c         //2
		d  = "ha" //独立值，iota += 1
		e         //"ha"   iota += 1
		f  = 100  //iota +=1
		g         //100  iota +=1
		h  = iota //7,恢复计数
		i1        //8
	)
	fmt.Println(a, b, c, d, e, f, g, h, i1)
	println()

	/* switch 用法：
	1. 支持多条件匹配
	2. 不同的 case 之间不使用 break 分隔，默认只会执行一个 case。
	3. 如果想要执行多个 case，需要使用 fallthrough 关键字，也可用 break 终止。
	*/
	var grade = "B"
	var marks = 90

	switch marks {
	case 90:
		grade = "A"
	case 80:
		grade = "B"
	case 50, 60, 70:
		grade = "C"
	default:
		grade = "D"
	}
	fmt.Printf("你的等级是 %s\n", grade)

	// switch 语句还可以被用于 type-switch 来判断某个interface变量中实际存储的变量类型。
	var x interface{}

	switch i := x.(type) {
	case nil:
		fmt.Printf("x 的类型 :%T", i)
	case int:
		fmt.Printf("x 是 int 型")
	case float64:
		fmt.Printf("x 是 float64 型")
	case func(int) float64:
		fmt.Printf("x 是 func(int) 型")
	case bool, string:
		fmt.Printf("x 是 bool 或 string 型")
	default:
		fmt.Printf("未知型")
	}
	fmt.Println()

	/* 循环控制
	1.支持 break,continue,goto
	2.for 可实现类似while的语法
	*/
	var i, j int
	// 打印100以内的素数
	for i = 2; i < 100; i++ {
		for j = 2; j <= (i / j); j++ {
			if i%j == 0 {
				break // 如果发现因子，则不是素数
			}
		}
		if j > (i / j) {
			fmt.Printf("%d  是素数\n", i)
		}
	}
	i = 1;
	j = 5
	for i <= j {
		if i == 4 {
			i++
			continue
		}
		fmt.Print(i)
		i++
	}
	fmt.Println()

	fmt.Println(max(2, 5))
	fmt.Println(swap("World", "Hello"))

	var c1 Circle
	c1.radius = 10.00
	fmt.Println("圆的面积 = ", c1.getArea())

	// nextNumber 为一个函数，函数 i 为 0
	nextNumber := getSequence()

	/* 调用 nextNumber 函数，i 变量自增 1 并返回 */
	fmt.Println(nextNumber())
	fmt.Println(nextNumber())

	/* 创建新的函数 nextNumber1，并查看结果 */
	nextNumber1 := getSequence()
	fmt.Println(nextNumber1())
	fmt.Println(nextNumber1())

	// 数组 var variable_name [SIZE] variable_type
	var balance = []float32{1000.0, 2.0, 3.4, 7.0, 50.0}
	fmt.Println(balance[1])

	// 多维数组 var variable_name [SIZE1][SIZE2]...[SIZEN] variable_type
	var arr = [3][4]int{
		{0, 1, 2, 3},   /*  第一行索引为 0 */
		{4, 5, 6, 7},   /*  第二行索引为 1 */
		{8, 9, 10, 11}, /* 第三行索引为 2 */
	}
	fmt.Println(arr[2][1]) // 9

	var array = []int{1, 2, 3, 4}
	// 未定义长度的数组只能传给不限制数组长度的函数
	setArray(array)
	// 定义了长度的数组只能传给限制了相同数组长度的函数
	var array2 = [5]int{1, 2, 3, 4, 5}
	setArray2(array2)

	// 指针
	i = 20       // 重新赋值
	var ptr *int // 声明指针变量
	if ptr == nil { // nil 表示空指针
		fmt.Println("当前ptr为空指针")
	}
	ptr = &i // ptr 指针指向变量i
	fmt.Println("变量地址：", ptr, "\n变量的值：", *ptr)

	// 指针数组 此处的max必须为常量
	const MAX = 4
	var ptr2 [MAX]*int
	for i = 0; i < MAX; i++ {
		ptr2[i] = &array[i] // 将数组中每个元素的地址赋值给指针数组
	}
	for i = 0; i < MAX; i++ {
		fmt.Printf("a[%d] = %d\n", i, *ptr2[i])
	}

	// 指针的指针
	var pptr **int
	pptr = &ptr
	fmt.Printf("指向指针的指针变量 **pptr = %d\n", **pptr)

	// 结构体
	circle := Circle{12}		// 省略key，以默认的顺序初始化字段
	circle = Circle{radius: 5}		// 结构体初始化 key => value 格式、字段可以省略
	fmt.Println("修改前：", circle.radius)
	alert(&circle)
	fmt.Println("修改后：", circle.radius)
    
    // 类型转换 type_name(expression) type_name为类型，expression为表达式。
	i = 3
	j = 4
	f1 := float32(i)/float32(j)	// i和j都需要转型，否则Invalid operation: float32(i)/j (mismatched types float32 and int)
	fmt.Println( f1 )			// 0.75
	fmt.Printf("%f",f1)			// 0.750000
}

/* 函数定义
func function_name( [parameter list] ) [return_types] {
   函数体
}
*/
func max(a, b int) int {
	if a > b {
		return a
	} else {
		return b
	}
}

// 可返回多个值
func swap(x, y string) (string, string) {
	return y, x
}

/* 定义结构体 */
type Circle struct {
	radius float64
}

/*
Go 语言中同时有函数和方法。一个方法就是一个包含了接受者的函数，
接受者可以是命名类型或者结构体类型的一个值或者是一个指针。所有给定类型的方法属于该类型的方法集。
func (variable_name variable_data_type) function_name() [return_type]{
   // 函数体
}
*/
//该 method 属于 Circle 类型对象中的方法
func (c Circle) getArea() float64 {
	//c.radius 即为 Circle 类型对象中的属性
	return 3.14 * c.radius * c.radius
}

/* 函数闭包
Go 语言支持匿名函数，可作为闭包。匿名函数是一个"内联"语句或表达式。匿名函数的优越性在于可以直接使用函数内的变量，不必申明。
以下实例中，我们创建了函数 getSequence() ，返回另外一个函数。该函数的目的是在闭包中递增 i 变量
*/
func getSequence() func() int {
	i := 0
	return func() int {
		i += 1
		return i
	}
}

// 未定义长度的数组只能传给 不限制数组长度 的函数
func setArray(params []int) {
	fmt.Println("params array length of setArray is : ", len(params))
}

// 定义了长度的数组只能传给限制了相同数组长度的函数
func setArray2(params [5]int) {
	fmt.Println("params array length of setArray2 is : ", len(params))
}

// 指针版变量交换，不支持函数重载
func swap1(x *int, y *int) {
	*x, *y = *y, *x
}

// 结构体指针，引用传递，此时的形参和实参指向的是同一个对象
func alert(circle *Circle) {
	circle.radius = 20
}
```



### 进阶语法

#### 切片 Slice

​	对数组功能的增强，可以理解为动态数组，弥补了数组长度固定的限制，同时可以向切片中追加元素。

```go
package main

import "fmt"

func main() {
	arr := []int{1,2,3,4,5}

	// 切片的定义	var identifier []type
	var s1 []int
	if s1 == nil {
		printSlice(s1)		// len=0 cap=0 slice=[]
		fmt.Println("当前切片s1为空切片")
	}
	// make() 函数来创建切片 参数:类型,长度,容量（可选）
	var s2 []int = make([]int, 5)
	s3 := make([]int, 5, 10)

	// 直接初始化 此时len=cap=3
	s1 =[] int {1,2,3}
	// 初始化为数组arr的引用
	s1 = arr[:]
	// 将arr中从下标startIndex到endIndex-1下的元素创建为一个新的切片(startIndex、endIndex可缺省)
	s1 = arr[1:5]

	// 注：通过数组创建的只是数组的引用，修改数组时，切片的值也会修改
	arr[4] = 6
	printSlice(s1)			// len=4 cap=4 slice=[2 3 4 6]
	printSlice(s2)			// len=5 cap=5 slice=[0 0 0 0 0]
	printSlice(s3)			// len=5 cap=10 slice=[0 0 0 0 0]

	// 切片截取（通过[low_index:upper_index],可缺省，low ~ (upper-1)下标）
	printSlice(s1[1:4])		// len=3 cap=3 slice=[3 4 6]

	var s4 []int
	// 切片的追加
	s4 = append(s4, 0)
	s4 = append(s4, 1)
	printSlice(s4)		// len=2 cap=2 slice=[0 1]

	// 切片的拷贝
	s5 := make([]int, len(s4), (cap(s4))*2)
	copy(s5, s4)		// 拷贝s4的内容到s5
	printSlice(s5)
}

func printSlice(x []int) {
	// len 获取切片长度，cap 切片容量
	fmt.Printf("len=%d cap=%d slice=%v\n", len(x), cap(x), x)
}
```



#### 范围 Range

​	Go 语言中 range 关键字用于 for 循环中迭代数组(array)、切片(slice)、通道(channel)或集合(map)的元素。在数组和切片中它返回元素的索引和索引对应的值，在map集合中返回 key-value 对的 key 值。 

```go
package main

import "fmt"

func main() {

	arr := []int{1,2,3,4,5}
	sum := 0
	// range 可以返回数组or切片中元素的下标和元素的值，此时如果不需要使用下标时，可以用_表示省略
	for index, num := range arr {
		fmt.Println(index, num)
		sum += num
	}
	fmt.Println("sum:", sum)

	// range 也可以用在map的键值对上。
	kvs := map[string]string{"a": "apple", "b": "banana"}
	for k, v := range kvs {
		fmt.Printf("%s -> %s\n", k, v)
	}

	// range 也可以用来枚举Unicode字符串。第一个参数是字符的索引，第二个是字符（Unicode的值）本身。
	for i, c := range "go" {
		fmt.Println(i, c)
	}
}

/*
0 1
1 2
2 3
3 4
4 5
sum: 15
a -> apple
b -> banana
0 103
1 111
*/
```



#### 集合 Map

​	存储的 key-value 键值对，使用 hash 表实现。

```go
package main

import "fmt"

func main() {

	// 声明map,默认为nil	map[key_type]value_type
	var countryCapitalMap map[string]string
	if countryCapitalMap == nil {
		fmt.Println("当前集合为 nil")
	}
	// 使用make函数初始化map
	countryCapitalMap = make(map[string]string)
	if countryCapitalMap != nil {
		fmt.Println("当前集合不为 nil")
	}

	// map插入key - value对,各个国家对应的首都
	countryCapitalMap[ "France" ] = "Paris"
	countryCapitalMap[ "Italy" ] = "罗马"
	countryCapitalMap[ "Japan" ] = "东京"
	countryCapitalMap[ "India " ] = "新德里"

	// map集合中range返回key
	for country := range countryCapitalMap {
		fmt.Println(country, "首都是", countryCapitalMap[country])
	}

	// 查看元素key在集合中是否存在
	captial, ok := countryCapitalMap[ "美国" ]
	if ok {
		fmt.Println("美国的首都是", captial)
	} else {
		fmt.Println("美国的首都不存在")
	}
}

/*
当前集合为 nil
当前集合不为 nil
France 首都是 Paris
Italy 首都是 罗马
Japan 首都是 东京
India  首都是 新德里
美国的首都不存在
*/
```



#### 接口 interface

​	接口把所有的具有共性的方法定义在一起，任何其他类型只要实现了这些方法就是实现了这个接口。 

```go
package main

import "fmt"

// 定义接口
type Phone interface {
	call()	// 接口定义的方法
}

type NokiaPhone struct {
}

/* 接口的实现方法
func (struct_name_variable struct_name) method_name() [return_type] {
	// 实现
}
*/
func (nokiaPhone NokiaPhone) call() {
	fmt.Println("I am Nokia, I can call you!")
}

type IPhone struct {
}

func (iPhone IPhone) call() {
	fmt.Println("I am iPhone, I can call you!")
}

func main() {
	var phone Phone

	phone = new(NokiaPhone)
	phone.call()

	phone = new(IPhone)
	phone.call()
}
```



#### 错误处理

​	通过内置的错误接口提供错误处理机制。

​	error 内置接口：

```go
type error interface {
    Error() string
}
```

​	使用 errors.New 可返回一个错误信息。

```go
package main

import (
	"errors"
	"fmt"
	"math"
)

func main() {

	result, err := sqrt(-2)
	if err == nil {
		fmt.Println(result)
	} else {
		fmt.Println(err.Error())	// 输出错误信息
	}

	result, err = sqrt(8)
	if err == nil {
		fmt.Println(result)
	} else {
		fmt.Println(err.Error())
	}
}

func sqrt(f float64) (float64, error) {
	if f < 0 {
		return 0, errors.New("math: square root of negative number")
	}
	return math.Sqrt(f), nil
}

/*
math: square root of negative number
2.8284271247461903
*/
```

​	通过实现上述的 Error 接口方法，来生成发生错误时返回的信息。

```go
package main

import "fmt"

type Man struct {
	age int
}

// 实现 Error 接口方法 
func (man Man) Error() string {
	strFmt := "Man.age 为%d, age 不能为负数"
	return fmt.Sprintf(strFmt, man.age)
}

// 初始化 Man 对象
func initMan(age int) (res Man, errorMassage string) {
	man := Man{age}
	if age < 0 {
		errorMassage = man.Error()
		return
	}
	return man, ""
}

func main() {
	man, errorMassage := initMan(10)
	if errorMassage == "" {
		fmt.Println(man.age)
	} else {
		fmt.Println(errorMassage)
	}

	if _, errorMassage := initMan(-10); errorMassage != "" { 
		fmt.Println(errorMassage)
	}
}

/*
10
Man.age 为-10, age 不能为负数
*/
```



#### Go 并发

##### goroutine 

​	通过 go 关键字来开启 goroutine 即可。以一个不同的、新创建的 goroutine 来执行一个函数。 同一个程序中的所有 goroutine 共享同一个地址空间。 goroutine 是轻量级线程，goroutine 的调度是由 Golang 运行时进行管理的。  

```go
package main

import (
	"fmt"
	"time"
)

func say(s string) {
	for i := 0; i < 5; i++ {
		time.Sleep(100 * time.Millisecond)
		fmt.Println(s)
	}
}

func main() {
    // 此时的 hello和world 输出时，没有固定的顺序
	go say("world")
	say("hello")
}
```



##### 通道

​	通道可用于两个 goroutine 之间通过传递一个指定类型的值来同步运行和通讯。操作符  `<-` 用于指定通道的方向，发送或接收。如果未指定方向，则为双向通道。 

```go
# 通道的声明，默认情况下通道是不带缓冲区的，发送端发送数据，必须等到接收端接受数据。
ch := make(chan int)
```

​	两个线程分别计算数组的前后部分，然后通过通道将数据返回给主线程，再求和


```go
package main

import "fmt"

func sum(s []int, c chan int) {
	sum := 0
	for _, v := range s {	// range 返回索引和值
		sum += v
	}
	c <- sum // 把 sum 发送到通道 c
}

func main() {
	s := []int{7, 2, 8, -9, 4, 0}

	// 声明通道
	c := make(chan int)
	go sum(s[:len(s)/2], c) // s[:len(s)/2] 表示数组的前半部分
	go sum(s[len(s)/2:], c)

	x, y := <-c, <-c // 从通道 c 中接收

	fmt.Println(x, y, x+y)
}

```



##### 带缓冲区的通道

​	带缓冲区的通道允许发送端的数据的发送和接收端的数据的获取处于**异步**状态，即发送端可以一直发送数据到缓冲区里，除非缓冲区满了，则会阻塞。此时就必须有接收端进行数据的接受。

​	**注意**：如果通道不带缓冲，发送方会阻塞直到接收方从通道中接收了值。如果通道带缓冲，发送方则会阻塞直到发送的值被拷贝到缓冲区内；如果缓冲区已满，则意味着需要等待直到某个接收方获取到一个值。接收方如果没有值可以接受则会一直阻塞。

```go
package main

import "fmt"

func main() {

	// 缓冲区大小为3的通道
	ch := make(chan int, 3)

	// 因为 ch 是带缓冲的通道，我们可以同时发送多个数据
	// 而不用立刻需要去同步读取数据
	ch <- 1
	ch <- 2
	ch <- 3
	// 关闭通道
	close(ch)

	fmt.Println("第一个数据：", <-ch)

	// 通过 range 遍历通道内的数据
	for i := range ch {
		fmt.Println(i)
	}
}

/*
第一个数据： 1
2
3
*/
```



------



> 菜鸟教程 http://www.runoob.com/go/go-tutorial.html