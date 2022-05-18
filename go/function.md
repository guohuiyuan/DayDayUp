<!--
 * @Description: 
 * @Author: Alone
 * @Date: 2022-05-12 21:54:09
 * @LastEditors: Alone
 * @LastEditTime: 2022-05-16 20:45:27
-->
# 函数

函数首字母大写时其他包可见，小写时只有相同包可以访问。

```
func funcName(param-list)(result-list) {
    function-body
}
```

1. 函数可以没有入参，也可以没有返回值
2. 多个相邻的相同类型参数可以简写 // a int, b int 简写为 a,b int
3. 支持有名的返回值，默认初始化为类型零值
4. 不支持默认值参数
5. 不支持函数重载
6. 不支持命名函数嵌套定义。
7. 支持多值返回
8. 函数实参到形参的传递是值拷贝。

```go
package main

import (
	"fmt"
)

func main() {
	e := 10
	e = chvalue(e)
	fmt.Print("e1= ")
	fmt.Println(e) // 11

	chpointer(&e)
	fmt.Print("e2= ")
	fmt.Println(e) // 12
}

func chvalue(a int) int {
	a = a + 1
	return a
}

func chpointer(a *int) {
	*a = *a + 1
	return
}

```

不定参数:

1. 所有不定参数的类型必须相同
2. 不定参数必须是函数的最后一个参数
3. 不定参数名在函数体内相当于切片，适用于切片操作
4. 切片可以作为参数传递给不定参数，切片名后要加 ...
5. 形参为不定参数的函数和形参为切片的函数类型不相同。

函数类型：

函数类型是函数定义首行去掉函数名，参数名和 {}

两个函数类型相同的条件是有相同的形参列表和返回值列表（列表元素的次序、个数和类型都相同），形参名可以不同。

## defer

defer关键字用于注册延迟调用，这些调用以先进后出的顺序在函数返回前被执行。

defer后必须是函数或方法调用，不能是语句，否则会报 expression in defer must be function call错误。

defer函数的实参在注册时通过值拷贝传递，必须先注册再执行，如果defer位于return之后则不执行，主动退出(`os.Exit(int)` )时不执行。

一般defer语句放在错误检查语句后。

## 闭包

闭包是由函数及其相关引用环境组合而成的实体，一般通过在匿名函数中引用外部函数或包全局变量构成。闭包=函数+引用环境。

闭包对闭包外的环境引入是直接引用，编译器会检测到闭包，会将闭包引用的外部变量分配到堆上。

如果函数返回的闭包引用了该函数的局部变量（参数或函数内部变量）：

1. 多次调用该函数，返回的多个闭包所引用的外部变量是多个副本，原因是每次调用函数都会为局部变量分配内存。
2. 用一个闭包函数多次，如果该闭包修改了其引用的外部变量，则每一次调用该闭包对该外部变量都有影响，因为闭包函数共享外部引用。

举个例子：

```go
package main

func fa(a int) func(i int) int {

	return func(i int) int {
		println(&a, a)
		a = a + i
		return a
	}
}

func main() {
	// f引用的外部闭包环境包括本次函数调用的形参a的值1
	f := fa(1)
	// g引用的外部闭包环境包括本次函数调用的形参a的值1
	g := fa(1)
	println(f(1))
	// 多次调用f引用的是同一个副本a
	println(f(1))
	println(g(1))
	println(g(1))
}

// 0xc00003df60 1
// 2
// 0xc00003df60 2
// 3
// 0xc00003df58 1
// 2
// 0xc00003df58 2
// 3

```

```go
package main

var (
	a = 0
)

func fa() func(i int) int {

	return func(i int) int {
		println(&a, a)
		a = a + i
		return a
	}
}

func main() {
	// f引用的外部闭包环境包括全局变量a的值1
	f := fa()
	// g引用的外部闭包环境包括全局变量a的值1
	g := fa()
	// f,g引用的是同一个副本a
	println(f(1))
	println(f(1))
	println(g(1))
	println(g(1))
}

// 0x982e00 0
// 1
// 0x982e00 1
// 2
// 0x982e00 2
// 3
// 0x982e00 3
// 4

```

对象是附有行为的数据，闭包是附有数据的行为。

## panic和recover

这两个函数用来处理Go运行时错误。panic用来主动抛出错误,recover用来捕获panic抛出的错误。

函数签名如下：

```go
panic(i interface{})
recover() interface{}
```

程序主动调用和产生运行时错误时都可以引发panic。发生panic后，程序会从调用panic的函数位置或者发生panic的地方立即返回，逐层向上执行函数的defer语句，然后逐层打印函数调用堆栈，直到被recover捕获或运行到最外层函数而退出。

recover只有在defer后面的函数体内被直接调用才能捕获panic终止异常，否则返回nil,异常继续向外传递。

```go
defer func() {
    println("defer inner")
    recover()
}
```

可以有连续多个panic被抛出，但只能捕获最后一次panic.init函数引发的panic只能在init函数中被捕获。函数不能捕获内部新启动的goroutine抛出的panic.

## init和main函数

init:

1. init函数是用于程序执行前做包的初始化的函数，比如初始化包里的变量等
2. 每个包可以拥有多个init函数
3. 包的每个源文件也可以拥有多个init函数
4. 同一个包中多个init函数的执行顺序go语言没有明确的定义(说明)
5. 不同包的init函数按照包导入的依赖关系决定该初始化函数的执行顺序
6. init函数不能被其他函数调用，而是在main函数执行之前，自动被调用

main:

```
// Go语言程序的默认入口函数(主函数)：func main()
// 函数体用｛｝一对括号包裹。

func main(){
    //函数体
}
```

执行顺序:

1. 同一文件中，从上至下
2. 同一个包中，按文件名字符串比较从小到大
3. 不同包中，如果没有相互依赖则先import的后调用，如果存在依赖，则先调用最早依赖的package中的init.



