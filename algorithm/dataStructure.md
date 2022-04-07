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

3. 优先队列
4. 堆