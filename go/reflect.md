# 1. 反射
反射是指在程序运行期对程序本身进行访问和修改的能力

1.1.1. 变量的内在机制
* 变量包含类型信息和值信息
* 类型信息：是静态的元信息，是预先定义好的
* 值信息：是程序运行过程中动态改变的

1.1.2. 反射的使用
* reflect包封装了反射相关的方法
* 获取类型信息：reflect.TypeOf，是静态的
* 获取值信息：reflect.ValueOf，是动态的

1.1.3. 空接口与反射
* 反射可以在运行时动态获取程序的各种详细信息

* 反射获取interface类型和数值信息

```
package main

import (
   "fmt"
   "reflect"
)

//反射获取interface类型信息

func main() {
   var x float64 = 3.4
   t := reflect.TypeOf(x)
   v := reflect.ValueOf(x)
   fmt.Println("类型是：", t,"值是：",v)
   k := t.Kind()
   fmt.Println("具体类型为：",k)
}
```

* 反射修改值信息

```
package main

import (
    "fmt"
    "reflect"
)

//反射修改值
func reflect_set_value(a interface{}) {
    v := reflect.ValueOf(a)
    k := v.Kind()
    switch k {
    case reflect.Float64:
        // 反射修改值
        v.SetFloat(6.9)
        fmt.Println("a is ", v.Float())
    case reflect.Ptr:
        // Elem()获取地址指向的值
        v.Elem().SetFloat(7.9)
        fmt.Println("case:", v.Elem().Float())
        // 地址
        fmt.Println(v.Pointer())
    }
}

func main() {
    var x float64 = 3.4
    // 反射认为下面是指针类型，不是float类型
    reflect_set_value(&x)
    fmt.Println("main:", x)
}
```

1.1.4. 结构体与反射

```
package main

import(
	"fmt"
	"reflect"
	"strings"
	"unicode"
)
type book struct {
	ID         uint64 `db:"id"`
	BookReview string `db:"bookreview"`
}

func main(){
	objptr := &book{

	}
	table := "book"
	var (
		tags  = tags(objptr)
		kinds = kinds(objptr)
		top   = len(tags) - 1
		cmd   = []string{}
	)
	if unicode.IsDigit([]rune(table)[0]) {
		table = "[" + table + "]"
	} else {
		table = "'" + table + "'"
	}
	cmd = append(cmd, "CREATE TABLE IF NOT EXISTS")
	cmd = append(cmd, table)
	cmd = append(cmd, "(")
	if top == 0 {
		cmd = append(cmd, tags[0])
		cmd = append(cmd, kinds[0])
		cmd = append(cmd, "PRIMARY KEY")
		cmd = append(cmd, "NOT NULL);")
	} else {
		for i := range tags {
			cmd = append(cmd, tags[i])
			cmd = append(cmd, kinds[i])
			switch i {
			default:
				cmd = append(cmd, "NULL,")
			case 0:
				cmd = append(cmd, "PRIMARY KEY")
				cmd = append(cmd, "NOT NULL,")
			case top:
				cmd = append(cmd, "NULL)")
			}
		}
	}
	fmt.Println(strings.Join(cmd, " ") + ";")
}

// tags 反射 返回结构体对象的 tag 数组
func tags(objptr interface{}) []string {
	var tags []string
	elem := reflect.ValueOf(objptr).Elem()
	for i, flen := 0, elem.Type().NumField(); i < flen; i++ {
		t := elem.Type().Field(i).Tag.Get("db")
		if t == "" {
			t = elem.Type().Field(i).Tag.Get("json")
			if t == "" {
				t = elem.Type().Field(i).Name
			}
		}
		tags = append(tags, t)
	}
	return tags
}

// kinds 反射 返回结构体对象的 kinds 数组
func kinds(objptr interface{}) []string {
	var kinds []string
	elem := reflect.ValueOf(objptr).Elem()
	// 判断第一个元素是否为匿名字段
	if elem.Type().Field(0).Anonymous {
		elem = elem.Field(0)
	}
	for i, flen := 0, elem.Type().NumField(); i < flen; i++ {
		switch elem.Field(i).Type().String() {
		case "bool":
			kinds = append(kinds, "BOOLEAN")
		case "int8":
			kinds = append(kinds, "TINYINT")
		case "uint8", "byte":
			kinds = append(kinds, "UNSIGNED TINYINT")
		case "int16":
			kinds = append(kinds, "SMALLINT")
		case "uint16":
			kinds = append(kinds, "UNSIGNED SMALLINT")
		case "int32":
			kinds = append(kinds, "INT")
		case "uint32":
			kinds = append(kinds, "UNSIGNED INT")
		case "int64":
			kinds = append(kinds, "BIGINT")
		case "uint64":
			kinds = append(kinds, "UNSIGNED BIGINT")
		default:
			kinds = append(kinds, "TEXT")
		}
	}
	return kinds
}

// values 反射 返回结构体对象的 values 数组
func values(objptr interface{}) []interface{} {
	var values []interface{}
	elem := reflect.ValueOf(objptr).Elem()
	for i, flen := 0, elem.Type().NumField(); i < flen; i++ {
		if elem.Field(i).Type() == reflect.SliceOf(reflect.TypeOf("")) { // []string
			values = append(values, elem.Field(i).Index(0).Interface()) // string
			continue
		}
		values = append(values, elem.Field(i).Interface())
	}
	return values
}

// addrs 反射 返回结构体对象的 addrs 数组
func addrs(objptr interface{}) []interface{} {
	var addrs []interface{}
	elem := reflect.ValueOf(objptr).Elem()
	for i, flen := 0, elem.Type().NumField(); i < flen; i++ {
		if elem.Field(i).Type() == reflect.SliceOf(reflect.TypeOf("")) { // []string
			s := reflect.ValueOf(make([]string, 1))
			elem.Field(i).Set(s)
			addrs = append(addrs, s.Index(0).Addr().Interface()) // string
			continue
		}
		addrs = append(addrs, elem.Field(i).Addr().Interface())
	}
	return addrs
}
```

