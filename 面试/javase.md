## java的基本类型有哪些？分别占多少字节？

类型 | 字节
----|-----
byte | 1 = $2^8$
short | 2 = $2^{16}$
int (默认) | 4 = $2^{32}$
long (L/l) | 8 = $2^{64}$
-----|------
float (F/f) | 4
double (默认) | 8
char | 2
boolean | 1

## 堆和栈存放什么？
堆存放对象，无论是全局的还是局部的；存放成员变量。

栈存放局部变量，还会记录方法运行的栈帧。

## 值传递和引用传递
java只有值传递，无论是基础数据类型还是对象，java都会复制一份再传入方法，其中对象复制的是地址。

那么这样与引用传递有什么区别呢？

引用传递的做法可以改变传入的形参。如下例，java无法改变person的值，而使用引用传递的编程语言可以。

```java
Person person = new Person();
change(person);
public void change(Person newPerson){
	newPerson = new Person();
}
```

## 创建对象的方式有哪些？
1. 用 new 关键字创造
2. 用克隆方法创造
3. 用反射机制创造
4. 可以用IO流创造对象，也就是 ObjectInputStream 和 ObjecOutputStream

## java特性
封装继承多态。

1. 封装：将对象封装成类，只对外提供接口。
2. 继承：子类继承父类的属性和方法，实现功能复用。子类无法继承父类的私有属性，但是可以通过调用父类的方法，使用该私有属性。子类无法继承私有方法。
3. 多态：可以通过父类调用子类的方法，当程序运行时才能确定是哪个子类。多态的必要条件：继承和方法重写（overriding，覆盖）。

## 抽象类和接口有什么区别
java代码规范方面：

1. 接口多实现，抽象类单继承
2. 接口和抽象类都有抽象方法，但是接口可以有 default 方法，抽象类可以有具体方法
3. 接口只能声明常量，抽象类可以声明变量

目的不同：

1. 接口的目的是定义行为
2. 抽象类的目的是代码复用

## 重载和重写的区别？
重载指的是在一个类里面，可以有同名方法，但是方法的参数列表不同。返回值不同不能算是重载。

方法重写的前提是继承，子类可以有父类的同名方法，并且参数列表也一样，但是具体的实现不同。

## static和final的区别？
static 是静态的意思，它可以修饰代码块、内部类、方法和变量。静态代码块只会在加载类的 class 文件时运行，静态变量代表在内存中只能有一份，被 static 修饰的内部类和方法代表可以通过类名直接调用，。

final 可以修饰类、方法和变量。修饰类代表这个类不能被继承；修饰方法代表这个方法不能被重写；修饰变量代表这个变量不能被重新赋值。

## final有哪些用法？
1. 修饰类表示不可继承
2. 修饰方法表示不可被重写
3. 修饰变量表示赋值一次之后不能被修改

## finalize的理解
它是 Object 的一个方法，子类如果实现它，在垃圾回收的时候，就会调用它。不过它的性能非常不好，java9 之后已经被废弃了。

## 怎么阻止一个对象序列化？
加 transient 关键字。

## 运行时异常(runtime exception)和受检异常(checked exception)的区别
1. 运行时异常是RuntimeException的子类，不要求必须处理这种异常。运行时异常应该在写代码时就应该可以检测到并可以避免，这意味着只要代码写得好，就不会出现运行时异常。
2. 受检异常是Exception的子类（不包括RuntimeException），要求必须处理这种异常（要么 `catch`，要么 `throw`），否则不能通过编译

## Error和Exception有什么区别？
Error是系统错误，不用指望程序能处理这种错误。Exception是逻辑错误，必须处理这种异常，否则无法编译。

## s=s+1和s+=1
```java
short s = 1;
s = s + 1; // A
s += 1; // B  s = (short) s + 1
```

A 会报错，因为 1 是 int 型，运算结果也是 int 型（结果以范围大的为准），需要先强转才能赋值给 s。

B 不会报错，因为 `+=` 运算符号隐含了强转。


## 构造器能否被重写？
不能。因为重写的前提是继承，构造器不能被继承。而且子类的构造器名称与父类的不同。

