# 基础

## 语言特性

### JDK 和 JRE 有什么区别？

JDK全称是Java Development Kit，提供了编译运行Java程序的各种工具，包括了编译器、JRE和常用的类库。

JRE全称是Java Runtime Environment,是运行java的必要环境。包括了JVM。

### Java是按值调用还是按引用调用？

按值调用是指方法接收调用者提供的值，按引用调用指方法接收调用者提供的变量地址。Java总是按值调用，方法得到的是所有参数值的副本。传递对象时实际上接收的时对象引用的副本。

## 数据类型 

### Java 有哪些基本数据类型？

| 数据类型 | 内存大小                               | 默认值   | 取值范围                    |
| -------- | -------------------------------------- | -------- | --------------------------- |
| byte     | 1 B                                    | (byte)0  | -128 ~ 127                  |
| short    | 2 B                                    | (short)0 | -2^15^ ~ 2^15^-1            |
| int      | 4 B                                    | 0        | -2^31^ ~ 2^31^-1            |
| long     | 8 B                                    | 0L       | -2^63^ ~ 2^63^-1            |
| float    | 4 B                                    | 0.0F     | ±3.4E+38（有效位数 6~7 位） |
| double   | 8 B                                    | 0.0D     | ±1.7E+308（有效位数 15 位） |
| char     | 英文 1B，中文 UTF-8 占 3B，GBK 占 2B。 | '\u0000' | '\u0000' ~ '\uFFFF'         |
| boolean  | 单个变量 4B / 数组 1B                  | false    | true、false                 |

JVM 没有 boolean 赋值的专用字节码指令，`boolean f = false` 就是使用 ICONST_0 即常数 0 赋值。单个 boolean 变量用 int 代替，boolean 数组会编码成 byte 数组。

### java 中的 Math.round(-1.5) 等于多少？

-1.`Math.round(1.5)=2` `Math.round(-1.5)=-1 `

### String是不可变类为什么值可以修改？

String类和成员变量都是final类型的。对一个String类的修改实际上都是创建一个新string对象，再引用该对象。

### 字符串拼接的方式

- 直接使用 `+` 拼接，底层使用`StringBuilder`实现
- `String`类中的 `concat` 方法，底层使用`Arrays.copyOf()` 创建一个新的字符数组，长度为当前字符串+拼接字符串，并将当前字符串的值拷贝到数组中，再使用`System.arraycopy()`将拼接字符串的值也拷贝到数组中，再重新构建新对象
- 使用 `StringBuilder`或`StringBuffer` ，底层和`concat` 类似，但容量不同

### String a = "a" + new String("b")创建了几个对象？

常量和常量拼接仍是常量，只要有变量参与拼接，结果就是变量，存在堆。

使用字面量时只创建一个常量池中的常量，使用new时，如果常量池中没有就新建，再在堆中创建一个对象引用常量池中常量。

故而此处创建了四个对象。常量池中的a和b，堆中的b和ab。

### == 和 equals 的区别是什么？

`==` 的作用是判断两个对象的地址是否相等，即判断两个对象是不是同一个对象。

`equals()`在未被覆盖时作用和`==` 一致。被覆盖后主要判断两个对象的内容是否相等。

### 两个对象的 hashCode()相同，则 equals()也一定为 true，对吗？

不一定。 `hashCode()` 的作用是获取哈希码，用于确定该对象在哈希表中的索引位置。如果两个对象相等，则`hashcode()` 也是相同的，分别调用`equals()` 方法都返回true。
两个对象有相同的hashcode值，它们也不一定相等。 `hashCode()` 的默认行为是对堆上的对象产生独特值。如果没有重写`hashcode()` ，则该class的两个对象无论如何都不会相等。

## 类、接口与继承

### final 在 java 中有什么作用？

final用来修饰类、属性和方法。被final修饰的类不可继承；被final修饰的方法不可以被重写；被final修饰的变量不可以被改变，被final修饰不可变的是变量的引用，而不是引用指向的内容。

### 重载和重写的区别

对编译器来说，方法名称和参数列表组成了一个唯一键，称为方法签名。JVM通过方法签名决定调用哪个方法。

