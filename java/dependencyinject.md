# Spring 依赖注入

在讲DI之前，先要了解一下IOC，其核心是容器代替人工创建好对象，需要使用的时候自助去拿即可。

## 基于注解的注入方式

基于注解的注入方式通常有3种：

- 基于属性注入

```java
@Service
public class UserService {
    @Autowired
    private Wolf1Bean wolf1Bean;//通过属性注入
}
```

- 基于 setter 方法注入

```java
@Service
public class UserService {
    private Wolf3Bean wolf3Bean;
    
    @Autowired  //通过setter方法实现注入
    public void setWolf3Bean(Wolf3Bean wolf3Bean) {
        this.wolf3Bean = wolf3Bean;
    }
}
```

- 基于构造器注入

当两个类属于强关联时，我们也可以通过构造器的方式来实现注入

```java
@Service
public class UserService {
  private Wolf2Bean wolf2Bean;
    
     @Autowired //通过构造器注入
    public UserService(Wolf2Bean wolf2Bean) {
        this.wolf2Bean = wolf2Bean;
    }
}
```

```java
@RequiredArgsConstructor
public class UserDaoImpl{
	private final User user;
}
```

## 接口注入

假设一个接口有多个实现类的情况，Spring在注入可能会报错，无法确认到底用哪一个。

解决思路有：

1. 通过配置文件和`@ConditionalOnProperty` 实现：

```java
@Component
@ConditionalOnProperty(name = "lonely.wolf",havingValue = "test1")
public class Wolf1Bean implements IWolf{
}
```

此时配置文件中需要配置`lonely.wolf=test1` 

2. 其他@Condition 条件注解

除了上面的配置文件条件，还可以通过其他类似的条件注解，如：

- @ConditionalOnBean：当存在某一个 Bean 时，初始化此类到容器。

- @ConditionalOnClass：当存在某一个类时，初始化此类的容器。

- @ConditionalOnMissingBean：当不存在某一个 Bean 时，初始化此类到容器。

- @ConditionalOnMissingClass：当不存在某一个类时，初始化此类到容器。

3. 通过`@Resource` 注解动态获取

```java
@Component
public class InterfaceInject {
    @Resource(name = "wolf1Bean")
    private IWolf iWolf;
}
```

此时只会注入BeanName为wolf1Bean的实现类。

4. 通过集合一次性注入接口的所有实现类：

```java
@Component
public class InterfaceInject {
    @Autowired
    List<IWolf> list;

    @Autowired
    private Map<String,IWolf> map;
}
```

上面的两种形式都会将 IWolf 中所有的实现类注入集合中。如果使用的是 List 集合，那么我们可以取出来再通过 instanceof 关键字来判定类型；而通过 Map 集合注入的话，Spring 会将 Bean 的名称（默认类名首字母小写）作为 key 来存储，这样我们就可以在需要的时候动态获取自己想要的实现类。

5. `@Primary` 优先注入

```java
@Component
@Primary
public class Wolf1Bean implements IWolf{
}
```

## @AutoWired 和 @Resource 的区别

`@Autowrite` ：通过类型去注入，可以用于构造器和参数注入。当我们注入接口时，其所有的实现类都属于同一个类型，所以就没办法知道选择哪一个实现类来注入。(byType)

`@Resource` ：默认通过名字注入，不能用于构造器和参数注入。如果通过名字找不到唯一的 Bean，则会通过类型去查找。可以通过指定 name 或者 type 来确定唯一的实现.(byName)

`@Qualifier` 注解是用来标识合格者，当 `@Autowrite` 和 `@Qualifier` 一起使用时，就相当于是通过名字来确定唯一.

为什么需要`@Qualifier` 注解？

因为`@Resource` 注解不能用在参数中，所以这时候就需要使用 `@Qualifier` 注解来确认唯一实现了

```java
@Component
public class InterfaceInject2 {
    @Bean
    public MyElement test(@Qualifier("wolf1Bean") IWolf iWolf){
        return new MyElement();
    }
}
```

## 各种DI方式的优缺点

- 构造器注入：强依赖性（即必须使用此依赖），不变性（各依赖不会经常变动）
- Setter注入：可选（没有此依赖也可以工作），可变（依赖会经常变动）
- Field注入：大多数情况下尽量少使用字段注入，一定要使用的话， @Resource相对@Autowired对IoC容器的耦合更低

## 属性注入存在的问题（为何不推荐使用@AutoWired进行注入）

- 不能像构造器一样注入不可变的对象
- 依赖对外部不可见，外界可以看到构造器和setter，无法看到私有字段
- 组件与ioc容器紧耦合，单元测试也必须使用ioc容器
- 依赖过多时不够明显

NPE：

```java
@Autowired
private User user;

private String company;

public UserDaoImpl(){
    this.company = user.getCompany();
}
```

编译过程不会报错，运行时会报NPE

因为Java 在初始化一个类时，是按照静态变量或静态语句块 –> 实例变量或初始化语句块 –> 构造方法 -> @Autowired 的顺序。所以在执行这个类的构造方法时，user对象尚未被注入，它的值还是 null。


# 参考资料

1. [最全的Spring依赖注入方式，你都会了吗？](https://juejin.cn/post/7069564336468918280)
2. [【源码分析】被同事代码坑后！深入理解Spring依赖注入！发现问题并不简单！](https://www.bilibili.com/video/BV1HF411c7GX?spm_id_from=333.999.0.0&vd_source=c7bea6fb9f92988f548051c4ed7de1e7)
3. [Idea不推荐使用@Autowired进行Field注入的原因](https://juejin.cn/post/7080441168462348319)
4. [Spring为啥不推荐使用@Autowired注解？](https://juejin.cn/post/7023618746501562399)