构造器可以被重载。显然，一个类可以有多个构造器。

## 两个对象值相同，hashcode是否可以不同？
不行。因为java规范规定，如果两个对象（x.equals(y) == true），那么hashcode必定相同。详见 `hashCode()` 源码中的注释。

`hashCode()` 具有以下三条协议：

1. 在 java 程序运行期间，只要 `equals()` 中比对的信息，无论调用同一个对象几次，`hashCode()` 一定会返回同一个整型（interger）。
2. 根据 `equals()`，如果两个对象相等，那么这两个对象调用 `hashCode()` 必须产生同一个整型结果。
3. 根据 `equals()`，如果两个对象不相等，那么这两个对象调用 `hashCode()` 无需产生不同的整型结果。然而，程序员应当注意，为不相等的对象生成不同的整数结果可能会提高哈希表的性能。

## 为什么不能根据返回类型来区分重载？
可能跟多态有关。一个方法可以返回子类，也可以返回父类。根据java规范，可以用父类接收子类。现在，如果统一用父类接收两个方法的返回值，那么究竟该调用哪个方法呢？

此外，如果一个方法没有返回值，另一个同名方法有返回值。在调用时，不用属性接收方法的返回结果。那么究竟调用的是哪个方法呢？

## 静态方法是否能被重写？
静态方法无法被重写。

当子类中拥有与父类中同名的静态方法时，父类方法会被隐藏，而不是被重写。

这样，静态方法就没有多态的特性。例如，下面的代码 `obj.test();` 打印的是“Parent”，而不是“Child”。

```java
public class Parent {
    public static void test(){
        System.out.println("Parent");
    }

    public static void main(String[] args) {
        Parent obj = new Child();
        obj.test();
    }
}

class Child extends Parent{
    public static void test(){
        System.out.println("Child");
    }
}
```

## 怎么实现浅克隆和深克隆？
1. 浅克隆：实现 `Cloneable` 接口，然后调用 `clone()`。
2. 深克隆：实现 `Serializable` 接口，然后使用 `ObjectInputStream` 和 `ObjectOutputStream` 实现深克隆。

## String是否可以被继承？为什么要用final修饰String？
不可以。不知道。

## 抽象类能用final吗？
不能，因为抽象类就是让人继承的。final 却说不让你继承。这不是矛盾。

