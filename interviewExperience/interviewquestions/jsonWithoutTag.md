# json包里使用的时候，结构体里的变量不加tag能不能正常转成json里的字段？

答: 

- 如果变量首字母小写，则为`private` 。无论如何不能转，因为取不到反射信息。
- 如果变量首字母大写，则为`public`
    - 不加tag，可以正常转为json里的字段，json内字段名跟结构体内字段原名一致。
    - 加了tag，从struct转json的时候，json的字段名就是tag里的字段名，原字段名已经没用。

举例说明:

```go
package main
import (
    "encoding/json"
    "fmt"
)
type J struct {
    a string             //小写无tag
    b string `json:"B"`  //小写+tag
    C string             //大写无tag
    D string `json:"DD"` //大写+tag
}
func main() {
    j := J {
      a: "1",
      b: "2",
      C: "3",
      D: "4",
    }
    fmt.Printf("转为json前j结构体的内容 = %+v\n", j)
    jsonInfo, _ := json.Marshal(j)
    fmt.Printf("转为json后的内容 = %+v\n", string(jsonInfo))
}
```

结果:

```
转为json前j结构体的内容 = {a:1 b:2 C:3 D:4}
转为json后的内容 = {"C":"3","DD":"4"} 
```

[来源](https://mp.weixin.qq.com/s?__biz=Mzg5NDY2MDk4Mw==&mid=2247486362&idx=1&sn=bc169a30fe904b24ebfbd3feedaa5556&source=41#wechat_redirect)