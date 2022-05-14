1. 栈

   和python语法类似

```
result := make([]int,0)
// 入栈
result = append(result,x)
// 出栈 切片包括头不包括尾,[0,n)
top := result[len(result)-1]
result = result[:len(result)-1]
```

2. 队列

```
result := make([]int,0)
// 入队
result = append(result,x)
// 出队
v:= result[0]
result = result[1:]
```

3. 单调栈
以递增为例子,保证遍历的时候,入栈的元素成递增排序

```
s := []int{1,4,3,2,5}
result := make([]int,0)
for i:=0;i<len(s);i++{
   for len(result) > 0 && result[len(result)-1] > s[i]{
      result = result[len(result)-1]
   } 
   result = append(result,s[i])
} 
```

4. 优先队列
5. 堆