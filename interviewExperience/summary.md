# go语言面经

**我打算在牛客、力扣找面经，一个一个记录下来,如果没有楼主没有写答案的话，自己补充答案**

## 优秀面经
1. [各厂三年go面经，已入字节](https://www.nowcoder.com/discuss/662296)


## go语言面经

- rpc微服务框架

远程过程调用（Remote Procedure Call，RPC）是一个计算机通信协议
该协议允许运行于一台计算机的程序调用另一台计算机的子程序，而程序员无需额外地为这个交互作用编程
如果涉及的软件采用面向对象编程，那么远程过程调用亦可称作远程调用或远程方法调用

- mq底层数仓

mq的分类
![](https://pics5.baidu.com/feed/9e3df8dcd100baa14ce94aeb4ff6ba18cafc2e8c.jpeg?token=feb3c3ce537738372bfe41ad1abbb71a)

mq的优势
1. 解耦
2. 异步
3. 削峰
4. 日志处理

mq的劣势
1. 系统的可用性降低
2. 系统的复杂性提高
3. 一致性问题

工作中使用mq进行埋点，把数据输出到神策进行统计

- runtime包里面的方法

runtime.Gosched()
让出CPU时间片，重新等待安排任务

runtime.Goexit()
退出当前协程

runtime.GOMAXPROCS()
确定需要使用多少个OS线程来同时执行Go代码