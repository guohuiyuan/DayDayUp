<!--
 * @Description: 
 * @Author: Alone
 * @Date: 2022-05-11 20:38:35
 * @LastEditors: Alone
 * @LastEditTime: 2022-05-16 20:49:45
-->
# 基本语法

## 编译运行

```go
// 编译
go build hello.go

// 运行
./hello
Hello,world.

```

## 词法单元

token是构成源程序的基本不可再分割的单元。编译器编译源程序的第一步就是将源程序分割为一个个独立的token.

Go语言的token可以分为关键字、标识符、操作符、分隔符和字面常量等。

标识符构成规则：开头一个字符必须是字母或下划线，后面跟任意多个字符、数字或下划线，且区分大小写。

内置关键字&&标识符&&操作符:

![](https://i0.hdslb.com/bfs/album/aee2e7f8a7cd4a9ff0c1447031b810aa886d4f35.jpg)


## 变量声明

完整声明: `var varName dataType [ = value]` 

短类型声明: `varName := value` 

## 变量使用注意事项

go变量不能声明后不使用, :=只能出现在函数内

布尔型数据和整型数据不能进行相互转换。布尔型变量不指定初始化值时为false。

不同类型的整型必须进行强制类型转换。

浮点数字面量默认为float64类型。

字符串：

1. 字符串可以通过索引访问字节单元，但不能修改

```go
var a = "hello,world"
b := a[0]
a[1] = 'a' // error
```

2. 字符串转换为切片[]byte(s)时，每转换一次都要复制一遍内容。
3. 字符串尾部不包含Null字符
4. 字符串底层实现是一个二元数据结构，一个是指针指向字节数组起点，另一个是长度。
5. 基于字符串创建的切片和原字符串指向相同的底层字符数组，不能修改。对字符串的切片操作返回的是string，不是slice

rune是表示Unicode编码字符的，在Go内部是int32类型的别名，占4个字节。

## 复合数据类型

### 指针

1. 指针声明类型为`*T` `*T` 出现在 = 左边表示指针声明，`*T` 出现在 = 右边表示取指针指向的值。

```go
var a = 11 
p := &a
```

2. 结构体指针访问结构体字段使用 . 点操作符

```go
type User struct {
    name string
    age int
}

andes := User {
    name: "andes",
    age: 18,
}
p := &andes
fmt.Println(p.name)
```

3. 不支持指针运算
4. 函数中允许返回局部变量的地址，编译器使用"栈逃逸"机制将这种局部变量的空间分配在堆上。

### 数组

声明数组：

```go
var arr [2]int
array := [...]float64{7.0, 8.5, 9.1}
```

1. 数组创建完长度就固定了，不可以追加元素
2. 数组是值类型的，数组赋值或作为函数参数都是值拷贝
3. 数组长度是数组类型的组成部分
4. 可以创建切片slice

### 切片slice

切片中维护3个元素，指向底层数组的指针，切片元素数量和底层数组容量。

创建方式：

1. 由数组创建: `array[b:e]` array为数组名，b为开始索引，e为结束索引，范围左闭右开
2. 由make创建: `a:=make([]int,10)` 

支持的操作:

- `len()` 返回切片长度
- `cap()` 返回切片底层数组容量
- `append()` 追加元素
- `copy()` 复制

### 字典map

创建方式:

1. 使用字面量创建： `ma := map[string]int{"a": 1, "b": 2}` 
2. 使用make函数创建: `make(map[K]T)`

支持的操作：

- 单个键访问: `mapName[key]` 
- 更新单个键: `map[1] = "tom" `  
- 访问某个键: `a := map[1] ` 
- 删除某个键： `delete(mapName,key)` 
- 返回键值对数量: `len()` 

注意事项：

- go内置的map不是线程安全的，并发安全的map需要使用标准包sync中的map
- 不要直接修改map value中某个元素的值，如果要修改，必须整体赋值。

### 结构体struct

struct字面量:

```go
struct {
    fieldName fieldType
    fieldName fieldType
    fieldName fieldType
}
```

自定义struct类型:

```go
type TypeName struct {
    fieldName fieldType
    fieldName fieldType
    fieldName fieldType
}
```

初始化:

```go
type Person struct {
    Name string
    Age int
}

p := &Person{
    Name: "tata",
    Age: 12,
}
```

## 流程控制

1. if-else:

```go
if x := f(); x < y {
    return x
} else if x > z {
    return z
} else {
    return y
}
```

2. switch:

```go
switch {
    case score >= 90:
        grade = 'A'
    case score >= 80:
        grade = 'B'
    case score >= 70:
        grade = 'C'
    case score >= 60:
        grade = 'D'
    default:
        grade = 'F'
}
fmt.Printf("grade=%c\n", grade) // grade = B
```

3. for:

`for init; condition; post {}` : for循环

`for condition {}` : while循环

`for {}` ：while 1 死循环

## 下划线

在import中，`import _ 包路径`是只执行该包中的init函数。

下划线在代码中:`f, _ := os.Open("/Users/***/Desktop/text.txt")` 表示忽略这个变量
 