## String s = new String(“xyz”);创建了几个字符串对象？
[请别再拿“String s = new String("xyz");创建了多少个String实例”来面试了吧](https://www.iteye.com/blog/rednaxelafx-774673)

两个。一个是字符串常量“xyz”；另一个是 `new` 出来字符串对象。

## String s = new String(“xyz”);创建了几个对象？
三个。**字符串常量对象**“xyz”、`new` 出来**字符串对象**、**引用对象** s。

## String、StringBuffer和StringBuilder的区别是什么？
String 是不可变字符串。

StringBuffer 和 StringBuilder 是可变字符串。不过 StringBuffer 是线程安全的。

## ==和equals的区别
==如果比较的是基本数据类型，那么就比较具体的值；如果比较的是对象，那么就比较对象的地址。

equals 方法默认使用 == 号，但是可以重写，可以比较对象的具体属性。

## 静态变量、成员变量和局部变量分别存储在jvm的哪里？
静态变量存在方法区，成员变量（类中定义的变量）存在堆中，局部变量（方法中定义的变量）存在栈中。

1. class 文件加载在方法区，静态变量随着 class 文件的加载而加载，因此它在方法区。
2. 对象存在堆中，因此成员变量也在堆中。
3. 方法需要栈来控制顺序，因此局部变量也在栈中。

变量如上所述。而对象存在于堆中。`int[] arr = new int[1000];`，这里的 `arr` 就是变量，数组是对象。

[Where are static methods and static variables stored in Java?](https://stackoverflow.com/questions/8387989/where-are-static-methods-and-static-variables-stored-in-java)

java 8 以前，方法区存储 1）Class level data (meta-data)；2）interned strings；3）static variables。

从 java 8 开始，interned strings 和静态变量存在堆中了。

## 怎么将GB2312编码的字符串转为ISO-8859-1？
做法是：

```java
String iso = new String(str.getBytes("gb2312"), "iso-8859-1");
```

然而这其实没那么简单。

网上有人介绍了 [ASCII、Unicode、GBK和UTF-8字符编码的区别联系](https://www.cnblogs.com/clarkzheng/archive/2007/07/24/829506.html) 。这个链接中的文章转载于 2007 年，原文已经不可考。

## char能不能储存一个汉字？
可以。

java char 类型占2个字节、16位，并且使用的是 utf-16 编码规则。在 utf-16 编码下大部分汉字是2个字节。正好够用。

在 utf-8 编码下大部分汉字是 3 个字节。

[What is the difference between UTF-8 and Unicode?](https://stackoverflow.com/questions/643694/what-is-the-difference-between-utf-8-and-unicode)

上面的链接有一个回答用一个汉字解释了 utf-8 的编码规则。它由 3 个字节组成。

举个例子，下面的代码会打印出 `[-25, -120, -79]` 数组，每个数字用二进制表示（8位）：11100111，10001000，10110001

```java
public class UnicodeTest {
    public static void main(String[] args) {
        String love="爱";
        System.out.println(Arrays.toString(love.getBytes()));
        for(byte i:love.getBytes()){
            System.out.println(Integer.toBinaryString(i).substring(32 - 8));
        }
    }
}
```

根据 utf-8 的编码规则，我们得到“爱”的 unicode 二进制编码是 0111001000110001，使用[网站](https://tool.oschina.net/hexconvert)计算得到对应的 16 进制是7231。使用[网站](https://tool.chinaz.com/tools/unicode.aspx)填入“\u7231”，可以得到“爱”。说明流程没有错。

## 内部类可以私有吗？
1. 类的内部类可以私有
2. 接口的内部类只能由 `public` 和 `default` 修饰

## 重写equals，为什么还要重写hashcode？
例如，重写 equals 方法，比对类中的一些属性。如果不重写 hashCode，那么 hashCode 返回的还是对象的地址。

这样的话，两个相等的对象，就会得到不同的 hashcode 了。

-----

因为这与 hashCode 的规范有关。它规定如果两个对象相等，那么哈希值也必须相等。

如果补不重写 hashcode 可能导致两个对象相同，但是 hashcode 却不同。

举个例子，如果有一个学生类，equals 方法比对的是姓名和性别，hashCode 方法返回的是姓名和性别对应的哈希值之和。那么同一个姓名但是不同性别的学生，它们的哈希值肯定是不同的。

如果子类重写了 equals 方法，但是没有重写 hashCode 方法。equals 中比对是姓名和班级，那么同班、同名但不同性别的学生应该算作是同一个人，但是父类的 hashCode 却反回了不同的值。

也就是说：相等的对象，返回了不同的哈希值。这就不符合 hashCode 的规定了。

这应该与 java 规范有关。

java 规定如果两个对象相同，也就是 equals 方法返回 true，那么它们的 hashcode 也相同。两个方法是配套的。

如果重写了 equals 方法，使用某些数据进行比对，那么 hashcode 也要同步。

例如学生类重写 equals 方法，需要比对学号、姓名和性别三个属性，hashcode 方法必须要加入这三个属性。hashcode 默认实现通常是将对象地址转为整数（来自 hashcode 方法源码上的注释的最后一行），不重写肯定不行。

**总而言之，需要让 equals 方法和 hashcode 方法里面所使用的数据是同步的。**

## String的哈希算法是怎么样的？
数组中的第一个数乘 31 的 n-1 次方，加上第二个数乘 31 的 n-2 次方，

## ____泛型擦除

## JVM内存结构
1. **方法区**存放类的信息，例如类的名字，类的继承关系，类的成员变量，类的方法代码。
2. **堆**里面存放实例对象，不管是成员变量还是局部变量的对象都在堆里。
3. **虚拟机栈**存放引用变量，或者基本类型变量，包括基本类型变量所指的值。
4. **本地方法栈**跟虚拟机栈差不多，只不过用于处理本地方法。
5. **程序计数器**用来记录当前所执行字节码的行号，就是执行到哪行。
6. 解释器将字节码解释为机器码
7. 即时编译器负责处理热点代码，就是哪种被经常调用的方法。防止解释器反复地去解释代码。

## 内存溢出
除了程序计数器，其它都会发生内存溢出。分为：

`OutOfMemoryError`：

1. 堆内存耗尽：对象一直被使用，不能被垃圾回收
2. 方法区内存耗尽：加载的类越来越多。很多框架都会在运行期间动态产生新的类。不过这种情况出现的比较少，因为默认情况下，方法区内存没有设置上限。
3. 虚拟机栈内存耗尽：每个线程最多会占用 1M 内存，随着线程越来越多，却又长时间运行不销毁。（线程运行时会在虚拟机栈中分配一点内存）

`StackOverflowError`：

1. 虚拟机栈内部，方法调用次数过多。每次调用，会把线程的 1M 内存分出一点给一个方法，如果一直递归调用，就会把 1M 内存耗尽。

## 方法区、永久代和元空间之间的关系
1. 方法区是 JVM 规范中的一种定义，*用来存储类的元数据、方法字节码、即时编译器需要的信息等*。
2. 永久代是 Hotspot 虚拟机对 JVM 规范的实现（1.8 以前）
3. 元空间是 Hotspot 虚拟机对 JVM 规范的实现（1.8 以后），使用**本地内存**作为存储空间

## 什么时候将类信息放入方法区（元空间）？什么时候销毁？
在加载类的时候，会创建 Class 对象，并且把类信息（类的元数据）放入方法区。

通过在堆中的 Class 对象可以访问元空间中对应的类信息。

在类加载器被回收的时候，才会把在方法区中相应的类信息回收。如果一个类只是不用了，那么它对应的类信息是不会被回收的。

一般来讲，系统的类加载器不会被回收，只有自定义的类加载器才可能被回收。

## JVM参数
-Xss 每个线程在虚拟机栈中分配的内存，如果是 linux 默认是 1M

**以下都是堆内存的设置**

-Xmx 虚拟机最大内存大小 （memory maximum）

-Xms 虚拟机最小内存大小 （memory startup）

-Xmn 虚拟机中新生代的内存大小 （ memory nursery）

-XX:SurvivorRatio

新生代（new）分为 eden 和 Survivor，Survivor 又分为 from 和 to。from 和 to 的内存总是相等的。

SurvivorRatio 就是说 eden:from 的比例。-XX:SurvivorRatio=3 就是 3:1。默认是 8:1。

-XX:NewRatio 指的是老年代和新生代的比例。-XX:NewRatio=2 就是 2:1，老年代占两份，新生代占一份。

**以下都是元空间的设置**

-XX:MaxMetaspaceSize 最大元空间大小，默认不设置，也就是没有限制

-XX:CompressedClassSpaceSize 元空间中 class space 的内存大小，剩余内存分配给 non-class space。

## 垃圾回收算法

## java生成字节码
1. 使用 java -c 命令将 java 源代码编译成字节码（Java Class）
2. 使用 java 命令执行字节码，它会创建一个 java 虚拟机，并且启动 main 线程（主线程）。*主线程的内存被放在虚拟机栈里面（JVM Stacks），其它线程的内存其实也会被放在这里面*。
3. 当虚拟机发现没见过的类，就使用类加载器加载类，也就是读取 .class 文件中的类信息，最后生成一个 Class 对象。类的信息被存在方法区里面。

## java类加载过程
类的生命周期分为：加载、连接和初始化。

1. 首先是加载：将类的字节码加载到方法区，并且在堆里面创建对应 Class 对象。如果它的父类还没加载，会先去加载父类。*加载是懒惰执行，就是说只有在真正用到类的时候，才会去加载。*
2. 然后是链接：一共分为三步，分别是验证、准备和解析。
	- 验证主要检查类是否符合字节码规范，对字节码的安全性、合法性进行检查
	- 准备主要是为静态变量分配空间，设置默认值。赋值语句在初始化步骤执行，此处只是设置默认值。
	- 解析就是将常量池中的符号引用解析为直接引用（符号引用说白了就是类名的字符串，直接引用就是类的引用地址）[虚拟机-24-类加载_将符号引用变为直接引用](https://www.bilibili.com/video/BV15b4y117RJ?p=124)
3. 初始化：执行静态代码块，为静态变量（非 final）赋值。初始化是懒惰执行
4. *最后就是分别是使用和卸载。可不说*

## 什么是双亲委派机制？
[Java 面试题：聊下什么是双亲委派机制](https://zhuanlan.zhihu.com/p/367609494)

[【JVM】Java双亲委派、类加载器这块算是玩明白了](https://www.bilibili.com/video/BV1X5411K7cw)

java 有三种类加载器，分别是应用程序类加载器、扩展类加载器和启动类加载器，它们具有上下级关系。

JVM 在加载类的时候，会首先委托父类加载器取加载类。

如果加载失败，那么就自己去加载。

如果自己也加载失败，那么就抛出 `ClassNotFoundException`。

-----

就是说，JVM 在加载类的时候，如果类加载器有父加载器，那么会首先委托父加载器加载。

最后会委托给 Bootstrap ClassLoader。它负责加载 lib 目录中的类。

如果加载失败，也就是说在目录中找不到类，那么就自己去加载。

如果也找不到，那就抛出 `ClassNotFoundException`。

## 为什么要有双亲委派机制？
安全性，就是禁止用户加载一些自定义的类，例如和 java.lang.String，java.lang.System，java.lang.Object 这种同名的类。如果用户用自定义加载器去加载这些类，程序就不知道到底应该用哪个类。

还有一点是为了避免多次加载同一个类。（只有父类加载不到，自己才会去加载）

## 有哪些引用
[Java中的强引用，软引用，弱引用，虚引用有什么用？](https://www.zhihu.com/question/37401125/answer/71807057)

1. 强引用：就是用 new 创建的对象。在强引用不到对象的时候，才会被回收。
2. 软引用：用 SoftReference 创建的对象，`new MySoftReference(new A())`。首次回收时不会释放对象，如果内存不够了，再次回收时，才会释放它。
3. 弱引用：用 WeakReference 创建的对象，`new MyWeakReference(new A())`。只要发生垃圾回收，就会释放对象。
4. 虚引用：用 PhantomReference 创建的对象，`new MyPhantomReference(new A())`。必须配合引用队列使用，在垃圾回收的时候会把对象加入到引用队列里面。其它跟弱引用差不多。

## 单例模式为什么要双重校验？
```java
public class Singleton {
    private volatile static Singleton singleton;
 
    private Singleton() {}
 
    public static Singleton getInstance() {
        if (singleton == null) {
            synchronized (Singleton.class) {
                if (singleton == null) {
                    singleton = new Singleton();
                }
            }
        }
        return singleton;
    }
}
```

假设有多个线程被阻塞在 synchronized 代码块那里，如果没有第二次检验，那么这些线程在获得锁之后会重复地创建单例对象。

## 封装继承多态你怎么理解？
[这样理解面向对象的封装，继承，多态是否正确？](https://www.zhihu.com/question/52767718/answer/143989505)

封装：封装就是明确地标识外部可使用的方法或属性。这样就可以区分内外，类的实现者即使修改了内部的代码，也不影响外部的使用。封装也表明了哪些方法不允许外部调用者修改。

继承和多态是一体的。

继承表明子类可以复用父类的代码，提升复用性。并且，也声明了这个子类是兼容父类的。

多态指的是，调用者在调用方法之后，只有在真正运行的时候才知道方法内部的行为、逻辑是什么。多态的前提就是继承。

多态的好处？

让代码有扩展性，如果有新的子类，只需要重写约定的方法即可，项目中的方法不需要修改。

例如说想要创建一个新的处理器适配器，DispatcherServlet 中的适配过程不需要做修改，只需要实现适配器中的 handle 方法就行了。