重载时方法名称相同，参数类型个数不同。重载可以在编译时就知道调用哪个方法，因此属于静态绑定。重写是指子类实现接口或继承父类时保持方法签名完全相同，实现不同方法体。

在元空间中，方法表用来保存方法信息。如果子类重写父类的方法，则方法表中的方法引用会指向子类实现。

### 内部类的作用及分类

1. 静态内部类：只加载一次，作用域在包内，可通过 外部类名.内部类名 访问，类内只能访问外部类所有静态属性和方法，不可访问外部类的非静态变量。

```java
public class Outer {

    private static int radius = 1;

    static class StaticInner {
        public void visit() {
            System.out.println("visit outer static  variable:" + radius);
        }
    }
}


// Outer.StaticInner inner = new Outer.StaticInner();
// inner.visit();

```

2. 成员内部类：属于外部类的每个对象，随对象一起加载。不可以定义静态成员和方法，可访问外部类的所有内容。

```java
public class Outer {

    private static  int radius = 1;
    private int count =2;
    
     class Inner {
        public void visit() {
            System.out.println("visit outer static  variable:" + radius);
            System.out.println("visit outer   variable:" + count);
        }
    }
}


//Outer outer = new Outer();
//Outer.Inner inner = outer.new Inner();
//inner.visit();

```

3. 局部内部类：定义在方法内，不能声明访问修饰符，只能定义实例成员变量和实例方法，作用范围仅在声明类的代码块中。定义在实例方法中的局部类可以访问外部类的所有变量和方法，定义在静态方法中的局部类只能访问外部类的静态变量和方法。

```java
public class Outer {

    private  int out_a = 1;
    private static int STATIC_b = 2;

    public void testFunctionClass(){
        int inner_c =3;
        class Inner {
            private void fun(){
                System.out.println(out_a);
                System.out.println(STATIC_b);
                System.out.println(inner_c);
            }
        }
        Inner  inner = new Inner();
        inner.fun();
    }
    public static void testStaticFunctionClass(){
        int d =3;
        class Inner {
            private void fun(){
                // System.out.println(out_a); 编译错误，定义在静态方法中的局部类不可以访问外部类的实例变量
                System.out.println(STATIC_b);
                System.out.println(d);
            }
        }
        Inner  inner = new Inner();
        inner.fun();
    }
}

```

```java
public static void testStaticFunctionClass(){
    class Inner {
    }
    Inner  inner = new Inner();
 }

```

4. 匿名内部类：只用一次的没有名字的类。
   

必须继承一个抽象类或者实现一个接口。

不能定义任何静态成员和静态方法。

当所在的方法的形参需要被匿名内部类使用时，必须声明为 final。

匿名内部类不能是抽象的，它必须要实现继承的类或者实现的接口的所有抽象方法。

```java
public class Outer {

    private void test(final int i) {
        new Service() {
            public void method() {
                for (int j = 0; j < i; j++) {
                    System.out.println("匿名内部类" );
                }
            }
        }.method();
    }
 }
 //匿名内部类必须继承或实现一个已有的接口 
 interface Service{
    void method();
}

```

内部类是一个编译器现象，与虚拟机无关。编译器会把内部类转换成常规的类文件，用 $ 分隔外部类名与内部类名，其中匿名内部类使用数字编号.

### 访问权限控制符有哪些？

| 访问权限控制符 | 本类 | 包内 | 包外子类 | 任何地方 |
| :------------: | :--: | :--: | :------: | :------: |
|     public     |  √   |  √   |    √     |    √     |
|   protected    |  √   |  √   |    √     |    ×     |
|       无       |  √   |  √   |    ×     |    ×     |
|    private     |  √   |  ×   |    ×     |    ×     |

### 接口和抽象类的异同

抽象类对于成员变量没有特殊要求。有构造方法，不能实例化。可以没有抽象方法，但有抽象方法一定是抽象类。只能单继承。

接口的成员变量默认为`public static final` 常量，没有构造方法，不能被实例化。方法默认是`public abstract ` JDK8支持默认/静态方法。JDK9支持私有方法。可以多继承。

### 子类初始化的顺序

- 父类静态代码块和静态变量
- 子类静态代码块和静态变量
- 父类普通代码块和普通变量
- 父类构造方法
- 子类普通代码块和普通变量
- 子类构造方法