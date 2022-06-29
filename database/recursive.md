# 递归查询

## oracle

oracle使用`start with ... connect by prior` 实现递归查询

模板：

```
SELECT * FROM TABLE WHERE 条件3 START WITH 条件1 CONNECT BY 条件2;
```

`start with` :设置起点，用来限制第一层的数据，或者叫根节点数据.以这部分数据为基础来查找第二层数据，然后以第二层数据查找第三层数据以此类推。省略后默认以全部行为起点。

`connect by` :用来指明在查找数据时以怎样的一种关系去查找；比如说查找第二层的数据时用第一层数据某个字段进行匹配，如果这个条件成立那么查找出来的数据就是第二层数据，同理往下递归匹配。

`prior`: 表示上一层级的标识符。经常用来对下一层级的数据进行限制。不可以接伪列。

`prior` 的用法:

```sql
-- PRIOR在子节点前面，向下递归，查找对应的子节点
 SELECT * FROM tab_connect_by A
  START WITH A.PARENT = '15' 
CONNECT BY PRIOR A.CHILD = A.PARENT;

-- PRIOR在父节点前面，向上递归，查找对应的父节点
 SELECT * FROM tab_connect_by A
  START WITH A.PARENT = '15' 
CONNECT BY PRIOR A.PARENT = A.CHILD;
```

[表结构详见](https://www.cnblogs.com/Faith-zhang/p/16179136.html)



## MySQL

[MySQL递归查询 三种实现方式](https://blog.csdn.net/xubenxismile/article/details/107662209)

第三种方式在实现时如果要去除自己不能用入参字段。

```
with recursive t as (
    select *
    from a
    where a.accountid = :accountId
    union all
    select a.*
    from a
    right join t on a.urid = t.parentid
    where a.urid is not null
),
temp as (
   select urid from a where a.accountid = :accountId
)
select acc.*, t.urid rootId, t.name rootName
from t left join acc on t.accountid = acc.urid
where t.urid <> (select urid from temp)
```