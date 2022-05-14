我打算使用plantuml把以前用powerdesigner的图重新画出来
# 时序图
题目：
在某在线订房系统中，顾客输入房间套数、房间类型、入住时间、入住天数等信息，系统显示符合要求的房间号；顾客确定预订的房间，系统显示预付订金信息；顾客输入信用卡账号和密码，系统请求银行信用卡系统提供支付服务；银行信用卡系统验证用户信息并返回验证通过和支付成功消息；顾客请求系统打印预订单和收据，系统打印相关资料；预订结束。
**注意：代码中的:一定要用英文的，要不然很多bug。**
代码：

```sequence
Title: 标题：顺序图
顾客 -> 订房系统:输入预定信息
订房系统 --> 顾客:显示符合要求的房间号
loop 房间套数>0
    顾客 -> 订房系统:确定预定的房间
    订房系统 --> 顾客:显示预付订金信息
end
loop 密码错误
    顾客 -> 订房系统:输入信用卡账号和密码
    订房系统 -> 信用卡系统:提交信用卡账号和密码
    信用卡系统 -> 信用卡系统:验证用户信息
    信用卡系统 --> 订房系统: 返回验证通过和支付成功消息
    订房系统 --> 顾客:显示验证通过和支付成功消息
end
顾客 -> 订房系统:提交打印预订单和收据要求的
订房系统 -> 订房系统:打印预订单和收据
订房系统 -> 顾客:显示预定结束
```
示例图：
![image.png](https://cdn.nlark.com/yuque/0/2021/png/22244395/1627541503223-7cc4e883-0226-4343-b9c2-54fdb5ff7011.png#clientId=u9e4d289e-9753-4&from=paste&height=621&id=u3915935d&margin=%5Bobject%20Object%5D&name=image.png&originHeight=621&originWidth=538&originalType=binary&ratio=1&size=39478&status=done&style=none&taskId=u1fee9919-1881-47b2-9b6c-e4d19c04c00&width=538)


# 流程图
题目：
数据库调度流程图
代码：
```java
@startuml
(*) --> 管理系统的调用
--> 权限验证
if 是否有权限 then
    ->[true] 更新数据库
    
else
    ->[false] 拒绝调用
endif
拒绝调用-->(*)
更新数据库-->(*)
@enduml
```
示例图:
![flow-chart2.png](https://cdn.nlark.com/yuque/0/2021/png/22244395/1628766238849-86d80cc1-2e02-4f86-99b7-1587e02842ba.png#clientId=u97d7b4f5-6fdf-4&from=paste&height=345&id=u8cc49899&margin=%5Bobject%20Object%5D&name=flow-chart2.png&originHeight=345&originWidth=311&originalType=binary&ratio=1&size=14424&status=done&style=none&taskId=u78faaac3-9242-44af-adac-54c74e1ba08&width=311)

# 用例图
题目:
某企业为了方便员工用餐，为企业餐厅开发了一个订餐系统（COS：Cafeteria Ordering System），企业员工可通过企业内联网使用该系统。该系统功能描述如下：
(1) 企业的任何员工都可以查看菜单和今日特价；
(2) 系统的顾客是注册到系统的员工，可以在线订餐（以下操作均需先登录）、注册工资支付、修改订餐信息和删除订餐信息，在注册工资支付时需要通过工资系统进行身份验证；
(3) 餐厅员工是特殊的顾客，可以进行备餐（系统记录备餐信息）、生成付费请求和请求送餐，其中对于注册使用工资支付的顾客生成付费请求并发送给工资系统；
(4) 菜单管理员是餐厅员工的一种，可以管理菜单；
(5) 送餐员也是餐厅员工的一种，可以打印送餐说明、记录送餐信息（如送餐时间）以及记录收费（对于没有注册工资支付的顾客，由送餐员收取现金后记录）。
代码:
```java
@startuml
left to right direction
skinparam packageStyle rectangle
skinparam usecase{
    BackgroundColor<<Main>> gold
    BorderColor<<Main>> YellowGreen
    ArrowColor blue
} 
actor 送餐员
actor 菜单管理员
actor 餐厅员工
actor 顾客
actor 员工
actor 工资系统
:餐厅员工:<|-up-送餐员
:餐厅员工:<|-up-菜单管理员
:顾客:<|-up-餐厅员工
:员工:<|-up-顾客
rectangle COS{
    usecase 查看菜单 <<Main>>
    usecase 查看今日特价 <<Main>>
    usecase 注册 <<Main>>
    usecase 登录 <<Main>>
    usecase 在线订餐 <<Main>>
    usecase 注册工资支付 <<Main>>
    usecase 修改订餐信息 <<Main>>
    usecase 删除订餐信息 <<Main>>
    usecase 备餐 <<Main>>
    usecase 生成付费请求 <<Main>>
    usecase 请求送餐 <<Main>>
    usecase 管理菜单 <<Main>>
    usecase 打印送餐说明 <<Main>>
    usecase 记录送餐信息 <<Main>>
    usecase 记录收费 <<Main>>
    usecase 注册工资支付 <<Main>>
    usecase 生成付费请求 <<Main>>

}
:员工:-right->(查看菜单)
:员工:-right->(查看今日特价)
:员工:-right--->(注册)
:顾客:-right->(在线订餐)
:顾客:-right->(注册工资支付)
:顾客:-right->(修改订餐信息)
:顾客:-right->(删除订餐信息) 
:餐厅员工:-right->(备餐)
:餐厅员工:-right->(生成付费请求)
:餐厅员工:-right->(请求送餐)
:菜单管理员:-right->(管理菜单)
:送餐员:-right->(打印送餐说明)
:送餐员:-right->(记录送餐信息)
:送餐员:-right->(记录收费)
:工资系统:-left->(注册工资支付)
:工资系统:-left->(生成付费请求)
(注册).>(登录) : extend
(在线订餐).> (登录) : include
(注册工资支付).> (登录) : include
(修改订餐信息).> (登录) : include
(删除订餐信息).> (登录) : include
(备餐).> (登录) : include
(生成付费请求).> (登录) : include
(请求送餐).> (登录) : include
(管理菜单).> (登录) : include
(打印送餐说明).> (登录) : include
(记录送餐信息).> (登录) : include
(记录收费).> (登录) : include

@enduml
```
示例图:
![diagram1.png](https://cdn.nlark.com/yuque/0/2021/png/22244395/1627546932539-ef1cd787-8ebc-49c0-94cc-5b70473a1840.png#clientId=u9e4d289e-9753-4&from=paste&height=1922&id=ud3d6ea21&margin=%5Bobject%20Object%5D&name=diagram1.png&originHeight=1922&originWidth=545&originalType=binary&ratio=1&size=154118&status=done&style=none&taskId=u2e11b009-124c-4703-93fd-27532db06a0&width=545)
# 状态图
题目:
某销售信息管理系统中销售部员工可以提交订单，刚提交的订单为“初始”状态；系统管理员可以处理订单，如果订单无误，则
修改订单为“备货”状态，否则将订单退还给提交订单的销售部员工修改，员工此时可以取消订单；仓库管理员备货完毕后可将订单状态改为“发货”状态；销售部员工在确认客户已经收到货物后，可将订单改为“关闭”状态。
代码:
```java
@startuml
hide empty description
[*] --> 初始状态:提交订单/Submit
state 初始状态: do/Handle
state 初始状态: do/Modify Status
state 修改状态: do/Modify Status
state 修改状态: exit/Cancel
state 备货状态: do/Modify
state 发货状态: do/Confirm
state 发货状态: do/Close
state 关闭状态
初始状态 --> 修改状态: 处理订单[订单有误]/Handle
初始状态 --> 备货状态: 处理订单[订单无误]/Handle & Modify Status
备货状态 --> 发货状态: 发货[已发货]/Modify Status
发货状态 --> 关闭状态: 确认订单[客户已收到货物]/Confirm & Close
修改状态 --> 初始状态: 修改完毕/Modify Order
修改状态 --> [*]:取消订单/Cancel
关闭状态 --> [*]
@enduml
```
示例图：
![statementDiagram.png](https://cdn.nlark.com/yuque/0/2021/png/22244395/1627608210910-dacaef22-bcbb-4b19-8d43-7a5f4244bd2b.png#clientId=uda9fc5a4-9b9a-4&from=paste&height=690&id=u3900fd40&margin=%5Bobject%20Object%5D&name=statementDiagram.png&originHeight=690&originWidth=652&originalType=binary&ratio=1&size=37402&status=done&style=none&taskId=u4810dcc2-73db-424b-96c4-f663b08f5d0&width=652)
# 组件图
题目：
Help.h是一个头文件，被Calculate.cpp和Filter.cpp引用，而Calculate.cpp又引用了Filter.cpp。此外，Filter.cpp还引用了头文件FileReader.h，Display.cpp又依赖于Calculate.cpp的运算结果
代码：
```java
@startuml
component Display.cpp
component Calculate.cpp
component Help.h
component Filter.cpp
component FileReader.h
() interface_1
Display.cpp -- interface_1
interface_1 -- Calculate.cpp
Calculate.cpp ..> Help.h
Calculate.cpp ..> Filter.cpp
Filter.cpp ..> Help.h
Filter.cpp ..> FileReader.h
@enduml
```
示例图：
![component-diagram.png](https://cdn.nlark.com/yuque/0/2021/png/22244395/1627609409284-8e0f4644-56a9-45b5-8833-64221014299a.png#clientId=uda9fc5a4-9b9a-4&from=paste&height=520&id=uc5bf4cb7&margin=%5Bobject%20Object%5D&name=component-diagram.png&originHeight=520&originWidth=257&originalType=binary&ratio=1&size=9955&status=done&style=none&taskId=u0a0382a2-1b21-4ee3-b2bf-d6a598ab97b&width=257)
# 类图
题目：
集合类的关系。
代码：
```java
@startuml
interface Iterable{
    Iterator() : Iterator<T>
    forEach(Consumer<? super T> action) : void
    spliterator() : Spliterator<T>
}
interface Collection{
    add(Object o) : boolean
    addAll(Collection c): boolean
    clear() : void
    remove(Object o):boolean
    removeAll(Collection c):boolean
    contains(Object o): boolean
    contains(Collection c): boolean
    isEmpty() : boolean
    size() : int
    retainAll(Collection c):boolean
    toArray(): Object[]
}
interface List{
    get(int index): E 
    set(int index,E element):E 
    indexOf(Object o):int
    lastIndexOf(Object o):int 
    sort(Comparator<? super E> c):void
    subList(int fromIndex,int toIndex):List<E>
}
interface Queue{
    offer(E e): boolean
    poll(): E 
    element(): E
    peek(): E
}
interface Set
interface Deque{
    offerLast(E e):boolean
    offerFirst(E e):boolean
    addLast(E e):void
    addFirst(E e):void
    removeFirst():E 
    removeLast():E 
    pollFirst():E 
    pollLast():E 
    getFirst():E 
    getLast():E 
    peekFirst():E 
    peekLast():E 
    pop():E 
    push(E e):void
}
interface SortedSet{
    comparator():Comparator<? super E>
    subSet(E fromElement,E toElement):SorteddSet<E>
    headSet(E toElement):SortedSet<E>
    tailSet(E fromElement):SortedSet<E>
    first():E
    last():E
}
class ArrayList
class LinkedList
class Vector
class Stack
class ArrayDeque
class PriorityQueue
class HashSet
class LinkedHashSet
class TreeSet
Iterable <|-- Collection
Collection <|-- List
Collection <|-- Queue 
Collection <|-- Set 
List <|.. ArrayList 
List <|.. LinkedList
List <|.. Vector
Vector <|-- Stack
Queue <|-- Deque
Deque <|.. LinkedList
Deque <|.. ArrayDeque
Queue <|.. PriorityQueue
Set <|-- SortedSet
SortedSet <|.. TreeSet
Set <|.. HashSet
Set <|.. LinkedHashSet
@enduml
```
示例图:
![class-diagram.png](https://cdn.nlark.com/yuque/0/2021/png/22244395/1627874905259-f21dfb74-c83b-42fe-9729-cf9eeffe6b6d.png#clientId=ub9b3ef1c-77e6-4&from=paste&height=1010&id=ubab0932b&margin=%5Bobject%20Object%5D&name=class-diagram.png&originHeight=1010&originWidth=1128&originalType=binary&ratio=1&size=74178&status=done&style=none&taskId=uc4575d48-d0f8-49e6-afb0-216da015fb6&width=1128)
# 思维导图
题目:
io流思维导图
代码:
```java
@startmindmap
* I/O
** 字节流
*** InputStream
**** ByteArrayInputStream
**** PipedInputStream
**** FilterInputStream
***** BufferedInputStream
***** DataInputStream
***** PushbackInputStream
**** FileInputStream 
**** ObjectInputStream 
*** OutputStream
**** ByteArrayOutputStream
**** PipedOutputStream
**** FilterOutputStream
***** BufferedOutputStream
***** DataOutputStream
***** PrintStream
**** FileOutputStream 
**** ObjectOutputStream
** 字符流
*** Reader 
**** CharArrayReader
**** PipedReader
**** FilterReader
***** PushbackReader 
**** BufferedReader 
**** InputStreamReader
***** FileReader
*** Writer
**** CharArrayWriter
**** PipedWriter
**** FilterWriter 
**** BufferedWriter 
**** OutputStreamWriter
***** FileWriter 
**** PrintWriter
@endmindmap
```
示例图:
![startmindmap.png](https://cdn.nlark.com/yuque/0/2021/png/22244395/1628768442625-f3a060fe-d95c-4f18-a12f-35e1e74061ec.png#clientId=u97d7b4f5-6fdf-4&from=paste&height=1262&id=u4e8662a5&margin=%5Bobject%20Object%5D&name=startmindmap.png&originHeight=1262&originWidth=738&originalType=binary&ratio=1&size=84713&status=done&style=none&taskId=ucf296838-6b73-4ca4-b137-5eb87c64e3e&width=738)
